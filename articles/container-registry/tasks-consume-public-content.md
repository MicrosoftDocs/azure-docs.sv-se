---
title: Aktivitets arbets flöde för att hantera innehåll i offentliga register
description: Skapa ett arbets flöde för automatiserade Azure Container Registry uppgifter för att spåra, hantera och använda innehåll i offentliga bilder i ett privat Azure Container Registry.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349290"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Hur du använder och underhåller offentligt innehåll med Azure Container Registry uppgifter

Den här artikeln innehåller ett exempel arbets flöde i Azure Container Registry som hjälper dig att hantera konsumerande och underhåll av offentligt innehåll:

1. Importera lokala kopior av beroende offentliga avbildningar.
1. Verifiera offentliga avbildningar via säkerhets genomsökning och funktionell testning.
1. Marknadsför avbildningarna till privata register för intern användning.
1. Utlös bas avbildnings uppdateringar för program beroende på offentligt innehåll.
1. Använd [Azure Container Registry uppgifter](container-registry-tasks-overview.md) för att automatisera det här arbets flödet.

Arbets flödet sammanfattas i följande bild:

![Konsumerar delat innehålls arbets flöde](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

Port rummet för gated-import hjälper till att hantera din organisations beroenden för externt hanterade artefakter, till exempel bilder som har källan från offentliga register, inklusive [Docker Hub][docker-hub], [GCR][gcr], [Quay][quay], [GitHub container Registry][ghcr], [Microsoft container Registry][mcr]eller till och med andra [Azure Container-register][acr]. 

Information om de risker som introduceras av beroenden på offentligt innehåll och hur du använder Azure Container Registry för att minimera dem finns i [blogg inlägget][oci-consuming-public-content] för det offentliga innehållet i OCI och [Hantera offentligt innehåll med Azure Container Registry](buffer-gate-public-content.md).

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra den här genom gången. Azure CLI version 2,10 eller senare rekommenderas. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-cli].

## <a name="scenario-overview"></a>Översikt över scenario

![Importera arbets flödes komponenter](./media/tasks-consume-public-content/consuming-public-content-objects.png)

Den här genom gången ställer in:

1. Tre **behållar register**, som representerar:
   * En simulerad [Docker Hub][docker-hub] ( `publicregistry` ) som stöder ändring av bas avbildningen
   * Grup registret ( `contoso` ) för att dela privata avbildningar
   * Delat register för företag/team ( `baseartifacts` ) för importerat offentligt innehåll
1. En **ACR-uppgift** i varje register. Aktiviteterna:  
   1. Bygg en simulerad offentlig `node` avbildning
   1. Importera och validera `node` avbildningen till det delade registret för företag/team
   1. Bygg och distribuera `hello-world` avbildningen
1. **ACR aktivitets definitioner**, inklusive konfigurationer för:
1. En samling **autentiseringsuppgifter för registret**, som pekar mot ett nyckel valv
1. En samling **hemligheter** som är tillgängliga i en `acr-task.yaml` , som pekar mot ett nyckel valv
1. En samling med **konfigurerade värden** som används i en `acr-task.yaml`
1. Ett **Azure Key Vault** för att skydda alla hemligheter
1. En **Azure Container instance** som är värd för `hello-world` build-programmet

## <a name="prerequisites"></a>Förutsättningar

Följande steg konfigurerar värden för resurser som skapas och används i genom gången.

### <a name="set-environment-variables"></a>Ange miljövariabler

Konfigurera variabler som är unika för din miljö. Vi följer bästa praxis för att placera resurser med varaktigt innehåll i sin egen resurs grupp för att minimera oavsiktlig borttagning. Du kan dock placera dem i en enda resurs grupp om du vill.

Exemplen i den här artikeln är formaterade för bash-gränssnittet.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git-databaser och-token

För att simulera din miljö kan du förgrena var och en av följande git-databaser till databaser som du kan hantera. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

Uppdatera sedan följande variabler för dina förgrenade databaser.

Det som `:main` läggs till i slutet av Git-URL: erna representerar databasens standard gren.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

Du behöver en [GitHub-åtkomsttoken (Pat)][git-token] för ACR-uppgifter för att klona och upprätta git-webhookar. Anvisningar för hur du skapar en token med de behörigheter som krävs för en privat lagrings platsen finns i [skapa en GitHub](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)-åtkomsttoken. 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Autentiseringsuppgifter för Docker Hub  
För att undvika begränsnings-och identitets förfrågningar när du hämtar bilder från Docker Hub, skapar du en [Docker Hub-token][docker-hub-tokens]. Ange sedan följande miljövariabler:

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>Skapa register

Med hjälp av Azure CLI-kommandon skapar du tre Premium-nivå behållare register, var och en i sin egen resurs grupp:

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>Skapa nyckel valv och ange hemligheter

Skapa ett nyckel valv:

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

Ange användar namn och token för Docker Hub i nyckel valvet:

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

Ange och verifiera en git-PAT i nyckel valvet:

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Skapa resurs grupp för en Azure Container instance

Den här resurs gruppen används i en senare uppgift när `hello-world` avbildningen distribueras.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>Skapa offentlig `node` bas avbildning

Om du vill simulera `node` avbildningen på Docker Hub skapar du en [ACR-uppgift][acr-task] för att skapa och underhålla den offentliga avbildningen. Med den här inställningen kan du simulera ändringar av `node` avbildningens kvarhållare.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

För att undvika Docker-begränsning kan du lägga till [Docker Hub-autentiseringsuppgifter][docker-hub-tokens] för aktiviteten. Kommandot [ACR Task credentials][acr-task-credentials] kan användas för att skicka Docker-autentiseringsuppgifter till alla register, inklusive Docker Hub.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

Ge aktiviteten åtkomst till läsa värden från nyckel valvet:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

[Aktiviteter kan utlösas][acr-task-triggers] av Git-skrivningar, bas avbildnings uppdateringar, timers eller manuella körningar. 

Kör aktiviteten manuellt för att skapa `node` avbildningen:

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

Lista avbildningen i det simulerade offentliga registret:

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>Skapa `hello-world` avbildningen

Bygg en avbildning baserat på den simulerade offentliga `node` avbildningen `hello-world` .

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>Skapa token för pull-åtkomst till simulerat offentligt register

Skapa en åtkomsttoken till det simulerade offentliga [registret som omfattas][acr-tokens] av `pull` . Ange det sedan i nyckel valvet:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Skapa token för pull-åtkomst med Azure Container Instances

Skapa en [åtkomsttoken till registret som är][acr-tokens] värd för `hello-world` avbildningen, som är begränsad till pull. Ange det sedan i nyckel valvet:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>Skapa uppgift för att skapa och underhålla `hello-world` avbildning

Följande kommando skapar en uppgift från definitionen i `acr-tasks.yaml` i `hello-world` lagrings platsen. Uppgifts stegen bygger `hello-world` avbildningen och distribuerar den sedan till Azure Container instances. Resurs gruppen för Azure Container Instances skapades i ett föregående avsnitt. Genom `az container create` att anropa i uppgiften med en skillnad i `image:tag` , distribueras aktiviteten till samma instans i den här genom gången.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

Lägg till autentiseringsuppgifter till uppgiften för det simulerade offentliga registret:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Ge aktiviteten åtkomst till läsa värden från nyckel valvet:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Ge aktiviteten åtkomst för att skapa och hantera Azure Container Instances genom att bevilja åtkomst till resurs gruppen:

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

När uppgiften har skapats och kon figurer ATS kör du aktiviteten för att skapa och distribuera `hello-world` avbildningen:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

Hämta IP-adressen för den behållare som är värd för avbildningen när den har skapats `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

I webbläsaren går du till IP-adressen för att se det program som körs.

## <a name="update-the-base-image-with-a-questionable-change"></a>Uppdatera bas avbildningen med en "tveksam" ändring

I det här avsnittet simuleras en ändring av bas avbildningen som kan orsaka problem i miljön.

1. Öppna `Dockerfile` i den förgrenade `base-image-node` lagrings platsen.
1. Ändra `BACKGROUND_COLOR` till `Orange` för att simulera ändringen.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

Genomför ändringen och titta efter ACR-aktiviteter för att automatiskt börja skapa.

Titta efter uppgiften att börja köra:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

Du bör slutligen se STATUS `Succeeded` baserat på en UTlösare av `Commit` :

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

Skriv **CTRL + C** om du vill avsluta titta-kommandot och visa loggarna för den senaste körningen:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

När `node` avbildningen är klar kan `watch` ACR-aktiviteter automatiskt börja skapa `hello-world` avbildningen:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

Du bör slutligen se STATUS `Succeeded` baserat på en UTlösare av `Image Update` :

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

Skriv **CTRL + C** om du vill avsluta titta-kommandot och visa loggarna för den senaste körningen:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

När du är klar hämtar du IP-adressen för platsen som är värd för den uppdaterade `hello-world` avbildningen:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Gå till webbplatsen i webbläsaren, som ska ha en orange (tveksam) bakgrund.

### <a name="checking-in"></a>Checkar in

Nu har du skapat en `hello-world` avbildning som skapats automatiskt på git-incheckningar och ändringar av bas `node` avbildningen. I det här exemplet bygger aktiviteten mot en bas avbildning i Azure Container Registry, men alla register som stöds kan användas.

Uppdateringen av bas avbildningen återaktiverar automatiskt uppgiften som körs när `node` bilden uppdateras. Som visas här är det inte alla uppdateringar som önskas.

## <a name="gated-imports-of-public-content"></a>Gated importer av offentligt innehåll

För att förhindra överströms ändringar från bryta kritiska arbets belastningar kan säkerhets genomsökning och funktionella tester läggas till.

I det här avsnittet skapar du en ACR-uppgift för att:

* Bygg en test avbildning
* Kör ett funktionellt test skript `./test.sh` mot test avbildningen
* Om avbildningen har testats importerar du den offentliga avbildningen till **baseimages** -registret

### <a name="add-automation-testing"></a>Lägg till Automation-testning

För att Grinda allt överordnat innehåll implementeras automatiserad testning. I det här exemplet `test.sh` tillhandahålls en som kontrollerar `$BACKGROUND_COLOR` . Om testet Miss lyckas returneras en `EXIT_CODE` av `1` som gör att AKTIVITETS steget ACR Miss lyckas, vilket avslutar uppgifts körningen. Testerna kan utökas i alla former av verktyg, inklusive loggnings resultat. Porten hanteras av ett pass/misslyckande-svar i skriptet som återges här:

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>Uppgift YAML 

Granska `acr-task.yaml` i `import-baseimage-node` lagrings platsen, som utför följande steg:

1. Bygg test bas avbildningen med följande Dockerfile:
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. När du är klar verifierar du avbildningen genom att köra behållaren, som kör `./test.sh`
1. Om du har slutfört kör du import stegen, som är gated med `when: ['validate-base-image']`

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>Skapa uppgift för att importera och testa bas avbildning

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

Lägg till autentiseringsuppgifter till uppgiften för det simulerade offentliga registret:

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

Ge aktiviteten åtkomst till läsa värden från nyckel valvet:

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Kör aktiviteten importera:

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> Om aktiviteten Miss lyckas på grund av `./test.sh: Permission denied` kontrollerar du att skriptet har kör behörigheter och går tillbaka till git-lagrings platsen:
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>Uppdatera `hello-world` avbildningen för att bygga från en `node` avbildning

Skapa en [åtkomsttoken][acr-tokens] för att komma åt registret för bas artefakter som är begränsat till `read` från `node` lagrings platsen. Ange sedan i nyckel valvet:

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

Lägg till autentiseringsuppgifter till **Hello-World-** aktiviteten för bas artefakt registret:

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

Uppdatera uppgiften för `REGISTRY_FROM_URL` att ändra att använda `BASE_ARTIFACTS` registret

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

Kör uppgiften **Hello-World** för att ändra dess bas avbildnings beroende:

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>Uppdatera bas avbildningen med en "giltig" ändring

1. Öppna `Dockerfile` i `base-image-node` lagrings platsen.
1. Ändra `BACKGROUND_COLOR` till `Green` för att simulera en giltig ändring.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

Genomför ändringen och övervaka uppdaterings ordningen:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

När du har kört, skriver du **CTRL + C** och övervakar loggarna:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

När du är klar övervakar du aktiviteten för **bas avbildnings import** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

När du har kört, skriver du **CTRL + C** och övervakar loggarna:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

När du är klar kan du övervaka aktiviteten **Hello-World** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

När du har kört, skriver du **CTRL + C** och övervakar loggarna:

```azurecli-interactive
az acr task logs -r $REGISTRY
```

När du är klar hämtar du IP-adressen för platsen som är värd för den uppdaterade `hello-world` avbildningen:

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

Gå till webbplatsen i webbläsaren, som ska ha en grön (giltig) bakgrund.

### <a name="view-the-gated-workflow"></a>Visa gated-arbetsflödet

Utför stegen i föregående avsnitt igen med bakgrunds färgen röd.

1. Öppna `Dockerfile` i `base-image-node` lagrings platsen
1. Ändra `BACKGROUND_COLOR` till `Red` för att simulera en ogiltig ändring.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

Genomför ändringen och övervaka uppdaterings ordningen:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

När du har kört, skriver du **CTRL + C** och övervakar loggarna:

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

När du är klar övervakar du aktiviteten för **bas avbildnings import** :

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

När du har kört, skriver du **CTRL + C** och övervakar loggarna:

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

I det här läget bör du se aktiviteten **bas-import-Node-** aktivitet utan att verifiera och stoppa sekvensen för att publicera en `hello-world` uppdatering. Utdata liknar följande:

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>Publicera en uppdatering till `hello-world`

Ändringar i `hello-world` avbildningen fortsätter att använda den senast verifierade `node` avbildningen.

Eventuella ytterligare ändringar av bas avbildningen som skickar portarna för portarna `node` utlöser bas avbildnings uppdateringar till `hello-world` avbildningen.

## <a name="cleaning-up"></a>Rensning

Ta bort de resurser som används i den här artikeln när de inte längre behövs.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln. du använde ACR uppgifter för att skapa ett automatiserat hantera-arbetsflöde för att presentera uppdaterade bas avbildningar i din miljö. Se relaterad information för att hantera avbildningar i Azure Container Registry.


* [Rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md)
* [Låsa en behållar avbildning i ett Azure Container Registry](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io