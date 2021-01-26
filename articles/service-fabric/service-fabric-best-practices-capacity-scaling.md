---
title: Kapacitets planering och skalning för Azure Service Fabric
description: Metod tips för planering och skalning Service Fabric kluster och program.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: d7d9ed8fa695c636e7aaf36fd034babb4de012d9
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784688"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Kapacitets planering och skalning för Azure Service Fabric

Innan du skapar ett Azure Service Fabric-kluster eller skalar beräknings resurser som är värdar för klustret är det viktigt att planera för kapacitet. Mer information om att planera för kapacitet finns i [planera Service Fabric kluster kapacitet](./service-fabric-cluster-capacity.md). För ytterligare vägledning för kluster skalbarhet, se [Service Fabric skalbarhets överväganden](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Förutom att överväger nodtyp och kluster egenskaper bör du förvänta dig skalnings åtgärder att ta längre tid än en timme att slutföra för en produktions miljö. Detta gäller även om antalet virtuella datorer som du lägger till.

## <a name="autoscaling"></a>Automatisk skalning
Du bör utföra skalnings åtgärder via Azure Resource Manager mallar, eftersom det är bästa praxis att behandla [resursfiler som kod](./service-fabric-best-practices-infrastructure-as-code.md). 

Om du använder automatisk skalning via skalnings uppsättningar för virtuella datorer blir din version av Resource Manager-mallen felaktigt definierad för antalet instanser för skalnings uppsättningar för virtuella datorer. Felaktig definition ökar risken för att framtida distributioner kommer att orsaka oönskade skalnings åtgärder. I allmänhet bör du använda autoskalning om:

* Att distribuera dina Resource Manager-mallar med lämplig kapacitet som har deklarerats stöder inte ditt användnings fall.
     
   Förutom manuell skalning kan du konfigurera en [kontinuerlig integrering och leverans pipeline i Azure DevOps Services med hjälp av distributions projekt för Azure Resource Group](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Den här pipelinen utlöses ofta av en Logic app som använder prestanda mått för virtuella datorer som frågas från [Azure Monitor REST API](../azure-monitor/platform/rest-api-walkthrough.md). Pipelinen skalas effektivt baserat på de mått som du vill ha, medan du optimerar för Resource Manager-mallar.
* Du behöver bara skala en nod för skalnings uppsättning för virtuella datorer i taget.
   
   För att skala ut med tre eller fler noder i taget bör du [skala ut ett Service Fabric kluster genom att lägga till en skalnings uppsättning för virtuella datorer](virtual-machine-scale-set-scale-node-type-scale-out.md). Det är säkert att skala in och skala ut skalnings uppsättningar för virtuella datorer vågrätt, en nod i taget.
* Du har silver tillförlitlighet eller högre för ditt Service Fabric-kluster och silver tålighet eller högre i alla skalor där du konfigurerar regler för automatisk skalning.
  
   Den lägsta kapaciteten för regler för automatisk skalning måste vara lika med eller större än fem instanser av virtuella datorer. Det måste också vara lika med eller större än din högsta Tillförlitlighets nivå för den primära nodtypen.

> [!NOTE]
> Den Service Fabric tillstånds känsliga Service Fabric:/system/InfastructureService/<NODE_TYPE_NAME> körs på varje nodtyp som har silver eller högre hållbarhet. Det är den enda system tjänst som stöds för att köras i Azure på någon av dina kluster Node-typer.

> [!IMPORTANT]
> Service Fabric automatisk skalning stöder `Default` `NewestVM` [skalnings uppsättningar för](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)virtuella datorers skalnings uppsättning.

## <a name="vertical-scaling-considerations"></a>Överväganden vid vertikal skalning

[Lodrät skalning](./virtual-machine-scale-set-scale-node-type-scale-out.md) en nodtyp i Azure Service Fabric kräver ett antal steg och överväganden. Ett exempel:

* Klustret måste vara felfritt innan skalningen. Annars kommer du att göra klustret ytterligare.
* Silver hållbarhets nivån eller större krävs för alla Service Fabric klusternoder som är värdar för tillstånds känsliga tjänster.

> [!NOTE]
> Den primära nodtypen som är värd för tillstånds känsliga Service Fabric system tjänster måste vara silver hållbarhets nivå eller större. När du har aktiverat silver tålighet är kluster åtgärder som uppgraderingar, tillägg eller borttagning av noder och så vidare långsammare eftersom systemet optimerar för data säkerhet över åtgärds hastigheten.

Vertikal skalning en skalnings uppsättning för virtuella datorer genom att helt enkelt ändra resurs-SKU: n är en destruktiv åtgärd, eftersom den återavbildning av värdarna på så vis tar bort alla lokalt sparade tillstånd. I stället vill du skala klustret vågrätt genom att lägga till en ny skalnings uppsättning med önskad SKU och sedan migrera dina tjänster till den nya skalnings uppsättningen för att slutföra en säker lodrät skalnings åtgärd.

Klustret använder Service Fabric- [nodens egenskaper och placerings begränsningar](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) för att bestämma var du ska vara värd för programmets tjänster. När du skalar en primär nodtyp lodrätt, distribuerar du en andra primär nodtyp och anger sedan ( `"isPrimary": false` ) på den ursprungliga primära nodtypen och fortsätter att inaktivera dess noder och tar bort dess skalnings uppsättning och dess relaterade resurser. Mer information finns i [skala upp en Service Fabric primär nodtyp för kluster](service-fabric-scale-up-primary-node-type.md).

> [!NOTE]
> Verifiera alltid åtgärder i test miljöer innan du försöker göra ändringar i produktions miljön. Som standard har Service Fabric kluster system tjänster en placerings begränsning som bara är den primära nodtypen för målet.

Med de egenskaper för Node och placering som deklarerats utför du följande steg en VM-instans i taget. Detta gör att system tjänsterna (och dina tillstånds känsliga tjänster) kan stängas av på ett smidigt sätt på den virtuella dator instans som du tar bort när nya repliker skapas någon annan stans.

1. Från PowerShell kör `Disable-ServiceFabricNode` du med avsikt `RemoveNode` att inaktivera noden som du ska ta bort. Ta bort nodtypen som har det högsta värdet. Om du till exempel har ett kluster med sex noder tar du bort den virtuella dator instansen "MyNodeType_5".
2. Kör `Get-ServiceFabricNode` för att kontrol lera att noden har övergått till inaktive rad. Om inte, vänta tills noden är inaktive rad. Detta kan ta några timmar för varje nod. Fortsätt inte förrän noden har övergått till inaktive rad.
3. Minska antalet virtuella datorer med ett i den nodtypen. Den högsta virtuella dator instansen tas nu bort.
4. Upprepa steg 1 till 3 efter behov, men skala aldrig upp antalet instanser i de primära noderna under färre än vad Tillförlitlighets nivån garanterar. Se [planera Service Fabric kluster kapacitet](./service-fabric-cluster-capacity.md) för en lista över rekommenderade instanser.
5. När alla virtuella datorer är borta (representeras som "nere") visas ett fel tillstånd i Fabric:/system/InfrastructureService/[Node Name]. Sedan kan du uppdatera kluster resursen för att ta bort nodtypen. Du kan antingen använda distribution av ARM-mallen eller redigera kluster resursen via [Azure Resource Manager](https://resources.azure.com). Detta startar en kluster uppgradering som tar bort tjänsten Fabric:/system/InfrastructureService/[Node Type] som är i fel tillstånd.
 6. När du vill ta bort VMScaleSet kan du fortfarande se noderna som "nere" från Service Fabric Explorer Visa. Det sista steget är att rensa dem med `Remove-ServiceFabricNodeState` kommandot.

## <a name="horizontal-scaling"></a>Horisontell skalning

Du kan göra horisontell skalning antingen [manuellt](./service-fabric-cluster-scale-in-out.md) eller [via programmering](./service-fabric-cluster-programmatic-scaling.md).

> [!NOTE]
> Om du skalar en nodtyp som har silver eller guld tålighet blir skalningen långsam.

### <a name="scaling-out"></a>Skala ut

Skala ut ett Service Fabric kluster genom att öka antalet instanser för en viss skalnings uppsättning för virtuella datorer. Du kan skala ut program mässigt genom att använda `AzureClient` och ID: t för önskad skalnings uppsättning för att öka kapaciteten.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Om du vill skala ut manuellt uppdaterar du kapaciteten i SKU-egenskapen för önskad resurs för [skalnings uppsättning för virtuell dator](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Skalning i

Skalning i kräver mer hänsyn än att skala ut. Exempel:

* Service Fabric system tjänster körs i den primära nodtypen i klustret. Stäng aldrig av eller skala antalet instanser för den nodtypen så att du har färre instanser än vad Tillförlitlighets nivån garanterar. 
* För en tillstånds känslig tjänst behöver du ett visst antal noder som alltid är upp till att upprätthålla tillgänglighet och bevara statusen för din tjänst. Du behöver minst ett antal noder som motsvarar antalet mål replik uppsättningar för partitionen eller tjänsten.

Följ dessa steg om du vill skala i manuellt:

1. Från PowerShell kör `Disable-ServiceFabricNode` du med avsikt `RemoveNode` att inaktivera noden som du ska ta bort. Ta bort nodtypen som har det högsta värdet. Om du till exempel har ett kluster med sex noder tar du bort den virtuella dator instansen "MyNodeType_5".
2. Kör `Get-ServiceFabricNode` för att kontrol lera att noden har övergått till inaktive rad. Om inte, vänta tills noden är inaktive rad. Detta kan ta några timmar för varje nod. Fortsätt inte förrän noden har övergått till inaktive rad.
3. Minska antalet virtuella datorer med ett i den nodtypen. Den högsta virtuella dator instansen tas nu bort.
4. Upprepa steg 1 till 3 efter behov tills du har etablerat den kapacitet du önskar. Skala inte antalet instanser i de primära nodtypen till mindre än vad garanti nivån garanterar. Se [planera Service Fabric kluster kapacitet](./service-fabric-cluster-capacity.md) för en lista över rekommenderade instanser.

Om du vill skala i manuellt uppdaterar du kapaciteten i SKU-egenskapen för önskad resurs för [skalnings uppsättning för virtuell dator](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Du måste förbereda noden för avstängning för skalning i program mässigt. Hitta noden som ska tas bort (den högsta instans-noden). Ett exempel:

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Inaktivera och ta bort noden genom att använda samma `FabricClient` instans ( `client` i det här fallet) och Node-instansen ( `instanceIdString` i det här fallet) som du använde i föregående kod:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> När du skalar i ett kluster ser du att den borttagna noden/VM-instansen visas i ett ohälsosamt tillstånd i Service Fabric Explorer. En förklaring av det här problemet finns [i beteenden som du kan studera i Service Fabric Explorer](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). Du kan:
> * Anropa [kommandot Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) med rätt nodnamn.
> * Distribuera [Service Fabric AutoScale Helper-programmet](https://github.com/Azure/service-fabric-autoscale-helper/) i klustret. Det här programmet ser till att de skalade noderna rensas från Service Fabric Explorer.

## <a name="reliability-levels"></a>Tillförlitlighets nivåer

[Tillförlitlighets nivån](./service-fabric-cluster-capacity.md) är en egenskap för Service Fabric kluster resurs. Den kan inte konfigureras på olika sätt för enskilda nodtyper. Den styr replikeringstrafiken för system tjänsterna för klustret och är en inställning på kluster resurs nivån. 

Tillförlitlighets nivån avgör det lägsta antalet noder som den primära nodtypen måste ha. Tillförlitlighets nivån kan ha följande värden:

* Platina: kör System tjänsterna med en mål replik uppsättning antal sju och nio startnoder.
* Guld: kör System tjänsterna med ett mål replik uppsättnings antal och sju startnoder.
* Silver: kör System tjänsterna med ett mål replik uppsättnings antal fem och fem startnoder.
* Brons: kör System tjänsterna med ett mål replik uppsättnings antal och tre startnoder.

Den minsta rekommenderade Tillförlitlighets nivån är silver.

Tillförlitlighets nivån anges i avsnittet Egenskaper i [resursen Microsoft. ServiceFabric/Clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters), så här:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Hållbarhets nivåer

> [!WARNING]
> Nodtyper som kör med brons hållbarhet får _inga privilegier_. Infrastruktur jobb som påverkar dina tillstånds lösa arbets belastningar kommer inte att stoppas eller fördröjas, vilket kan påverka dina arbets belastningar. 
>
> Använd endast brons-hållbarhet för nodtyper som kör tillstånds lösa arbets belastningar. För produktions arbets belastningar kör du silver eller högre för att säkerställa tillstånds konsekvens. Välj rätt tillförlitlighet baserat på vägledningen i dokumentationen för [kapacitets planering](./service-fabric-cluster-capacity.md).

Hållbarhets nivån måste anges i två resurser. Det finns en tilläggs profil för den [virtuella datorns skalnings uppsättnings resurs](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Den andra resursen finns under `nodeTypes` i [resursen Microsoft. ServiceFabric/Clusters](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric skapa kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Läs mer om [Service Fabric support alternativ](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
