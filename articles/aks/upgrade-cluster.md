---
title: Uppgradera ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du uppgraderar ett Azure Kubernetes service-kluster (AKS) för att få de senaste funktionerna och säkerhets uppdateringarna.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 947d669d436308a550bce31f04c7b1a2b8a8485a
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734360"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett AKS-kluster (Azure Kubernetes Service)

En del av AKS-klustrets livs cykel omfattar att utföra periodiska uppgraderingar till den senaste Kubernetes-versionen. Det är viktigt att du tillämpar de senaste säkerhets versionerna eller uppgraderar för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar huvud komponenterna eller en enskild standardnode-pool i ett AKS-kluster.

Information om AKS-kluster som använder flera resurspooler eller Windows Server-noder finns i [uppgradera en Node-pool i AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!WARNING]
> En AKS kluster uppgradering utlöser en Cordon och tömmer dina noder. Om du har en låg beräknings kvot som är tillgänglig kan uppgraderingen Miss lyckas. Mer information finns i [öka kvoter](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga AKS-kluster uppgraderingar

Om du vill kontrol lera vilka Kubernetes-versioner som är tillgängliga för klustret använder du kommandot [AZ AKS get-uppgraderingar][az-aks-get-upgrades] . Följande exempel söker efter tillgängliga uppgraderingar i *myAKSCluster* i *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster som stöds kan Kubernetes minor-versioner inte hoppas över. Alla uppgraderingar måste utföras sekventiellt av högre versions nummer. Till exempel tillåts uppgraderingar mellan *1.14. x*  ->  *1.15. x* eller *1.15. x*  ->  *1.16. x* , men *1.14.* x  ->  *1.16. x* är inte tillåtna. 
> > Det går bara att hoppa över flera versioner när du uppgraderar från en _version som inte stöds_ tillbaka till en _version som stöds_. Till exempel en uppgradering från en *1.10. x* --> en 1.15 som stöds *. x* kan slutföras.

Följande exempel på utdata visar att klustret kan uppgraderas till versioner *1.19.1* och *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Om ingen uppgradering är tillgänglig får du meddelandet:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Anpassa uppgradering av nod överspänning

> [!Important]
> Nod toppar kräver en prenumerations kvot för det begärda Max antalet överspännings antal för varje uppgraderings åtgärd. Till exempel har ett kluster som har 5 noder, var och en med antalet 4 noder, totalt 20 noder. Om varje nod har ett högsta värde för överspänning på 50% krävs ytterligare beräknings-och IP-kvot för 10 noder (2 noder * 5 pooler) för att uppgraderingen ska slutföras.
>
> Om du använder Azure-CNI kontrollerar du att det finns tillgängliga IP-adresser i under nätet samt [uppfyller IP-kraven för Azure cni](configure-azure-cni.md).

Som standard konfigurerar AKS uppgraderingar till överspänning med en ytterligare nod. Ett standardvärde för en för max toppar ger AKS för att minimera arbets belastnings störningar genom att skapa ytterligare en nod innan Cordon/dräneringen av befintliga program ersätter en äldre version av noden. Det maximala spänning svärdet kan anpassas per Node-pool för att möjliggöra en kompromiss mellan uppgraderings hastighet och uppgraderings avbrott. Genom att öka det maximala spänning svärdet slutförs uppgraderings processen snabbare, men om du anger ett stort värde för maximal överspänning kan det orsaka avbrott under uppgraderings processen. 

Till exempel ger ett högsta värde för överspänning på 100% den snabbaste möjliga uppgraderings processen (dubblera antalet noder), men gör också att alla noder i Node-poolen töms samtidigt. Du kanske vill använda ett högre värde, till exempel för test miljöer. För produktionspooler, rekommenderar vi en max_surge inställning på 33%.

AKS accepterar både heltals värden och ett procent värde för maximal överspänning. Ett heltal, till exempel "5", indikerar fem ytterligare noder till överspänning. Värdet "50%" indikerar ett överspännings värde på hälften av det aktuella antalet noder i poolen. Högsta värde för överspännings procent kan vara minst 1% och högst 100%. Ett procent värde avrundas uppåt till närmaste antal noder. Om max värdet för maximal spänning är lägre än det aktuella antalet noder vid tidpunkten för uppgraderingen, används det aktuella antalet noder för max värdet för överspänning.

Under en uppgradering kan Max värdet för överspänning vara minst 1 och ett högsta värde som motsvarar antalet noder i nodens resurspool. Du kan ange större värden, men det maximala antalet noder som används för maximal spänning är inte högre än antalet noder i poolen vid tidpunkten för uppgraderingen.

> [!Important]
> Inställningen Max spänning i en Node-pool är permanent.  Vid efterföljande Kubernetes-uppgraderingar eller uppgraderingar av Node-versioner används den här inställningen. Du kan när som helst ändra det maximala spänning svärdet för dina nodkonfigurationer. För produktionspooler, rekommenderar vi en inställning för max-överspänning på 33%.

Använd följande kommandon för att ange max värde för toppar för nya eller befintliga nodkonfigurationer.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Med en lista över tillgängliga versioner för ditt AKS-kluster använder du kommandot [AZ AKS Upgrade][az-aks-upgrade] för att uppgradera. Under uppgraderings processen kommer AKS att: 
- Lägg till en ny buffert (eller så många noder som har kon figurer ATS i [högsta spänning](#customize-node-surge-upgrade)) till klustret som kör den angivna Kubernetes-versionen. 
- [Cordon och Töm][kubernetes-drain] en av de gamla noderna för att minimera störningar i program som körs (om du använder maximal överspänning kommer det att [Cordon och tömma][kubernetes-drain] sig så många noder på samma gång som antalet buffertplatser som anges). 
- När den gamla noden är helt dränerad kommer den att återavbildas för att ta emot den nya versionen och den blir noden buffert för följande nod som ska uppgraderas. 
- Den här processen upprepas tills alla noder i klustret har uppgraderats. 
- I slutet av processen tas den sista noden bort, vilket bibehåller det befintliga antalet noder i agenten och zon balansen.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder du har.

> [!IMPORTANT]
> Se till att alla `PodDisruptionBudgets` (PDBs) tillåter att minst en POD-replik flyttas vid en tidpunkt, annars kommer åtgärden för att tömma/ta bort.
> Om tömningen Miss lyckas, Miss lyckas uppgraderings åtgärden enligt design för att säkerställa att programmen inte störs. Korrigera vad som orsakade åtgärden att stoppa (felaktig PDBs, brist på kvot och så vidare) och försök igen.

För att bekräfta att uppgraderingen har slutförts använder du kommandot [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempel på utdata visar att klustret nu kör *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Ange kanal för automatisk uppgradering

Förutom att uppgradera ett kluster manuellt kan du ange en kanal för automatisk uppgradering i klustret. Följande uppgraderings kanaler är tillgängliga:

|Kanal| Åtgärd | Exempel
|---|---|---|
| `none`| inaktiverar automatiska uppgraderingar och behåller klustret med den aktuella versionen av Kubernetes| Standardinställning om den lämnas oförändrad|
| `patch`| Uppgradera automatiskt klustret till den senaste korrigerings versionen som stöds när den blir tillgänglig och behåll den lägre versionen.| Om exempelvis ett kluster kör version *1.17.7* och versions *1.17.9*, *1.18.4*, *1.18.6* och *1.19.1* är tillgängliga, uppgraderas klustret till *1.17.9*|
| `stable`| Uppgradera automatiskt klustret till den senaste korrigerings versionen som stöds på del version *n-1*, där *N* är den senaste lägre versionen som stöds.| Om exempelvis ett kluster kör version *1.17.7* och versions *1.17.9*, *1.18.4*, *1.18.6* och *1.19.1* är tillgängliga, uppgraderas klustret till *1.18.6*.
| `rapid`| Uppgradera automatiskt klustret till den senaste versionen som stöds på den senaste lägre versionen som stöds.| I de fall där klustret finns i en version av Kubernetes som är en del av en *n-2-* lägre version där *n* är den senaste lägre versionen som stöds, uppgraderas först klustret till den senaste korrigerings versionen som stöds på en lägre version av *n-1* . Om ett kluster till exempel kör version *1.17.7* och versioner *1.17.9*, *1.18.4*, *1.18.6* och *1.19.1* är tillgängliga, uppgraderas klustret först till *1.18.6* och uppgraderas sedan till *1.19.1*.

> [!NOTE]
> Klustrets automatiska uppgradering endast uppdateringar till GA-versioner av Kubernetes och kommer inte att uppdateras till för hands versioner.

Automatiskt uppgradera ett kluster följer samma process som att uppgradera ett kluster manuellt. Mer information finns i [uppgradera ett AKS-kluster][upgrade-cluster].

Automatisk kluster uppgradering för AKS-kluster är en förhands gransknings funktion.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registrera `AutoUpgradePreview` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] , som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Det tar några minuter för statusen att visa *registrerad*. Verifiera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av kommandot [AZ Provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Om du vill ställa in kanalen för automatisk uppgradering när du skapar ett kluster använder du parametern *Auto-Upgrade-Channel* , som liknar följande exempel.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Om du vill ställa in kanalen för automatisk uppgradering på ett befintligt kluster uppdaterar du parametern *Auto-Upgrade-Channel* , som liknar följande exempel.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du uppgraderar ett befintligt AKS-kluster. Mer information om hur du distribuerar och hanterar AKS-kluster finns i självstudierna.

> [!div class="nextstepaction"]
> [AKS-självstudier][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster