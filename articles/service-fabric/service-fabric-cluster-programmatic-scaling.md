---
title: Skalning av Azure Service Fabric-program
description: Skala ett Azure Service Fabric-kluster i program mässigt, enligt anpassade utlösare
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648216"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Skala ett Service Fabric kluster program mässigt 

Service Fabric kluster som körs i Azure skapas ovanpå virtuella datorers skalnings uppsättningar.  [Kluster skalning](./service-fabric-cluster-scale-in-out.md) beskriver hur Service Fabric kluster kan skalas antingen manuellt eller med regler för automatisk skalning. Den här artikeln beskriver hur du hanterar autentiseringsuppgifter och skalar ett kluster i eller ut med hjälp av Fluent Azure Compute SDK, som är ett mer avancerat scenario. För en översikt läser du [programmerings metoder för samordning av Azure skalnings åtgärder](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Hantera autentiseringsuppgifter
En utmaning att skriva en tjänst för att hantera skalning är att tjänsten måste kunna komma åt resurser för skalnings uppsättningar för virtuella datorer utan en interaktiv inloggning. Det är enkelt att komma åt Service Fabric-klustret om skalnings tjänsten ändrar sitt eget Service Fabric-program, men autentiseringsuppgifter krävs för att få åtkomst till skalnings uppsättningen. Du kan använda ett [huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli) som skapats med [Azure CLI](https://github.com/azure/azure-cli)för att logga in.

Du kan skapa ett huvud namn för tjänsten med följande steg:

1. Logga in på Azure CLI ( `az login` ) som en användare med åtkomst till den virtuella datorns skal uppsättning
2. Skapa tjänstens huvud namn med `az ad sp create-for-rbac`
    1. Anteckna appId (kallas "klient-ID" någon annan stans), namn, lösen ord och klient organisation för senare användning.
    2. Du kommer också att behöva ditt prenumerations-ID, som kan visas med `az account list`

I Fluent Compute-biblioteket kan du logga in med dessa autentiseringsuppgifter på följande sätt (Observera att de viktigaste Fluent `IAzure` -typerna i Azure som finns i [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) -paketet):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

När du har loggat in kan du fråga skalnings uppsättnings instans antal via `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` .

## <a name="scaling-out"></a>Skala ut
Med hjälp av Fluent Azure Compute SDK kan du lägga till instanser i den virtuella datorns skalnings uppsättning med bara några anrop –

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

En skalnings uppsättnings storlek för virtuell dator kan också hanteras med PowerShell-cmdletar. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) kan hämta objektet för skalnings uppsättning för virtuella datorer. Den aktuella kapaciteten är tillgänglig via `.sku.capacity` egenskapen. När du har ändrat kapaciteten till det önskade värdet kan du uppdatera den virtuella datorns skalnings uppsättning i Azure med [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) kommandot.

När du lägger till en nod manuellt bör du lägga till en skalnings uppsättnings instans som behövs för att starta en ny Service Fabric nod eftersom mallen för skalnings uppsättning innehåller tillägg för att automatiskt ansluta nya instanser till Service Fabric klustret. 

## <a name="scaling-in"></a>Skalning i

Skalning i liknar skala ut. De faktiska ändringarna i skalnings uppsättningen för virtuella datorer är praktiskt taget desamma. Men som tidigare diskuterats rensar Service Fabric bara bort borttagna noder automatiskt med en hållbarhet på guld eller silver. Därför är det i brons-hållbarhets skalan nödvändigt att interagera med Service Fabric-klustret för att stänga av noden som ska tas bort och sedan ta bort dess tillstånd.

Att förbereda noden för avstängning innebär att hitta noden som ska tas bort (den senast tillagda instansen av virtuella datorers skalnings uppsättning) och inaktivera den. Instanser av skalnings uppsättning för virtuella datorer numreras i den ordning som de läggs till, så att de nya noderna kan hittas genom att jämföra antalet suffix i nodernas namn (som matchar de underliggande instans namnen för den virtuella datorns skal uppsättning). 

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

När noden som ska tas bort, kan den inaktive ras och tas bort med samma `FabricClient` instans och `IAzure` instansen från tidigare.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Precis som med utskalning kan PowerShell-cmdletar för att ändra den virtuella datorns skalnings uppsättnings kapacitet också användas här om en skript metod är bättre. När den virtuella dator instansen har tagits bort kan Service Fabric Node status tas bort.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Nästa steg

Kom igång med att implementera din egen logik för automatisk skalning, och bekanta dig med följande begrepp och användbara API: er:

- [Skalning manuellt eller med regler för automatisk skalning](./service-fabric-cluster-scale-in-out.md)
- [Azures hanterings bibliotek för .net](https://github.com/Azure/azure-libraries-for-net) (användbart för att interagera med ett Service Fabric klusters underliggande skalnings uppsättningar för virtuella datorer)
- [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient) (användbart för att interagera med ett Service Fabric kluster och dess noder)
