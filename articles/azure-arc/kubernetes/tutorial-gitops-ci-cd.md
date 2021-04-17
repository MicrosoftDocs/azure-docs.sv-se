---
title: 'Självstudie: Implementera CI/CD med GitOps med Azure Arc-aktiverade Kubernetes-kluster'
description: Den här självstudien visar hur du ställer in en CI/CD-lösning med GitOps Azure Arc kubernetes-kluster. En konceptuell beskrivning av det här arbetsflödet finns i artikeln CI/CD Workflow using GitOps - Azure Arc enabled Kubernetes (CI/CD-arbetsflöde med GitOps – Azure Arc aktiverat Kubernetes).
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589150"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Självstudie: Implementera CI/CD med GitOps med hjälp Azure Arc kubernetes-aktiverade Kubernetes-kluster


I den här självstudien ska du konfigurera en CI/CD-lösning med Hjälp av GitOps Azure Arc kubernetes-kluster. Med hjälp av Azure Vote-exempelappen kommer du att:

> [!div class="checklist"]
> * Skapa ett Azure Arc aktiverat Kubernetes-kluster.
> * Anslut ditt program och Dina GitOps-lagringsplatsen till Azure Repos.
> * Importera CI/CD-pipelines.
> * Anslut din Azure Container Registry (ACR) till Azure DevOps och Kubernetes.
> * Skapa miljövariabelgrupper.
> * Distribuera `dev` `stage` miljöerna och .
> * Testa programmiljöerna.

Om du inte har ™ Azure-prenumeration kan du skapa ett [kostnadsfritt konto innan](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Innan du börjar

I den här självstudien förutsätter vi att du är bekant med Azure DevOps, Azure Repos och Pipelines samt Azure CLI.

* Logga in [på Azure DevOps Services](https://dev.azure.com/).
* Slutför den [föregående självstudien](./tutorial-use-gitops-connected-cluster.md) för att lära dig hur du distribuerar GitOps för din CI/CD-miljö.
* Förstå fördelarna [och arkitekturen med](./conceptual-configurations.md) den här funktionen.
* Kontrollera att du har:
  * Ett [anslutet Azure Arc aktiverat Kubernetes-kluster](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) **med namnet arc-cicd-cluster**.
  * En ansluten Azure Container Registry (ACR) med [AKS-integrering eller](../../aks/cluster-container-registry-integration.md) [icke-AKS-klusterautentisering.](../../container-registry/container-registry-auth-kubernetes.md)
  * Behörigheterna "Skapa administratör" och "Projektadministratör" för [Azure Repos](/azure/devops/repos/get-started/what-is-repos) och [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Installera följande Azure Arc Kubernetes CLI-tillägg för versioner >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Kör följande kommandon för att uppdatera tilläggen till den senaste versionen:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importera program och GitOps-lagringsplatsen till Azure Repos

Importera en [programlagringsplatsen](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) och en [GitOps-lagringsplatsen](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) till Azure Repos. I den här självstudien använder du följande exempeldatabaser:

* **arc-cicd-demo-src application** repo
   * Url: https://github.com/Azure/arc-cicd-demo-src
   * Innehåller azure vote-exempelappen som du ska distribuera med GitOps.
* **arc-cicd-demo-gitops** GitOps-lagringsplatsen
   * Url: https://github.com/Azure/arc-cicd-demo-gitops
   * Fungerar som bas för dina klusterresurser som finns i Azure Vote-appen.

Läs mer om att [importera Git-lagringsplatsen](/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Att importera och använda två separata lagringsplatsen för program- och GitOps-lagringsplatsen kan förbättra säkerheten och enkelheten. Programmet och GitOps-lagringsplatsens behörigheter och synlighet kan justeras individuellt.
> Klusteradministratören kanske till exempel inte hittar de ändringar i programkoden som är relevanta för klustrets önskade tillstånd. Omvänt behöver en programutvecklare inte känna till de specifika parametrarna för varje miljö – en uppsättning testvärden som ger täckning för parametrarna kan vara tillräckliga.

## <a name="connect-the-gitops-repo"></a>Ansluta GitOps-lagringsplatsen

Om du vill distribuera appen kontinuerligt ansluter du programdatabasen till klustret med GitOps. GitOps-lagringsplatsen **arc-cicd-demo-gitops** innehåller de grundläggande resurserna för att få igång din app i ditt **arc-cicd-cluster-kluster.**

Den första GitOps-lagringsplatsen innehåller [bara ett manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) som skapar de **dev-** och stage-namnrymder som motsvarar distributionsmiljöerna. 

GitOps-anslutningen som du skapar kommer automatiskt att:
* Synkronisera manifesten i manifestkatalogen.
* Uppdatera klustertillståndet.

CI/CD-arbetsflödet fyller i manifestkatalogen med extra manifest för att distribuera appen.


1. [Skapa en ny GitOps-anslutning](./tutorial-use-gitops-connected-cluster.md) till din nyligen importerade **arc-cicd-demo-gitops-lagringsplatsen** i Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Se till *att* Flux endast använder `arc-cicd-cluster/manifests` katalogen som bassökväg. Definiera sökvägen med hjälp av följande operatorparameter:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Om du använder en HTTPS-anslutningssträng och har anslutningsproblem bör du utelämna användarnamnprefixet i URL:en. Måste till exempel `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` ha tagits `alice@` bort. `--https-user`anger användaren i stället, till exempel `--https-user alice` .

1. Kontrollera status för distributionen i Azure Portal.
   * Om det lyckas visas både och `dev` `stage` namnrymder som skapats i klustret.

## <a name="import-the-cicd-pipelines"></a>Importera CI/CD-pipelines

Nu när du har synkroniserat en GitOps-anslutning måste du importera DE CI/CD-pipelines som skapar manifesten.

Programdatabasen innehåller en `.pipeline` mapp med de pipelines som du ska använda för PR, CI och CD. Importera och byt namn på de tre pipelines som finns i exempeldatabasen:

| Namn på pipelinefil | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Programmets PR-pipeline med **namnet arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Programmets CI-pipeline med **namnet arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Programmets CD-pipeline med **namnet arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Ansluta din ACR
Både dina pipelines och kluster använder ACR för att lagra och hämta Docker-avbildningar.

### <a name="connect-acr-to-azure-devops"></a>Ansluta ACR till Azure DevOps
Under CI-processen distribuerar du dina programcontainrar till ett register. Börja med att skapa en Azure-tjänstanslutning:

1. Öppna sidan Tjänstanslutningar på **sidan för projektinställningar** i Azure DevOps. I TFS öppnar du **sidan Tjänster** från **inställningsikonen** på den översta menyraden.
2. Välj **+ Ny tjänstanslutning** och välj den typ av tjänstanslutning som du behöver.
3. Fyll i parametrarna för tjänstanslutningen. För den här självstudien:
   * Ge tjänstanslutningen **namnet arc-demo-acr**. 
   * Välj **myResourceGroup** som resursgrupp.
4. Välj Bevilja **åtkomstbehörighet till alla pipelines.** 
   * Det här alternativet auktoriserar YAML-pipelinefiler för tjänstanslutningar. 
5. Välj **OK** för att skapa anslutningen.

### <a name="connect-acr-to-kubernetes"></a>Ansluta ACR till Kubernetes
Aktivera Kubernetes-klustret för att hämta avbildningar från ACR. Om det är privat krävs autentisering.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Ansluta ACR till befintliga AKS-kluster

Integrera ett befintligt ACR med befintliga AKS-kluster med följande kommando:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Skapa en pull-hemlighet för avbildningar

Om du vill ansluta icke-AKS och lokala kluster till ACR skapar du en pull-hemlighet för avbildningar. Kubernetes använder avbildningshemligheter för att lagra information som behövs för att autentisera registret.

Skapa en pull-hemlighet för avbildningar med följande `kubectl` kommando. Upprepa för både `dev` - och `stage` -namnrymderna.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

För att undvika att behöva ange en imagePullSecret för varje podd bör du överväga att lägga till imagePullSecret till tjänstkontot i `dev` `stage` namnrymderna och . Mer information [finns i Kubernetes-självstudien.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account)

## <a name="create-environment-variable-groups"></a>Skapa miljövariabelgrupper

### <a name="app-repo-variable-group"></a>Variabelgrupp för app-lagringsplatsen
[Skapa en variabelgrupp](/azure/devops/pipelines/library/variable-groups) med **namnet az-vote-app-dev**. Ställ in följande värden:

| Variabel | Värde |
| -------- | ----- |
| AZ_ACR_NAME | (din ACR-instans, till exempel. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (Din Azure-tjänstanslutning, som ska vara **arc-demo-acr** från tidigare i självstudien) |
| AZURE_VOTE_IMAGE_REPO | Den fullständiga sökvägen till Azure Vote App-lagringsplatsen, till exempel azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Git-anslutningssträngen för din GitOps-lagringsplatsen |
| Pat | En [skapad PAT-token](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) med läs-/skrivbehörigheter. Spara den om du vill använda den senare när du skapar `stage` variabelgruppen. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Markera din PAT som en hemlighetstyp. Överväg att länka hemligheter från ett [Azure KeyVault i dina program.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Mellansteg för miljövariabelgrupp

1. Klona **variabelgruppen az-vote-app-dev.**
1. Ändra namnet till **az-vote-app-stage**.
1. Kontrollera följande värden för motsvarande variabler:

| Variabel | Värde |
| -------- | ----- |
| ENVIRONMENT_NAME | Fas |
| TARGET_NAMESPACE | `stage` |

Nu är du redo att distribuera till `dev` `stage` miljöerna och .

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Distribuera utvecklingsmiljön för första gången
När CI- och CD-pipelines har skapats kör du CI-pipelinen för att distribuera appen för första gången.

### <a name="ci-pipeline"></a>CI-pipeline

Under den första CI-pipelinekörningen kan du få ett resursauktoriseringsfel när du läser namnet på tjänstanslutningen.
1. Kontrollera att variabeln som används är AZURE_SUBSCRIPTION.
1. Auktorisera användningen.
1. Kör pipelinen igen.

CI-pipelinen:
* Ser till att programändringen klarar alla automatiserade kvalitetskontroller för distribution.
* Genomför eventuell extra verifiering som inte kunde slutföras i PR-pipelinen.
    * Pipelinen är specifik för GitOps och publicerar även artefakterna för de genomförande som ska distribueras av CD-pipelinen.
* Verifierar att Docker-avbildningen har ändrats och att den nya avbildningen push-skickas.

### <a name="cd-pipeline"></a>CD-pipeline
Den lyckade CI-pipelinekörningen utlöser CD-pipelinen för att slutföra distributionsprocessen. Du distribuerar till varje miljö inkrementellt.

> [!TIP]
> Om CD-pipelinen inte utlöses automatiskt:
> 1. Kontrollera att namnet matchar grenutlösaren i [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Den bör vara `arc-cicd-demo-src CI`.
> 1. Kör CI-pipelinen igen.

När mallen och manifeständringarna i GitOps-lagringsplatsen har genererats skapar CD-pipelinen en stämpel, push-push-skicka den och skapar en PR för godkännande.
1. Öppna pr-länken som anges i `Create PR` aktivitetens utdata.
1. Kontrollera ändringarna i GitOps-lagringsplatsen. Du bör se:
   * Helm-mall på hög nivå ändras.
   * Kubernetes-manifest på låg nivå som visar de underliggande ändringarna i det önskade tillståndet. Flux distribuerar dessa manifest.
1. Om allt ser bra ut godkänner du och slutför PR.

1. Efter några minuter hämtar Flux ändringen och startar distributionen.
1. Vidarebefordra porten lokalt med hjälp `kubectl` av och se till att appen fungerar korrekt med:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Visa Azure Vote-appen i webbläsaren på `http://localhost:8080/` .

1. Rösta på dina favoriter och gör dig redo att göra några ändringar i appen.

## <a name="set-up-environment-approvals"></a>Konfigurera miljögodkännanden
Vid appdistribution kan du inte bara göra ändringar i koden eller mallarna, utan du kan också oavsiktligt placera klustret i ett felaktigt tillstånd.

Om utvecklingsmiljön visar en paus efter distributionen kan du hålla den från att gå till senare miljöer med hjälp av miljögodkännanden.

1. I ditt Azure DevOps-projekt går du till den miljö som behöver skyddas.
1. Gå till **Godkännanden och kontroller** för resursen.
1. Välj **Skapa**.
1. Ange godkännare och ett valfritt meddelande.
1. Välj **Skapa** igen för att slutföra tillägget av den manuella godkännandekontrollen.

Mer information finns i [självstudien Definiera godkännande och](/azure/devops/pipelines/process/approvals) kontroller.

Nästa gång CD-pipelinen körs pausas pipelinen efter att GitOps PR har skapats. Kontrollera att ändringen har synkroniserats korrekt och skickar grundläggande funktioner. Godkänn kontrollen från pipelinen för att låta ändringsflödet gå till nästa miljö.

## <a name="make-an-application-change"></a>Göra en programändring

Med den här baslinjeuppsättningen med mallar och manifest som representerar klustrets tillstånd gör du en liten ändring i appen.

1. Redigera filen **på lagringsplatsen arc-cicd-demo-src.** [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg)

2. Eftersom "Cats vs Dogs" inte får tillräckligt med röster ändrar du det till "Tabbar kontra blanksteg" för att öka röstantalet.

3. Genomför ändringen i en ny gren, push-skicka den och skapa en pull-begäran.
   * Det här är det typiska utvecklarflödet som startar CI/CD-livscykeln.

## <a name="pr-validation-pipeline"></a>PR-valideringspipeline

PR-pipelinen är den första försvarslinjen mot en felaktig ändring. Vanliga kvalitetskontroller för programkod omfattar linting och statisk analys. Från ett GitOps-perspektiv måste du också garantera samma kvalitet för den resulterande infrastrukturen som ska distribueras.

Programmets Dockerfile- och Helm-diagram kan använda lint på ett sätt som liknar programmet.

Fel som påträffades under lintningen sträcker sig från:
* Felaktigt formaterade YAML-filer till
* Metodtips, till exempel att ange cpu- och minnesgränser för ditt program.

> [!NOTE]
> För att få bästa möjliga täckning från Helm-linting i ett verkligt program måste du ersätta värden som är relativt lika dem som används i en verklig miljö.

Fel som hittades under pipelinekörningen visas i testresultatavsnittet för körningen. Här kan göra du följande:
* Spåra användbar statistik om feltyperna.
* Hitta den första ååsen som de identifierades för.
* Stackspårningsstil länkar till de kodavsnitt som orsakade felet.

När pipelinekörningen är klar har du försäkrat dig om kvaliteten på programkoden och mallen som ska distribuera den. Nu kan du godkänna och slutföra PR. KI:n körs igen och återskapar mallarna och manifesten innan CD-pipelinen utlöses.

> [!TIP]
> I en verklig miljö ska du inte glömma att ange förgreningsprinciper för att säkerställa att PR klarar dina kvalitetskontroller. Mer information finns i artikeln [Ange förgreningsprinciper.](/azure/devops/repos/git/branch-policies)

## <a name="cd-process-approvals"></a>Godkännanden av CD-process

En lyckad CI-pipelinekörning utlöser CD-pipelinen för att slutföra distributionsprocessen. Precis som första gången du kan använda CD-pipelinen distribuerar du till varje miljö inkrementellt. Den här gången kräver pipelinen att du godkänner varje distributionsmiljö.

1. Godkänn distributionen till `dev` miljön.
1. När mallen och manifestet har ändrats till GitOps-lagringsplatsen har genererats skapar CD-pipelinen en commit (genomföring) och push-pushar den och skapar en PR för godkännande.
1. Öppna pr-länken som du fick i uppgiften.
1. Kontrollera ändringarna i GitOps-lagringsplatsen. Du bör se:
   * Helm-malländringar på hög nivå.
   * Kubernetes-manifest på låg nivå som visar de underliggande ändringarna i det önskade tillståndet.
1. Om allt ser bra ut godkänner du och slutför PR.
1. Vänta tills distributionen har slutförts.
1. Som ett grundläggande röktest går du till programsidan och kontrollerar att röstningsappen nu visar Flikar jämfört med Blanksteg.
   * Vidarebefordra porten lokalt med hjälp `kubectl` av och se till att appen fungerar som den ska: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Visa Azure Vote-appen i webbläsaren på och http://localhost:8080/ kontrollera att röstningsalternativen har ändrats till Flikar jämfört med Blanksteg. 
1. Upprepa steg 1–7 för `stage` miljön.

Distributionen är nu klar. Detta avslutar CI/CD-arbetsflödet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här programmet tar du bort alla resurser med följande steg:

1. Ta bort Azure Arc GitOps-konfigurationsanslutningen:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Ta bort `dev` namnområdet:
   * `kubectl delete namespace dev`

3. Ta bort `stage` namnområdet:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du ställt in ett fullständigt CI/CD-arbetsflöde som implementerar DevOps från programutveckling till distribution. Ändringar i appen utlöser automatiskt validering och distribution, som är gated av manuella godkännanden.

Gå vidare till vår konceptuella artikel om du vill veta mer om GitOps och konfigurationer med Azure Arc aktiverat Kubernetes.

> [!div class="nextstepaction"]
> [CI/CD-arbetsflöde med GitOps – Azure Arc Aktiverat Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
