---
title: Självstudie – bygge för snabb behållar avbildning
description: I den här självstudien lär du dig att skapa en avbildning av en dockercontainer i Azure med Azure Container Registry Tasks (ACR Tasks) och sedan distribuera den till Azure Container Instances.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 384c7bebea8ed8120a1bc8134e4189e5e7bcb8db
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060286"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Självstudier: Skapa och distribuera containeravbildningar i molnet med Azure Container Registry-uppgifter

[ACR Tasks](container-registry-tasks-overview.md) är en uppsättning funktioner i Azure Container Registry som ger effektiva avbildningar av dockercontainrar i Azure. I den här artikeln får du lära dig att använda funktionen *Quick Build* (Snabbskapa) i ACR Tasks.

Utvecklingscykeln för den ”inre loopen” är den iterativa processen för att skriva kod, skapa och testa ditt program innan du utför en källkontroll. En snabbuppgift förlänger den inre loopen till molnet, vilket ger dig versionsverifiering och automatisk överföring av skapade avbildningar till containerregistret. Avbildningarna skapas internt i molnet nära registret, vilket ger snabbare distribution.

All din Dockerfile-expertis är direkt överföringsbar till ACR Tasks. Du behöver inte ändra dina Dockerfile-filer för att kunna skapa uppgifter med ACR Tasks i molnet, bara kommandot som du kör. 

I den här självstudien, som är del ett i en serie:

> [!div class="checklist"]
> * Hämta källkod för exempelprogram
> * Skapa en containeravbildning i Azure
> * Distribuera en behållare till Azure Container Instances

I kommande självstudier får du lära dig att använda i ACR Tasks för automatiska containeravbildningsversioner som bygger på kodincheckning och uppdateringar av basavbildningar. ACR-aktiviteter kan också köra [aktiviteter med flera steg](container-registry-tasks-multi-step.md), med hjälp av en yaml-fil för att definiera steg för att skapa, skicka och välja att testa flera behållare.

## <a name="prerequisites"></a>Förutsättningar

### <a name="github-account"></a>GitHub-konto

Skapa ett konto på https://github.com om du inte redan har ett. Den här självstudieserien använder en GitHub-lagringsplats för att visa automatiserade avbildningsversioner i ACR Tasks.

### <a name="fork-sample-repository"></a>Förgrena en exempellagringsplats

Därefter använder du GitHubs användargränssnitt för att förgrena exempellagringsplatsen till ditt GitHub-konto. I denna självstudie skapar du en containeravbildning från källan på lagringsplatsen och i nästa självstudie skickar du en incheckning till din lagringsplatsförgrening för att starta en automatiserad uppgift.

Förgrena den här lagringsplatsen: https://github.com/Azure-Samples/acr-build-helloworld-node

![Skärmbild av knappen Förgrening (markerad) i GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Klona din förgrening

När du har förgrenat lagringsplatsen kan du klona din förgrening och ange den katalog som innehåller din lokala klon.

Klona lagrings platsen med `git` , Ersätt **\<your-github-username\>** med ditt GitHub-användar namn:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Ange den katalog som innehåller källkoden:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-gränssnitt

Kommandona i den här självstudieserien är formaterade för Bash-gränssnittet. Om du föredrar att använda PowerShell, kommandotolken eller något annat gränssnitt, kan du behöva justera radfortsättningen och miljövariabelns format därefter.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Skapa i Azure med ACR Tasks

Nu när du har hämtat källkoden till din dator, följer du dessa steg för att skapa ett containerregister och en containeravbildning med ACR Tasks.

För att göra det enklare att köra exempelkommandona, använder självstudierna i serien gränssnittets miljövariabler. Kör följande kommando för att ange variabeln `ACR_NAME`. Ersätt **\<registry-name\>** med ett unikt namn för ditt nya behållar register. Register namnet måste vara unikt inom Azure, får bara innehålla gemena bokstäver och innehålla 5-50 alfanumeriska tecken. De andra resurser som du skapar i självstudien baseras på det här namnet, så du behöver bara ändra den första variabeln.

```console
ACR_NAME=<registry-name>
```

När containerregistrets miljövariabel har fyllts i, bör du nu kunna kopiera och klistra in resten av kommandona i självstudien utan att redigera några värden. Kör följande kommandon för att skapa en resursgrupp och ett containerregister:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Nu när du har ett register kan du använda ACR Tasks till att skapa en containeravbildning från exempelkoden. Utför kommandot [az acr build][az-acr-build] för att utföra en *snabbuppgift*:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

Utdata från kommandot [az acr build][az-acr-build] liknar följande. Du kan se uppladdningen av källkoden (”sammanhanget”) till Azure, samt information om åtgärden `docker build` som ACR Tasks kör i molnet. Eftersom ACR Tasks använder `docker build` för att skapa dina avbildningar, krävs inga ändringar av dina Dockerfiles-filer för att du ska kunna börja använda ACR Tasks omedelbart.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

I slutet av utdatan visar ACR Tasks de beroenden som har identifierats för avbildningen. Detta gör att ACR Tasks kan automatisera avbildningsversioner av basavbildningsuppdateringar, t.ex. när en basavbildning uppdateras med OS- eller ramverkskorrigeringar. Du lär dig mer om stödet i ACR Tasks för basavbildningsuppdateringar senare i den här självstudieserien.

## <a name="deploy-to-azure-container-instances"></a>Distribuera till Azure Container Instances

ACR Tasks skickar automatiskt skapade avbildningar till registret som standard, vilket innebär att du kan distribuera dem från registret direkt.

I det här avsnittet skapar du ett Azure Key Vault och tjänstens huvudnamn. Sedan distribuerar du behållaren till Azure Container Instances (ACI) med hjälp av autentiseringsuppgifterna för tjänstens huvudnamn.

### <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

Alla produktionsscenarier bör använda [huvudnamnen för tjänsten][service-principal-auth] för att få åtkomst till ett containerregister i Azure. Med tjänstens huvudnamn får du rollbaserad åtkomstkontroll till containeravbildningarna. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

#### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](../key-vault/index.yml), skapar du ett med Azure CLI och följande kommandon.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Skapa ett huvudnamn för tjänsten och lagra autentiseringsuppgifter

Du måste nu skapa ett huvudnamn för tjänsten och lagra dess autentiseringsuppgifter i nyckelvalvet.

Använd kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] till att skapa tjänstens huvudnamn och [az keyvault secret set][az-keyvault-secret-set] för att lagra **lösenordet** till tjänstens huvudnamn i valvet:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argumentet `--role` i föregående kommando konfigurerar huvudnamnet för tjänsten med rollen *acrpull*, vilket endast ger den hämtningsåtkomst till registret. Om du vill bevilja både sändnings- och hämtningsåtkomst ändrar du argumentet `--role` till *acrpush*.

Därefter lagrar du *appId* för tjänstens huvudnamn i valvet, vilket är det **användarnamn** du skickar till Azure Container Registry för autentisering:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure Key Vault och lagrat två hemligheter i det:

* `$ACR_NAME-pull-usr`: ID för tjänstens huvudnamn som ska användas som containerregistrets **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenord för tjänstens huvudnamn som ska användas som containerregistrets **lösenord**.

Nu kan du referera till dessa hemligheter efter namn när du eller dina program och tjänster hämtar avbildningar från registret.

### <a name="deploy-a-container-with-azure-cli"></a>Distribuera en container med Azure CLI

Nu när autentiseringsuppgifterna för tjänstens huvudnamn lagras som Azure Key Vault-hemligheter, kan dina program och tjänster använda dem för att få åtkomst till ditt privata register.

Kör kommandot [az container create][az-container-create] för att distribuera en behållarinstans. Kommandot använder de autentiseringsuppgifter för tjänstens huvudnamn som lagras i Azure Key Vault för autentiseringen till containerregistret.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Värdet `--dns-name-label` måste vara unikt i Azure, så föregående kommando lägger till containerregistrets namn i containerns DNS-namnetikett. Utdata från kommandot visar containerns fullständiga domännamn (FQDN), till exempel:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Anteckna containerns FQDN – du behöver det i nästa avsnitt.

### <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill se startprocessen i behållaren använder du kommandot [az container attach][az-container-attach]:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

`az container attach`Utdata visar först behållarens status när den hämtar avbildningen och startar och binder sedan den lokala konsolens STDOUT och stderr till den för behållaren.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

När `Server running at http://localhost:80` visas navigerar du till containerns FQDN i webbläsaren för att se programmet som körs. FQDN bör ha visats i utdatan från kommandot `az container create` du körde i föregående avsnitt.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Exempel program som körs i webbläsaren":::

Om du vill ta bort konsolen från containern trycker du på `Control+C`.

## <a name="clean-up-resources"></a>Rensa resurser

Stoppa behållarinstansen med kommandot [az container delete][az-container-delete]:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Om du vill ta bort *alla* resurser som du har skapat i den här självstudien, inklusive containerregistret, nyckelvalvet och tjänstens huvudnamn, utfärdar du följande kommandon. Dessa resurser används dock i [nästa självstudie](container-registry-tutorial-build-task.md) i serien, så du kan behålla dem om du går direkt vidare till nästa självstudie.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Nästa steg

Nu när du har testat den inre loopen med en snabbuppgift kan du konfigurera en **versionsuppgift** för att utlösa containeravbildningsversioner när du har skickat källkod till en Git-lagringsplats:

> [!div class="nextstepaction"]
> [Utlös automatiska versioner med uppgifter](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
