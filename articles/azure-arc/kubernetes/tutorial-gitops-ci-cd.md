---
title: 'Självstudie: implementera CI/CD med GitOps med Azure Arc-aktiverade Kubernetes-kluster'
description: Den här självstudien vägleder dig genom att konfigurera en CI/CD-lösning med GitOps med Azure Arc-aktiverade Kubernetes-kluster. För ett konceptuellt sätt på det här arbets flödet, se CI/CD-arbetsflöde med hjälp av GitOps-Azure Arc-aktiverad Kubernetes artikel.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: 72caca47cde960eb7298ec2cf0c6994755cb3159
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121617"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Självstudie: implementera CI/CD med GitOps med Azure Arc-aktiverade Kubernetes-kluster


I den här självstudien skapar du en CI/CD-lösning med hjälp av GitOps med Azure Arc-aktiverade Kubernetes-kluster. Med hjälp av Azures exempel Azure-röstning kommer du att:

> [!div class="checklist"]
> * Skapa ett Azure Arc-aktiverat Kubernetes-kluster.
> * Anslut din program-och GitOps-databaser till Azure databaser.
> * Importera CI/CD-pipeliner.
> * Anslut din Azure Container Registry (ACR) till Azure DevOps och Kubernetes.
> * Skapa miljö variabel grupper.
> * Distribuera- `dev` och- `stage` miljöer.
> * Testa program miljöerna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudien förutsätter att Azure-DevOps, Azure-databaser och pipelines är välkända och Azure CLI.

* Logga in på [Azure DevOps Services](https://dev.azure.com/).
* Slutför den [föregående själv studie kursen](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) och lär dig hur du distribuerar GitOps för din CI/CD-miljö.
* Förstå [fördelarna och arkitekturen](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-configurations) i den här funktionen.
* Kontrol lera att du har:
  * Ett [anslutet Azure Arc-aktiverat Kubernetes-kluster](https://docs.microsoft.com/azure/azure-arc/kubernetes/quickstart-connect-cluster#connect-an-existing-kubernetes-cluster) med namnet **Arc-cicd-Cluster**.
  * En ansluten Azure Container Registry (ACR) med antingen [AKS-integrering](https://docs.microsoft.com/azure/aks/cluster-container-registry-integration) eller [icke-AKS](https://docs.microsoft.com/azure/container-registry/container-registry-auth-kubernetes).
  * Behörigheterna "Build admin" och "Project admin" för [Azure-databaser](https://docs.microsoft.com/azure/devops/repos/get-started/what-is-repos) och Azure- [pipeliner](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-get-started).
* Installera följande Azure Arc-aktiverade Kubernetes CLI-tillägg för versioner >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Kör följande kommandon för att uppdatera tilläggen till den senaste versionen:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Importera program-och GitOps-databaser till Azure databaser

Importera ett [program lagrings platsen](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo) och en [GitOps-lagrings platsen](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo) till Azure databaser. I den här självstudien använder du följande exempel databaser:

* **Arc-cicd-demo-src** Application lagrings platsen
   * ADRESSER https://github.com/Azure/arc-cicd-demo-src
   * Innehåller exemplet på Azure-röstning som du ska distribuera med GitOps.
* **Arc-cicd-demo-gitops** GitOps lagrings platsen
   * ADRESSER https://github.com/Azure/arc-cicd-demo-gitops
   * Fungerar som en bas för dina kluster resurser som är hus till Azure röstnings-appen.

Läs mer om att [Importera git-databaser](https://docs.microsoft.com/azure/devops/repos/git/import-git-repository).

>[!NOTE]
> Att importera och använda två separata databaser för program-och GitOps-databaser kan förbättra säkerheten och enkelheten. Program-och GitOps-databasernas behörigheter och synlighet kan justeras individuellt.
> Kluster administratören kanske till exempel inte hittar ändringarna i program koden som är relevanta för klustrets önskade tillstånd. En programutvecklare behöver inte känna till de specifika parametrarna för varje miljö – en uppsättning test värden som ger täckning för parametrarna kan vara tillräckligt.

## <a name="connect-the-gitops-repo"></a>Anslut GitOps-lagrings platsen

Om du vill distribuera din app kontinuerligt ansluter du programmet lagrings platsen till klustret med hjälp av GitOps. Din **Arc-cicd-demo-gitops** gitops-lagrings platsen innehåller de grundläggande resurserna för att komma igång med din app på ditt **Arc-cicd-kluster** kluster.

Den inledande GitOps-lagrings platsen innehåller bara ett [manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) som skapar namn områden för **utveckling** och **steg** som motsvarar distributions miljöerna.

GitOps-anslutningen som du skapar kommer automatiskt att:
* Synkronisera manifesten i manifest katalogen.
* Uppdatera kluster tillstånd.

CI/CD-arbetsflödet fyller i manifest katalogen med extra manifest för att distribuera appen.


1. [Skapa en ny GitOps-anslutning](https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-use-gitops-connected-cluster) till den nyligen importerade **Arc-cicd-demo-GitOps-** lagrings platsen i Azure databaser.

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

1. Se till att flödet *endast* använder `arc-cicd-cluster/manifests` katalogen som bas Sök väg. Definiera sökvägen med hjälp av följande operator-parameter:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Om du använder en HTTPS-anslutningssträng och har anslutnings problem, se till att du utelämnar prefixet username i URL: en. Till exempel `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` måste ha `alice@` tagits bort. `--https-user`Anger till exempel användaren `--https-user alice` .

1. Kontrol lera distributionens status i Azure Portal.
   * Om det lyckas visas både `dev` och `stage` namn områden som skapats i klustret.

## <a name="import-the-cicd-pipelines"></a>Importera CI/CD-pipeliner

Nu när du har synkroniserat en GitOps-anslutning måste du importera de CI/CD-pipeliner som skapar manifesten.

Programmet lagrings platsen innehåller en `.pipeline` mapp med de pipeliner som du använder för pull, CI och CD. Importera och Byt namn på de tre pipelinen som finns i exemplet lagrings platsen:

| Namn på pipeline-fil | Beskrivning |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | Pipeline för program PR, namngiven **Arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | Pipeline för program-CI, med namnet **Arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | Programmets CD-pipeline, med namnet **Arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Anslut din ACR
Både dina pipelines och klustret använder ACR för att lagra och hämta Docker-avbildningar.

### <a name="connect-acr-to-azure-devops"></a>Anslut ACR till Azure DevOps
Under CI-processen distribuerar du dina program behållare till ett register. Börja med att skapa en Azure-tjänst anslutning:

1. Öppna sidan **tjänst anslutningar** på sidan projekt inställningar i Azure-DevOps. I TFS öppnar du sidan **tjänster** från ikonen **Inställningar** i den översta meny raden.
2. Välj **+ ny tjänst anslutning** och välj den typ av tjänst anslutning du behöver.
3. Fyll i parametrarna för tjänst anslutningen. I den här självstudien:
   * Namnge tjänst anslutnings **bågen – demo-ACR**. 
   * Välj **myResourceGroup** som resurs grupp.
4. Välj **behörigheten bevilja åtkomst till alla pipeliner**. 
   * Det här alternativet godkänner YAML-pipeline-filer för tjänst anslutningar. 
5. Klicka på **OK** för att skapa anslutningen.

### <a name="connect-acr-to-kubernetes"></a>Anslut ACR till Kubernetes
Aktivera Kubernetes-klustret för att hämta avbildningar från din ACR. Om den är privat krävs autentisering.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Ansluta ACR till befintliga AKS-kluster

Integrera en befintlig ACR med befintliga AKS-kluster med hjälp av följande kommando:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Skapa en bilds pull-hemlighet

Om du vill ansluta icke-AKS och lokala kluster till din ACR skapar du en avbildning av hämtnings hemligheten. Kubernetes använder image-pull-hemligheter för att lagra information som behövs för att autentisera ditt register.

Skapa en bild av pull-hemlighet med följande `kubectl` kommando. Upprepa för både- `dev` och- `stage` namn områdena.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> För att undvika att behöva ange en imagePullSecret för varje Pod kan du överväga att lägga till imagePullSecret till tjänst kontot `dev` i `stage` namn områdena och. Mer information finns i [själv studie kursen om Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) .

## <a name="create-environment-variable-groups"></a>Skapa miljö variabel grupper

### <a name="app-repo-variable-group"></a>Lagrings platsen variabel grupp för app
[Skapa en variabel grupp](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups) med namnet **AZ-rösta-app-dev**. Ställ in följande värden:

| Variabel | Värde |
| -------- | ----- |
| AZ_ACR_NAME | (till exempel din ACR-instans. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (din Azure Service-anslutning, som ska vara **Arc-demo-ACR** från tidigare i självstudien) |
| AZURE_VOTE_IMAGE_REPO | Den fullständiga sökvägen till Azures röst app-lagrings platsen, till exempel azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | Git-anslutningssträngen för din GitOps-lagrings platsen |
| PAT | En [skapad Pat-token](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?#create-a-pat) med käll-och Skriv behörighet. Spara den för att använda senare när du skapar `stage` variabel gruppen. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Markera din PAT som en hemlig typ. Överväg att länka hemligheter från ett [Azure-valv](https://docs.microsoft.com/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)i dina program.
>
### <a name="stage-environment-variable-group"></a>Variabel grupp för fas miljö

1. Klona variabel gruppen **AZ-rösta-app-dev** .
1. Ändra namnet till **AZ-rösta-app-Stage**.
1. Se till att följande värden för motsvarande variabler:

| Variabel | Värde |
| -------- | ----- |
| ENVIRONMENT_NAME | Fas |
| TARGET_NAMESPACE | `stage` |

Nu är du redo att distribuera till- `dev` och- `stage` miljöer.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Distribuera utvecklings miljön för första gången
Med de CI-och CD-pipeliner som skapats kör du CI-pipeline för att distribuera appen för första gången.

### <a name="ci-pipeline"></a>CI-pipeline

Under den inledande CI-pipeline-körningen kan du få ett resurs auktoriseringsfel för att läsa namnet på tjänst anslutningen.
1. Kontrol lera att variabeln som används är AZURE_SUBSCRIPTION.
1. Auktorisera användningen.
1. Kör pipelinen igen.

CI-pipeline:
* Säkerställer att program ändringen skickar alla automatiserade kvalitets kontroller för distribution.
* Utför eventuell extra verifiering som inte kunde slutföras i PR-pipeline.
    * För GitOps publicerar pipelinen även artefakterna för det genomförande som ska distribueras av CD-pipeline.
* Verifierar Docker-avbildningen har ändrats och den nya avbildningen skickas.

### <a name="cd-pipeline"></a>CD-pipeline
Körningen av en lyckad CI-pipeline startar CD-pipeline för att slutföra distributions processen. Du distribuerar till varje miljö stegvis.

> [!TIP]
> Om CD-pipelinen inte startar automatiskt:
> 1. Kontrol lera att namnet matchar gren utlösaren i [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Den bör vara `arc-cicd-demo-src CI`.
> 1. Kör CI-pipeline igen.

När mallen och manifestet ändras till GitOps-lagrings platsen har genererats, skapar CD-pipeline en incheckning, push-överför den och skapar en PR för godkännande.
1. Öppna PR-länken i Uppgiftsutdata `Create PR` .
1. Verifiera ändringarna i GitOps-lagrings platsen. Du bör se:
   * Helm mal len på hög nivå ändras.
   * Kubernetes-manifest på låg nivå som visar de underliggande ändringarna av det önskade läget. Flöden distribuerar dessa manifest.
1. Om allt ser bra ut, Godkänn och slutför du PR.

1. Efter några minuter, tar flödet upp ändringen och startar distributionen.
1. Vidarebefordra porten lokalt med `kubectl` och se till att appen fungerar korrekt med:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Visa Azures röst-app i webbläsaren på `http://localhost:8080/` .

1. Rösta på dina favoriter och bli redo att göra några ändringar i appen.

## <a name="set-up-environment-approvals"></a>Konfigurera miljö godkännanden
Vid en app-distribution kan du inte bara göra ändringar i koden eller mallarna, men du kan också oavsiktligt försätta klustret i ett felaktigt tillstånd.

Om dev-miljön visar ett avbrott efter distributionen kan du fortsätta att gå till senare miljöer med hjälp av miljö godkännanden.

1. I ditt Azure DevOps-projekt går du till den miljö som behöver skyddas.
1. Gå till **godkännanden och kontroller** av resursen.
1. Välj **Skapa**.
1. Ange god kännare och ett valfritt meddelande.
1. Välj **skapa** igen för att slutföra tillägget av kontrollen för manuellt godkännande.

Mer information finns i själv studie kursen [definiera godkännande och kontroller](https://docs.microsoft.com/azure/devops/pipelines/process/approvals) .

Nästa gången som CD-pipelinen körs pausas pipelinen när GitOps PR-genereringen har skapats. Kontrol lera att ändringen har synkroniserats korrekt och att de grundläggande funktionerna skickas. Godkänn kontrollen från pipelinen för att låta ändrings flödet till nästa miljö.

## <a name="make-an-application-change"></a>Gör en program ändring

Med den här bas linje uppsättningen mallar och manifest som representerar status i klustret, gör du en liten ändring i appen.

1. Redigera filen i **Arc-cicd-demo-src-** lagrings platsen [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) .

2. Eftersom "katter vs hundar" inte får tillräckligt med röster, ändra det till "tabbar mot Spaces" för att öka antalet röster.

3. Genomför ändringen i en ny gren, skicka den och skapa en pull-begäran.
   * Det här är det vanligaste Developer-flödet som startar CI/CD-livscykeln.

## <a name="pr-validation-pipeline"></a>Pipeline för PR-verifiering

PR pipeline är den första försvars linjen mot en felaktig förändring. Vanliga kvalitets kontroller för program kod inkluderar en luddfri och statisk analys. Från ett GitOps-perspektiv måste du också garantera samma kvalitet för att den resulterande infrastrukturen ska kunna distribueras.

Programmets Dockerfile-och Helm-diagram kan använda luddfri på ett liknande sätt som programmet.

Fel hittades under rad avstånd från:
* Felaktigt formaterade YAML-filer till
* Rekommendationer för bästa praxis, till exempel att ange processor-och minnes gränser för ditt program.

> [!NOTE]
> För att få den bästa täckningen från Helm-luddfri i ett verkligt program måste du ersätta värden som är rimligen liknar de som används i en verklig miljö.

Fel som upptäckts under körningen av pipelinen visas i avsnittet test resultat i körningen. Här kan göra du följande:
* Spåra den användbara statistiken för fel typerna.
* Hitta den första incheckning som identifierades.
* Stack spårnings stil länkar till de kod avsnitt som orsakade felet.

När pipelinen har körts klart har du garanterat kvaliteten på program koden och mallen som ska distribuera den. Nu kan du godkänna och slutföra PR. CI körs igen och återskapar mallarna och manifesten innan CD-pipelinen aktive ras.

> [!TIP]
> I en verklig miljö glömmer du inte att ange filial principer för att se till att PRen klarar dina kvalitets kontroller. Mer information finns i artikeln [Ange gren principer](https://docs.microsoft.com/azure/devops/repos/git/branch-policies) .

## <a name="cd-process-approvals"></a>Godkännande av CD-process

En lyckad CI pipeline-körning utlöser en CD-pipeline för att slutföra distributions processen. Precis som första gången du kan CD-pipeline distribuerar du varje miljö stegvis. Den här gången kräver pipelinen att du godkänner varje distributions miljö.

1. Godkänn distributionen till `dev` miljön.
1. När mallen och manifestet ändras till GitOps-lagrings platsen har genererats, skapar CD-pipeline en incheckning, push-överför den och skapar en PR för godkännande.
1. Öppna PR-länken som angetts i uppgiften.
1. Verifiera ändringarna i GitOps-lagrings platsen. Du bör se:
   * Helm mal len på hög nivå ändras.
   * Kubernetes-manifest på låg nivå som visar de underliggande ändringarna av det önskade läget.
1. Om allt ser bra ut, Godkänn och slutför du PR.
1. Vänta tills distributionen har slutförts.
1. Som ett grundläggande rök test navigerar du till sidan program och kontrollerar att röstnings appen nu visar tabbar eller blank steg.
   * Vidarebefordra porten lokalt med `kubectl` och se till att appen fungerar korrekt med: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Visa Azures röst-app i webbläsaren på http://localhost:8080/ och kontrol lera att röst valen har ändrats till tabbar eller blank steg. 
1. Upprepa steg 1-7 för `stage` miljön.

Distributionen är klar. Detta avslutar CI/CD-arbetsflödet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort alla resurser med följande steg:

1. Ta bort Azure Arc-GitOps konfigurations anslutning:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Ta bort `dev` namn området:
   * `kubectl delete namespace dev`

3. Ta bort `stage` namn området:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett fullständigt CI/CD-arbetsflöde som implementerar DevOps från program utveckling genom distribution. Ändringar i appen utlöser automatiskt validering och distribution, som är gated med manuella godkännanden.

Gå vidare till vår konceptuella artikel och lär dig mer om GitOps och konfigurationer med Azure Arc-aktiverade Kubernetes.

> [!div class="nextstepaction"]
> [CI/CD-arbetsflöde med GitOps-Azure Arc-aktiverad Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)
