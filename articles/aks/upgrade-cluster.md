---
title: Uppgradera ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du uppgraderar Azure Kubernetes Service kluster (AKS) för att få de senaste funktionerna och säkerhetsuppdateringarna.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779623"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett AKS-kluster (Azure Kubernetes Service)

En del av AKS-klustrets livscykel omfattar periodiska uppgraderingar till den senaste Kubernetes-versionen. Det är viktigt att du använder de senaste säkerhets versionerna eller uppgraderar för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar huvudkomponenterna eller en standardnodpool i ett AKS-kluster.

Information om AKS-kluster som använder flera nodpooler eller Windows Server-noder finns i [Uppgradera en nodpool i AKS.][nodepool-upgrade]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!WARNING]
> En uppgradering av ett AKS-kluster utlöser en avspärrning och tömning av dina noder. Om du har en låg tillgänglig beräkningskvot kan uppgraderingen misslyckas. Mer information finns i [öka kvoter](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga uppgraderingar av AKS-kluster

Du kan kontrollera vilka Kubernetes-versioner som är tillgängliga för klustret med [kommandot az aks get-upgrades.][az-aks-get-upgrades] I följande exempel söker efter tillgängliga uppgraderingar till *myAKSCluster* i *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster som stöds går det inte att hoppa över Kubernetes-delversioner. Alla uppgraderingar måste utföras sekventiellt efter huvudversionsnummer. Uppgraderingar mellan *1.14.x*  ->  *1.15.x* eller *1.15.x*  ->  *1.16.x* tillåts till exempel, men *1.14.x*  ->  *1.16.x* tillåts inte. 
> > Det går bara att hoppa över flera versioner när du uppgraderar från _en version som inte stöds_ tillbaka till en version som _stöds._ Till exempel kan en uppgradering från *en 1.10.x* --> som stöds *1.15.x* slutföras.

Följande exempelutdata visar att klustret kan uppgraderas till version *1.19.1* och *1.19.3:*

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Om ingen uppgradering är tillgänglig visas meddelandet:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Anpassa uppgradering av nodtoppar

> [!Important]
> Nodtoppar kräver prenumerationskvot för det begärda högsta antalet ökningar för varje uppgraderingsåtgärd. Ett kluster som har 5 nodpooler, var och en med 4 noder, har till exempel totalt 20 noder. Om varje nodpool har ett högsta ökningsvärde på 50 % krävs ytterligare beräkning och IP-kvot på 10 noder (2 noder * 5 pooler) för att slutföra uppgraderingen.
>
> Om du Azure CNI måste du kontrollera att det finns tillgängliga IP-adresser i undernätet för att uppfylla [IP-kraven för Azure CNI](configure-azure-cni.md).

Som standard konfigurerar AKS uppgraderingar så att de ökar med ytterligare en nod. Med standardvärdet ett för inställningarna för högsta ökning kan AKS minimera störningar i arbetsbelastningen genom att skapa ytterligare en nod före avspärrningen/tömning av befintliga program för att ersätta en äldre versionad nod. Maxvärdet för ökning kan anpassas per nodpool för att möjliggöra en avvägning mellan uppgraderingshastighet och uppgraderingsavbrott. Genom att öka maxvärdet för ökningar slutförs uppgraderingsprocessen snabbare, men om du anger ett stort värde för maximal ökning kan det orsaka avbrott under uppgraderingsprocessen. 

Ett högsta ökningsvärde på 100 % ger till exempel den snabbaste möjliga uppgraderingsprocessen (dubblering av antalet noder) men gör också att alla noder i nodpoolen töms samtidigt. Du kanske vill använda ett högre värde som detta för testmiljöer. För produktionsnodpooler rekommenderar vi en max_surge inställning på 33 %.

AKS accepterar både heltalsvärden och ett procentvärde för högsta ökning. Ett heltal som "5" anger att ytterligare fem noder ska gå upp. Värdet "50 %" anger ett ökningsvärde på hälften av det aktuella antalet noder i poolen. Högsta ökningsprocent kan vara minst 1 % och högst 100 %. Ett procentvärde avrundas uppåt till närmaste nodantal. Om maxvärdet för ökningar är lägre än det aktuella antalet noder vid tidpunkten för uppgraderingen används det aktuella nodantalet för maxvärdet för ökningar.

Under en uppgradering kan maxvärdet för ökningar vara minst 1 och ett maxvärde som är lika med antalet noder i nodpoolen. Du kan ange större värden, men det maximala antalet noder som används för högsta ökning är inte högre än antalet noder i poolen vid tidpunkten för uppgraderingen.

> [!Important]
> Inställningen för högsta ökning i en nodpool är permanent.  Efterföljande Kubernetes-uppgraderingar eller nodversionsuppgraderingar använder den här inställningen. Du kan när som helst ändra det högsta värdet för dina nodpooler. För produktionsnodpooler rekommenderar vi en högsta inställning på 33 %.

Använd följande kommandon för att ange högsta ökningsvärden för nya eller befintliga nodpooler.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Använd kommandot [az aks upgrade][az-aks-upgrade] för att uppgradera med en lista över tillgängliga versioner för ditt AKS-kluster. Under uppgraderingsprocessen kommer AKS att: 
- lägg till en ny buffertnod (eller så många noder som konfigurerats vid [högsta](#customize-node-surge-upgrade)ökning) till det kluster som kör den angivna Kubernetes-versionen. 
- [avspärrning och][kubernetes-drain] tömning av en av de gamla noderna för att [][kubernetes-drain] minimera störningar i program som körs (om du använder maximal ökning av avspärrning kommer den att avspärra och tömma så många noder samtidigt som antalet angivna buffertnoder). 
- När den gamla noden är fullständigt tömd återställs den till att ta emot den nya versionen och blir buffertnoden för följande nod som ska uppgraderas. 
- Den här processen upprepas tills alla noder i klustret har uppgraderats. 
- I slutet av processen tas den sista buffertnoden bort, vilket upprätthåller det befintliga antalet agentnoder och zonsaldot.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder du har.

> [!IMPORTANT]
> Se till att alla (PDU) tillåter att minst 1 poddreplik flyttas åt gången, annars misslyckas åtgärden med att `PodDisruptionBudgets` tömma/avlägsna.
> Om tömningsåtgärden misslyckas misslyckas uppgraderingsåtgärden av design för att säkerställa att programmen inte avbryts. Korrigera vad som gjorde att åtgärden stoppas (felaktiga PDU:er, brist på kvot och så vidare) och försök igen.

Bekräfta att uppgraderingen lyckades med kommandot [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempelutdata visar att klustret nu kör *1.18.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Ange kanal för automatisk uppgradering

Förutom att manuellt uppgradera ett kluster kan du ange en kanal för automatisk uppgradering i klustret. Följande uppgraderingskanaler är tillgängliga:

|Kanal| Action | Exempel
|---|---|---|
| `none`| inaktiverar automatiska uppgraderingar och behåller klustret i den aktuella versionen av Kubernetes| Standardinställning om den lämnas oförändrad|
| `patch`| uppgradera automatiskt klustret till den senaste korrigeringsversionen som stöds när det blir tillgängligt samtidigt som delversionen är densamma.| Om ett kluster till exempel kör version *1.17.7* och *versionerna 1.17.9,* *1.18.4,* *1.18.6* och *1.19.1* är tillgängliga uppgraderas klustret till *1.17.9*|
| `stable`| uppgradera automatiskt klustret till den senaste korrigeringsversionen som stöds på delversion *N-1,* där *N* är den senaste delversionen som stöds.| Om ett kluster till exempel kör version *1.17.7* och *versionerna 1.17.9,* *1.18.4,* *1.18.6* och *1.19.1* är tillgängliga uppgraderas klustret till *1.18.6.*
| `rapid`| uppgradera automatiskt klustret till den senaste korrigeringsversionen som stöds på den senaste delversion som stöds.| I de fall där klustret har en version av Kubernetes som har en *N-2-delversion* där *N* är den senaste delversionen som stöds uppgraderar klustret först till den senaste korrigeringsversionen som stöds på *N-1-delversionen.* Om ett kluster till exempel kör version *1.17.7* och *versionerna 1.17.9*, *1.18.4*, *1.18.6* och *1.19.1* är tillgängliga uppgraderas klustret först till *1.18.6* och uppgraderas sedan till *1.19.1.*

> [!NOTE]
> Klusteruppgradering uppdateras endast till GA-versioner av Kubernetes och kommer inte att uppdateras till förhandsversioner.

Uppgradering av ett kluster sker automatiskt på samma sätt som när ett kluster uppgraderas manuellt. Mer information finns i Uppgradera [ett AKS-kluster.][upgrade-cluster]

Automatisk uppgradering av kluster för AKS-kluster är en förhandsgranskningsfunktion.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registrera `AutoUpgradePreview` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Det tar några minuter för statusen att visa *Registrerad.* Kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Om du vill ange kanalen för automatisk uppgradering när du skapar ett kluster använder du parametern *auto-upgrade-channel,* ungefär som i följande exempel.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Om du vill ange kanalen för automatisk uppgradering i ett befintligt kluster uppdaterar du *parametern auto-upgrade-channel,* ungefär som i följande exempel.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du uppgraderar ett befintligt AKS-kluster. Mer information om hur du distribuerar och hanterar AKS-kluster finns i uppsättningen självstudier.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
