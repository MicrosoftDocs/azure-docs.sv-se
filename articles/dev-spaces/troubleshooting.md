---
title: Felsökning
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Lär dig hur du felsöker och löser vanliga problem när du aktiverar och använder Azure dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: e26f066294cb0a6a48c5a3299213206fe4226ad0
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210833"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Fel sökning av Azure dev Spaces

Den här guiden innehåller information om vanliga problem som kan uppstå när du använder Azure dev Spaces.

Om du har problem med att använda Azure dev Spaces kan du skapa ett [problem i Azure dev Spaces GitHub-lagringsplatsen](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Innan du börjar

För att felsöka problem på ett effektivare sätt kan det hjälpa att skapa mer detaljerade loggar för granskning.

För Visual Studio ställer du in `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` miljö variabeln på 1. Se till att starta om Visual Studio för att miljö variabeln ska börja gälla. När den är aktive rad skrivs detaljerade loggar till din `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalog.

I CLI kan du skriva ut mer information under kommando körningen med hjälp av `--verbose` växeln. Du kan också bläddra i mer detaljerade loggar i `%TEMP%\Azure Dev Spaces` . I en Mac kan du hitta *Temp* -katalogen genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator är *Temp* -katalogen vanligt vis `/tmp` . Kontrol lera också att loggning är aktiverat i [konfigurations filen för Azure CLI](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Azure dev Spaces fungerar också bäst vid fel sökning av en enskild instans eller pod. `azds.yaml`Filen innehåller en inställning, *replicaCount*, som anger antalet poddar som Kubernetes körs för tjänsten. Om du ändrar *replicaCount* för att konfigurera programmet så att det kör flera poddar för en specifik tjänst, bifogas fel söknings programmet till de första pod, i alfabetisk ordning. Fel söknings programmet ansluts till en annan Pod när den ursprungliga Pod återanvänds, vilket kan resultera i ett oväntat beteende.

## <a name="common-issues-when-using-local-process-with-kubernetes"></a>Vanliga problem när du använder lokal process med Kubernetes

### <a name="fail-to-restore-original-configuration-of-deployment-on-cluster"></a>Det gick inte att återställa den ursprungliga konfigurationen av distributionen på klustret

När du använder den lokala processen med Kubernetes, om den lokala processen med Kubernetes-klienten kraschar eller avslutas plötsligt, kan det hända att den lokala processen med Kubernetes inte återställs till ursprungs läget innan den lokala processen med Kubernetes är ansluten till den.

Du kan åtgärda det här problemet genom att distribuera om tjänsten till klustret.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Vanliga problem när du aktiverar Azure dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fel "Det gick inte att skapa Azure dev Spaces-kontrollant"

Du kanske ser det här felet när något går fel när du skapar kontrollanten. Om det är ett tillfälligt fel tar du bort och återskapar styrenheten för att åtgärda det.

Du kan också prova att ta bort kontroll enheten:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Använd Azure dev Spaces CLI för att ta bort en kontrollant. Det går inte att ta bort en kontrollant från Visual Studio. Du kan inte heller installera Azure dev Spaces CLI i Azure Cloud Shell så att du inte kan ta bort en kontrollant från Azure Cloud Shell.

Om du inte har installerat Azure dev Spaces CLI kan du först installera det med hjälp av följande kommando och sedan ta bort din styrenhet:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>Styrenheten kunde inte skapas på grund av kontrollantens namn längd

Ett namn på en Azure dev-enhet får inte vara längre än 31 tecken. Om namnet på din kontrollant överstiger 31 tecken när du aktiverar dev Spaces i ett AKS-kluster eller skapar en kontrollant får du ett fel meddelande. Ett exempel:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Åtgärda problemet genom att skapa en kontrollant med ett alternativt namn. Ett exempel:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Det går inte att aktivera dev-utrymmen när Windows-nodkonfigurationer läggs till i ett AKS-kluster

För närvarande är Azure dev Spaces endast avsett att köras på Linux-poddar och noder. När du har ett AKS-kluster med en Windows-adresspool måste du se till att Azure dev Spaces-poddar bara är schemalagda på Linux-noder. Om en Azure dev Spaces-pod är schemalagd att köras på en Windows-nod kommer Pod inte att starta och inte aktivera dev-utrymmen.

Åtgärda problemet genom att [lägga till en-utsmak](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) till ditt AKS-kluster så att Linux-poddar inte är schemalagda att köras på en Windows-nod.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fel "hittade inga icke-förorenade Linux-noder i klart läge i klustret. Det måste finnas minst en azds Linux-nod i klar läge för att distribuera poddar i namn området.

Azure dev Spaces kunde inte skapa en kontrollant i ditt AKS-kluster eftersom det inte gick att hitta en ej förorenad nod i ett *klart* tillstånd för att schemalägga poddar på. Azure dev Spaces kräver minst en Linux-nod i ett *klart* tillstånd som gör det möjligt att schemalägga poddar utan att ange tolerera.

Åtgärda problemet genom att [Uppdatera din smak konfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) på ditt AKS-kluster för att säkerställa att minst en Linux-nod tillåter schemaläggning av poddar utan att ange tolererande. Se också till att minst en Linux-nod som tillåter schemaläggning av poddar utan att ange tolererar är *klar* . Om noden tar lång tid att komma *igång* kan du prova att starta om noden.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fel "Azure dev Spaces-CLI har inte installerats korrekt" vid körning av AZ AKS use-dev-Spaces

En uppdatering av Azure dev Spaces CLI ändrade installations Sök vägen. Om du använder en tidigare version av Azure CLI än 2.0.63 kan du se det här felet. Använd om du vill visa din version av Azure CLI `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Trots att fel meddelandet körs `az aks use-dev-spaces` med en version av Azure CLI före 2.0.63, lyckas installationen. Du kan fortsätta att använda `azds` utan problem.

Åtgärda problemet genom att uppdatera installationen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) till 2.0.63 eller senare. Den här uppdateringen löser det fel meddelande som visas när du kör `az aks use-dev-spaces` . Alternativt kan du fortsätta att använda din aktuella version av Azure CLI och Azure dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Fel "Det gick inte att komma åt Kube-apiserver"

Du kanske ser det här felet när det inte går att ansluta till ditt AKS-klusters API-server i Azure dev Spaces.

Om åtkomst till din AKS-kluster-API-Server är låst eller om du har [auktoriserade IP-adressintervall för API-Server](../aks/api-server-authorized-ip-ranges.md) för ditt AKS-kluster, måste du också [skapa](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) eller [Uppdatera](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) klustret så att det [tillåter ytterligare intervall baserat på din region](configure-networking.md#aks-cluster-network-requirements)

Se till att API-servern är tillgänglig genom att köra kubectl-kommandon. Om API-servern inte är tillgänglig kontaktar du AKS-supporten och försöker igen när API-servern fungerar.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Vanliga problem vid förberedelse av ditt projekt för Azure dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning "Dockerfile kunde inte genereras på grund av ett språk som inte stöds"
Azure dev Spaces har inbyggt stöd för C# och Node.js. När du kör `azds prep` i en katalog med kod skrivet på något av dessa språk skapar Azure dev Spaces automatiskt en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure dev Spaces med kod som skrivits på andra språk, men du måste skapa Dockerfile manuellt innan du kör `azds up` för första gången.

Om ditt program är skrivet på ett språk som Azure dev Spaces inte har inbyggt stöd för, måste du ange en lämplig Dockerfile för att skapa en behållar avbildning som kör din kod. Docker innehåller en [lista över metod tips för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) och en [Dockerfile-referens](https://docs.docker.com/engine/reference/builder/) som kan hjälpa dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile på plats kan du köra `azds up` programmet i Azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Vanliga problem vid start eller stopp av tjänster med Azure dev Spaces

### <a name="error-config-file-not-found"></a>Fel: det gick inte att hitta konfigurations filen:

När `azds up` du kör kan du se det här felet. Både `azds up` och `azds prep` måste köras från rot katalogen för det projekt som du vill köra i ditt dev-utrymme.

Så här åtgärdar du problemet:
1. Ändra din aktuella katalog till rotmappen som innehåller tjänst koden. 
1. Om du inte har en _azds. yaml_ -fil i Code-mappen kör `azds prep` du för att skapa Docker, Kubernetes och Azure dev Spaces-tillgångar.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout vid "väntar på att behållar avbildningen byggs..." steg med virtuella AKS-noder

Denna tids gräns inträffar när du försöker använda dev Spaces för att köra en tjänst som är konfigurerad för att köras på en [virtuell AKS-nod](../aks/virtual-nodes-portal.md). Dev Spaces stöder för närvarande inte skapande eller fel sökning av tjänster på virtuella noder.

Om du kör `azds up` med `--verbose` växeln eller aktiverar utförlig loggning i Visual Studio visas ytterligare information:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Kommandot ovan visar att tjänstens Pod har tilldelats till *virtuell-Node-ACI-Linux*, som är en virtuell nod.

Åtgärda problemet genom att uppdatera Helm-diagrammet för tjänsten för att ta bort avsöknings *-eller* *tolerera* värden som tillåter att tjänsten körs på en virtuell nod. De här värdena definieras vanligt vis i diagrammets `values.yaml` fil.

Du kan fortfarande använda ett AKS-kluster som har funktionen virtuella noder aktiverade, om den tjänst som du vill bygga eller felsöka i dev Spaces körs på en VM-nod. Att köra en tjänst med dev Spaces på en VM-nod är standard konfigurationen.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fel "Det gick inte att hitta en färdig till gång pod" vid start av dev Spaces

Det här felet uppstår om Helm-klienten inte längre kommunicerar med till-Pod som körs i klustret.

Åtgärda problemet genom att starta om agent-noderna i klustret.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fel "release azds- \<identifier\> - \<spacename\> - \<servicename\> misslyckades: tjänsterna \<servicename\> " finns redan "eller" pull-åtkomst nekad för \<servicename\> , lagrings plats finns inte eller kan kräva Docker inloggning ""

Felen kan uppstå om du blandar kör direkta Helm-kommandon (till exempel `helm install` , `helm upgrade` eller `helm delete` ) med dev Spaces-kommandon (till exempel `azds up` och `azds down` ) inuti samma dev-utrymme. De inträffar eftersom dev Spaces har sin egen till-instans, som står i konflikt med din egen till-instans som körs i samma dev-utrymme.

Det är bra att använda både Helm-kommandon och dev Spaces-kommandon mot samma AKS-kluster, men varje dev Spaces-aktiverad namnrymd bör använda antingen ett eller ett annat.

Anta till exempel att du använder ett Helm-kommando för att köra hela programmet i ett överordnat dev-utrymme. Du kan skapa underordnade dev-utrymmen från överordnad, använda dev Spaces för att köra enskilda tjänster i de underordnade dev Spaces och testa tjänsterna tillsammans. När du är redo att kontrol lera ändringarna använder du ett Helm-kommando för att distribuera den uppdaterade koden till det överordnade dev-utrymmet. Använd inte `azds up` för att köra den uppdaterade tjänsten i det överordnade dev-utrymmet, eftersom den kommer att stå i konflikt med att tjänsten ursprungligen körs med Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Befintliga Dockerfile används inte för att bygga en behållare

Azure dev Spaces kan konfigureras så att de pekar på en viss _Dockerfile_ i ditt projekt. Om det verkar som om Azure dev Spaces inte använder den _Dockerfile_ som du förväntar dig att bygga dina behållare, kan du uttryckligen behöva berätta om Azure dev Spaces som Dockerfile ska använda. 

Lös problemet genom att öppna den _azds. yaml_ -fil som Azure dev-utrymmen genererar i projektet. Uppdatera *konfigurationer: utveckla: skapa: Dockerfile* för att peka på den Dockerfile som du vill använda. Ett exempel:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fel "obehörig: autentisering krävs" vid försök att använda en Docker-avbildning från ett privat register

Du använder en Docker-avbildning från ett privat register som kräver autentisering.

Du kan åtgärda det här problemet genom att låta dev-utrymmen autentisera och hämta bilder från det privata registret med [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Om du vill använda imagePullSecrets [skapar du en Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) i namn området där du använder avbildningen. Ange sedan hemligheten som en imagePullSecret i `azds.yaml` .

Nedan visas ett exempel på en att ange imagePullSecrets i `azds.yaml` .

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Om du anger imagePullSecrets i `azds.yaml` åsidosätts imagePullSecrets som anges i `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Fel "det går inte att starta tjänsten".

Du kanske ser det här felet när det inte går att starta en tjänst kod. Orsaken är ofta i användar kod. Om du vill ha mer diagnostikinformation aktiverar du mer detaljerad loggning när du startar tjänsten.

På kommando raden använder `--verbose` du för att aktivera mer detaljerad loggning. Du kan också ange ett utdataformat med `--output` . Ett exempel:

```cmd
azds up --verbose --output json
```

I Visual Studio:

1. Öppna **verktyg > alternativ** och under **projekt och lösningar**väljer du **skapa och kör**.
2. Ändra inställningarna för **MSBuild Project build-utdata utförligt** till **detaljerat** eller **diagnostik**.

    ![Skärm bild av dialog rutan verktyg alternativ](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Köra en tjänst igen efter att styrenheten har skapats på nytt

Du får ett fel meddelande om att *tjänsten inte kan startas* när du försöker köra om en tjänst när du har tagit bort och återskapat den Azure dev Spaces-styrenhet som är associerad med det här klustret. I den här situationen innehåller utförliga utdata följande text:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det här felet uppstår eftersom borttagning av dev Spaces-styrenheten inte tar bort tjänster som tidigare installerats av kontrollanten. Om du återskapar kontrollanten och sedan försöker köra tjänsterna med hjälp av den nya styrenheten Miss lyckas det eftersom de gamla tjänsterna fortfarande är på plats.

Åtgärda problemet genom att använda `kubectl delete` kommandot för att manuellt ta bort de gamla tjänsterna från klustret, och kör sedan dev Spaces för att installera de nya tjänsterna.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fel "det går inte att starta tjänsten". När du använder Dockerfiles med flera steg

Du får ett fel meddelande om att det *inte går att starta tjänsten* när du använder en Dockerfile med flera steg. I den här situationen innehåller utförliga utdata följande text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Det här felet beror på att Azure dev Spaces för närvarande inte stöder versioner med flera steg. Om du vill undvika versioner av flera steg kan du skriva om din Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Nätverks trafiken vidarebefordras inte till ditt AKS-kluster när du ansluter till utvecklings datorn

När du använder [Azure dev Spaces för att ansluta ditt AKS-kluster till din utvecklings dator](https://code.visualstudio.com/docs/containers/local-process-kubernetes)kan du stöta på ett problem där nätverks trafiken inte vidarebefordras mellan din utvecklings dator och ditt AKS-kluster.

När du ansluter din utvecklings dator till ditt AKS-kluster vidarebefordrar Azure dev-utrymmen nätverks trafiken mellan ditt AKS-kluster och utvecklings datorn genom att ändra din utvecklings dators `hosts` fil. Azure dev Spaces skapar en post i `hosts` med adressen för Kubernetes-tjänsten som du ersätter som ett värdnamn. Den här posten används med vidarebefordran av portar för att dirigera nätverks trafik mellan utvecklings datorn och AKS-klustret. Om en tjänst på utvecklings datorn är i konflikt med den port i Kubernetes-tjänsten som du ersätter kan inte Azure dev-utrymmen vidarebefordra nätverks trafik för Kubernetes-tjänsten. *Windows BranchCache* -tjänsten är till exempel vanligt vis kopplad till *0.0.0.0:80*, vilket innebär att det uppstår en konflikt mellan port 80 och alla lokala IP-adresser.

För att åtgärda det här problemet måste du stoppa alla tjänster eller processer som står i konflikt med porten i Kubernetes-tjänsten som du försöker ersätta. Du kan använda verktyg som *netstat*för att kontrol lera vilka tjänster eller processer på din utvecklings dator som är i konflikt.

Till exempel för att stoppa och inaktivera tjänsten *Windows BranchCache* :
* Kör `services.msc` från kommando tolken.
* Högerklicka på *BranchCache* och välj *Egenskaper*.
* Klicka på *stoppa*.
* Alternativt kan du inaktivera den genom att ställa in *Starttyp* på *inaktive rad*.
* Klicka på *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Fel "ingen AzureAssignedIdentity hittades för pod: azds/azds-webhook-Deployment- \<id\> Assigned State"

När du kör en tjänst med Azure dev Spaces i ett AKS-kluster med hanterade [identiteter](../aks/use-managed-identity.md) och [Pod-hanterade](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) identiteter installerade, kan processen sluta svara efter steget för *diagram installationen* . Om du inspekterar *azds – webhook* i *azds* -namn utrymmet kan du se det här felet.

Tjänsterna för Azure dev-tjänster körs i klustret och använder klustrets hanterade identitet för att kommunicera med Server dels tjänsterna i Azure dev-tjänsten utanför klustret. När Pod-hanterad identitet installeras konfigureras nätverks regler på klustrets noder för att omdirigera alla anrop för autentiseringsuppgifter för hanterad identitet till en [NMI-DaemonSet () som är installerad på klustret](https://github.com/Azure/aad-pod-identity#node-managed-identity). Den här NMI-DaemonSet identifierar den anropande Pod och ser till att Pod har märkts korrekt för att få åtkomst till den begärda hanterade identiteten. Azure dev Spaces kan inte identifiera om ett kluster har Pod-hanterad identitet installerad och inte kan utföra den nödvändiga konfigurationen för att ge Azure dev Spaces-tjänster åtkomst till klustrets hanterade identitet. Eftersom Azure dev Spaces-tjänsterna inte har kon figurer ATS för åtkomst till klustrets hanterade identitet, tillåter inte NMI-DaemonSet att de får en Azure AD-token för den hanterade identiteten och kan inte kommunicera med Server dels tjänster för Azure dev Spaces.

Du kan åtgärda det här problemet genom att använda en [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md) för *azds-injektioner-webhook* och uppdatera poddar instrumenterad av Azure dev Spaces för att få åtkomst till den hanterade identiteten.

Skapa en fil med namnet *webhookException. yaml* och kopiera följande yaml-definition:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Ovanstående fil skapar ett *AzurePodIdentityException* -objekt för *azds-mataren-webhook*. Använd följande om du vill distribuera objektet `kubectl` :

```cmd
kubectl apply -f webhookException.yaml
```

Om du vill uppdatera poddar som hanteras av Azure dev Spaces för att få åtkomst till den hanterade identiteten uppdaterar du *namn området* i definitionerna nedan yaml och använder för `kubectl` att tillämpa det för varje dev-utrymme.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

Du kan också skapa *AzureIdentity* -och *AzureIdentityBinding* -objekt och uppdatera Pod-etiketter för arbets belastningar som körs i utrymmen som Instrumentas av Azure dev Spaces för att få åtkomst till den hanterade identiteten som skapats av AKS-klustret.

Om du vill visa information om den hanterade identiteten kör du följande kommando för ditt AKS-kluster:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

Kommandot ovan matar ut *clientId* och *resourceId* för den hanterade identiteten. Ett exempel:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Skapa ett *AzureIdentity* -objekt genom att skapa en fil med namnet *clusteridentity. yaml* och använda följande yaml-definition uppdaterad med information om din hanterade identitet från föregående kommando:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Skapa ett *AzureIdentityBinding* -objekt genom att skapa en fil med namnet *clusteridentitybinding. yaml* och använda följande yaml-definition:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Använd följande för att distribuera *AzureIdentity* -och *AzureIdentityBinding* -objekten `kubectl` :

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

När du har distribuerat *AzureIdentity* -och *AzureIdentityBinding* -objekten kan alla arbets belastningar med etiketten *aadpodidbinding: mitt-etikett-Value* komma åt klustrets hanterade identitet. Lägg till den här etiketten och distribuera om alla arbets belastningar som körs i ett dev-utrymme. Ett exempel:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Vanliga problem med Visual Studio och Visual Studio Code med Azure dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fel "nödvändiga verktyg och konfigurationer saknas"

Det här felet kan inträffa vid start av VS Code: "[Azure dev Spaces] verktyg och konfigurationer som krävs för att bygga och felsöka [projekt namn] saknas."
Felet innebär att azds.exe inte finns i miljövariabeln PATH, som visas i VS Code.

Försök att starta VS Code från en kommando tolk där miljövariabeln PATH är korrekt inställd.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fel "verktyg som krävs för att bygga och felsöka ProjectName är inaktuella."

Du ser det här felet i Visual Studio Code om du har en nyare version av VS Code-tillägget för Azure dev Spaces, men en äldre version av Azure dev Spaces CLI.

Försök att hämta och installera den senaste versionen av Azure dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fel: "Det gick inte att hitta fel söknings tillägg för typ: coreclr"

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Du kanske inte har VS Code-tillägget för C# installerat på utvecklings datorn. Tillägget C# innehåller fel söknings stöd för .NET Core (CoreCLR).

Lös problemet genom att installera [vs Code-tillägget för C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fel "konfigurerad fel söknings typ" coreclr "stöds inte"

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Du kanske inte har VS Code-tillägget för Azure dev Spaces installerat på din utvecklings dator.

Lös problemet genom att installera VS Code-tillägget för Azure dev Spaces.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fel "ogiltigt ' CWD '-värde '/src '. Det går inte att hitta den angivna filen i systemet. " eller "starta: program"/src/[sökväg till projektets binärfil] finns inte "

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code. Som standard används VS Code-tillägget `src` som arbets katalog för projektet på behållaren. Om du har uppdaterat `Dockerfile` för att ange en annan arbets katalog kan du se det här felet.

Åtgärda problemet genom att uppdatera `launch.json` filen under `.vscode` katalogen i projektmappen. Ändra `configurations->cwd` direktivet så att det pekar på samma katalog som den som `WORKDIR` definierats i projektets `Dockerfile` . Du kan också behöva uppdatera- `configurations->program` direktivet.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel meddelandet "Pipe-program" azds "avslutades utan förvarning med koden 126".

Du kanske ser det här felet när du kör fel söknings programmet Visual Studio Code.

Åtgärda problemet genom att stänga och öppna Visual Studio-koden igen. Starta om fel söknings programmet.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Felet "intern Watch misslyckades: se ENOSPC" vid koppling av fel sökning till ett Node.js program

Felet uppstår när noden som kör Pod med det Node.js program som du försöker ansluta till med en fel sökare har överskridit värdet *FS. inotify. max_user_watches* . I vissa fall [kan standardvärdet *FS. inotify. max_user_watches* vara för litet för att kunna hantera ett fel söknings program direkt till en POD](https://github.com/Azure/AKS/issues/772).

En tillfällig lösning på det här problemet är att öka värdet för *FS. inotify. max_user_watches* på varje nod i klustret och starta om noden för att ändringarna ska börja gälla.

## <a name="other-common-issues"></a>Andra vanliga problem

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Felet "azds" känns inte igen som ett internt eller externt kommando, fungerande program eller en kommando fil

Det här felet kan inträffa om `azds.exe` inte är installerat eller korrekt konfigurerat.

Så här åtgärdar du problemet:

1. Kontrol lera platsen% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI för `azds.exe` . Om det är där, lägger du till platsen i miljövariabeln PATH.
2. Om `azds.exe` inte är installerat kör du följande kommando:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Auktoriseringsfel "Microsoft. DevSpaces/register/Action"

Du behöver *ägar* -eller *deltagar* åtkomst i din Azure-prenumeration för att kunna hantera Azure dev Spaces. Om du försöker hantera dev Spaces och du inte har *ägare* eller *deltagar* åtkomst till den associerade Azure-prenumerationen kan du se ett auktoriseringsfel. Ett exempel:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Du kan åtgärda det här problemet genom att använda ett konto med *ägar* -eller *deltagar* åtkomst till Azure-prenumerationen genom att registrera `Microsoft.DevSpaces` namn området manuellt:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nya poddar startar inte

Kubernetes-initieraren kan inte använda PodSpec för nya poddar på grund av RBAC-behörighet ändringar i rollen *kluster-admin* i klustret. Den nya Pod kan också ha en ogiltig PodSpec, till exempel det tjänst konto som är kopplat till Pod inte längre finns. Om du vill se poddar som är i ett *väntande* tillstånd på grund av ett problem med initieraren använder du `kubectl get pods` kommandot:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Det här problemet kan påverka poddar i *alla namn områden* i klustret, inklusive namn rymder där Azure dev Spaces inte är aktiverat.

Åtgärda problemet genom att [Uppdatera dev Spaces CLI till den senaste versionen](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) och ta sedan bort *azds-InitializerConfiguration* från Azure dev Spaces-styrenheten:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

När du har tagit bort *azds-InitializerConfiguration* från Azure dev Spaces-kontrollanten använder `kubectl delete` du för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort distribuerar du om din poddar.

Om nya poddar fortfarande fastnar i ett *väntande* tillstånd efter en distribution, använder `kubectl delete` du för att ta bort eventuella poddar i ett *väntande* tillstånd. När alla väntande poddar har tagits bort tar du bort kontrollanten från klustret och installerar om den:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

När din styrenhet har installerats om distribuerar du din poddar.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Felaktiga RBAC-behörigheter för att anropa dev Spaces-styrenhet och API: er

Användaren som använder Azure dev Spaces-styrenheten måste ha åtkomst för att kunna läsa administratörs *kubeconfig* i AKS-klustret. Den här behörigheten är till exempel tillgänglig i den [inbyggda administratörs rollen för Azure Kubernetes service-klustret](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Användaren som har åtkomst till Azure dev Spaces-kontrollanten måste också ha rollen *deltagare* eller *ägare* till Azure för kontrollanten. Mer information om hur du uppdaterar en användares behörigheter för ett AKS-kluster finns [här](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Så här uppdaterar du användarens Azure-roll för kontrollanten:

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till resurs gruppen som innehåller kontrollanten, som vanligt vis är samma som ditt AKS-kluster.
1. Aktivera kryss rutan *Visa dolda typer* .
1. Klicka på kontroll enheten.
1. Öppna fönstret *Access Control (IAM)* .
1. Klicka på fliken *roll tilldelningar* .
1. Klicka på *Lägg till* och *Lägg sedan till roll tilldelning*.
    * För *roll*väljer du antingen *deltagare* eller *ägare*.
    * För *tilldela åtkomst till*väljer du *Azure AD-användare, grupp eller tjänstens huvud namn*.
    * För *Välj*söker du efter den användare som du vill ge behörighet.
1. Klicka på *Spara*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchning Miss lyckas för en offentlig URL som är associerad med en dev Spaces-tjänst

Du kan konfigurera en offentlig URL-slutpunkt för din tjänst genom `--enable-ingress` att ange växeln till `azds prep` kommandot eller genom att markera `Publicly Accessible` kryss rutan i Visual Studio. Det offentliga DNS-namnet registreras automatiskt när du kör tjänsten i dev Spaces. Om det här DNS-namnet inte är registrerat visas en *sida som inte kan visas* eller så *går det inte att nå platsen* i webbläsaren vid anslutning till den offentliga URL: en.

Så här åtgärdar du problemet:

* Kontrol lera status för alla URL: er som är kopplade till dina dev Spaces-tjänster:

  ```console
  azds list-uris
  ```

* Om en URL är i *vänte* läge väntar dev Spaces fortfarande på att DNS-registreringen ska slutföras. Ibland tar det några minuter innan registreringen är klar. I dev Spaces öppnas även en lokal värd för varje tjänst, som du kan använda när du väntar på DNS-registrering.
* Om en URL är i *vänte* läge i mer än 5 minuter kan det tyda på ett problem med den externa DNS-Pod som skapar den offentliga slut punkten eller nginx ingress-styrenhet Pod som hämtar den offentliga slut punkten. Använd följande kommandon för att ta bort dessa poddar och tillåta AKS att automatiskt återskapa dem:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel: fel vid anslutning till uppströms anslutning eller från koppling/återställning före huvuden

Du kanske ser det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. Det här felet innebär att container porten inte är tillgänglig. Detta kan bero på följande:

* Behållaren håller fortfarande på att skapas och distribueras. Det här problemet kan uppstå om du kör `azds up` eller startar fel söknings programmet och sedan försöker komma åt behållaren innan den har distribuerats.
* Port konfigurationen är inte konsekvent i _Dockerfile_, Helm-diagrammet och alla Server koder som öppnar en port.

Så här åtgärdar du problemet:

1. Om behållaren håller på att skapas/distribueras kan du vänta 2-3 sekunder och försöka komma åt tjänsten igen. 
1. Kontrol lera port konfigurationen i följande till gångar:
    * ** [Helm-diagram](https://docs.helm.sh):** Anges av `service.port` kommandot och `deployment.containerPort` i Values. yaml autogenererade by `azds prep` .
    * Portar som öppnas i program kod, till exempel i Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Det gick inte att hitta typen eller namn områdets namn "Mina bibliotek"

Det går inte att hitta ett biblioteks projekt som du använder. Med dev Spaces är Bygg kontexten på projekt-/tjänst nivån som standard.  

Så här åtgärdar du problemet:

1. Ändra `azds.yaml` filen för att ange Bygg kontexten till lösnings nivån.
2. Ändra `Dockerfile` filerna och `Dockerfile.develop` för att referera till projektfilerna, till exempel korrekt i `.csproj` förhållande till den nya bygg kontexten.
3. Lägg till en `.dockerignore` i samma katalog som `.sln` filen.
4. Uppdatera `.dockerignore` med ytterligare poster efter behov.

Du kan hitta ett exempel [här](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horisontell Pod-skalning fungerar inte i ett dev-utrymme

När du kör en tjänst i ett dev-utrymme injiceras tjänstens Pod [med ytterligare behållare för Instrumentation](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) och alla behållare i en POD måste ha resurs gränser och begär Anden som ställts in för horisontell Pod autoskalning.

Du kan åtgärda det här problemet genom att använda en resurs förfrågan och begränsa till de inmatade dev Spaces-behållare. Resurs begär Anden och gränser kan tillämpas för den inmatade behållaren (devspaces-proxy) genom att lägga till `azds.io/proxy-resources` anteckningen i din POD-spec. Värdet ska anges till ett JSON-objekt som representerar avsnittet resurser i behållar specifikationen för proxyservern.

Nedan visas ett exempel på en proxy-resurs anteckning som ska tillämpas på pod-specifikationen.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Aktivera Azure dev Spaces i ett befintligt namn område med poddar som körs

Du kan ha ett befintligt AKS-kluster och-namnrymd med poddar där du vill aktivera Azure dev Spaces.

Om du vill aktivera Azure dev Spaces i ett befintligt namn område i ett AKS-kluster kör `use-dev-spaces` du och använder `kubectl` för att starta om alla poddar i namn området.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

När din poddar har startats om kan du börja använda din befintliga namnrymd med Azure dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Aktivera Azure dev Spaces på AKS-kluster med begränsad utgående trafik för klusternoder

Om du vill aktivera Azure dev Spaces i ett AKS-kluster där den utgående trafiken från klusternoder är begränsad, måste du tillåta följande FQDN: er:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Hämta Linux Alpine och andra Azure dev Spaces-bilder |
| gcr.io | HTTP: 443 | Hämta Helm/till-avbildningar|
| storage.googleapis.com | HTTP: 443 | Hämta Helm/till-avbildningar|

Uppdatera brand väggen eller säkerhets konfigurationen för att tillåta nätverks trafik till och från alla ovanstående FQDN och [infrastruktur tjänster i Azure dev Spaces](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations).

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Fel "Det gick inte att hitta klustret \<cluster\> i prenumerationen \<subscriptionId\> "

Du kan se det här felet om din kubeconfig-fil är riktad mot ett annat kluster eller en annan prenumeration än du försöker använda med klients IDE-verktyget för Azure dev Spaces. Verktyget Azure dev Spaces på klient sidan replikerar beteendet för *kubectl*, som använder [en eller flera kubeconfig-filer](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) för att välja och kommunicera med klustret.

Så här åtgärdar du problemet:

* Används `az aks use-dev-spaces -g <resource group name> -n <cluster name>` för att uppdatera den aktuella kontexten. Det här kommandot aktiverar även Azure dev Spaces på ditt AKS-kluster om inte redan har Aktiver ATS. Du kan också använda `kubectl config use-context <cluster name>` för att uppdatera den aktuella kontexten.
* Använd `az account show` för att visa den aktuella Azure-prenumerationen som du riktar in och kontrol lera att detta är korrekt. Du kan ändra den prenumeration som du riktar in med `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Fel vid användning av dev Spaces efter rotering av AKS-certifikat

När [du har roterat certifikaten i AKS-klustret](../aks/certificate-rotation.md), kan vissa åtgärder, t `azds space list` . ex. och inte, utföras `azds up` . Du måste också uppdatera certifikaten på Azure dev Spaces-kontrollanten när du har roterat certifikaten i klustret.

Åtgärda problemet genom att se till att *kubeconfig* har de uppdaterade certifikaten med hjälp av `az aks get-credentials` och kör sedan `azds controller refresh-credentials` kommandot. Exempel:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
