---
title: Simulera felen i Azure mikrotjänster
description: Den här artikeln pratar om de testnings åtgärder som finns i Microsoft Azure Service Fabric.
author: georgewallace
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: aea918f75ad9c8aa58b5201d2fdd967794085438
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547085"
---
# <a name="testability-actions"></a>Test åtgärder
För att simulera en otillförlitlig infrastruktur ger Azure Service Fabric dig, utvecklaren med olika metoder för att simulera olika verkliga problem och tillstånds över gångar. De visas som testnings åtgärder. Åtgärderna är de lågnivå-API: er som orsakar en speciell fel inmatning, tillstånds över gång eller validering. Genom att kombinera dessa åtgärder kan du skriva omfattande test scenarier för dina tjänster.

Service Fabric innehåller några vanliga test scenarier som består av dessa åtgärder. Vi rekommenderar starkt att du använder dessa inbyggda scenarier, som väljs noggrant för att testa vanliga tillstånds över gångar och felfall. Åtgärder kan dock användas för att skapa anpassade test scenarier när du vill lägga till täckning för scenarier som inte omfattas av de inbyggda scenarierna eller som är anpassade för ditt program.

C#-implementeringar av åtgärderna finns i System.Fabric.dll sammansättningen. System Fabric PowerShell-modulen finns i Microsoft.ServiceFabric.Powershell.dll sammansättningen. Som en del av Runtime-installationen installeras ServiceFabric PowerShell-modulen för att möjliggöra enkel användning.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Åtgärder som är feltoleranta eller som inte går att åtgärda
Test åtgärder klassificeras i två större buckets:

* Misslyckade fel: de här felen simulerar fel som att datorn startas om och processen kraschar. I sådana fall av haverier stoppas körnings kontexten för processen plötsligt. Detta innebär att ingen rensning av tillstånd kan köras innan programmet startas igen.
* Fel: de här felen simulerar åtgärdade åtgärder, t. ex. replikering och sjunker av belastnings utjämning. I sådana fall får tjänsten ett meddelande om stängningen och kan rensa den innan den avslutas.

För bättre kvalitets validering kör du tjänst-och affärs arbets belastningen samtidigt som du inducing olika och misslyckade fel. Misslyckade fel i scenarier där tjänst processen plötsligt avslutas mitt i ett arbets flöde. Detta testar återställnings Sök vägen när tjänst repliken har återställts med Service Fabric. Detta hjälper till att testa data konsekvensen och om tjänstens tillstånd upprätthålls på rätt sätt efter fel. Den andra uppsättningen fel (problem som är felfritt) testar att tjänsten fungerar korrekt för replikeringar som flyttas runt med Service Fabric. Den här test hanteringen av annullering i RunAsync-metoden. Tjänsten måste kontrol lera om den token som angetts har angetts, spara dess tillstånd korrekt och avsluta RunAsync-metoden.

## <a name="testability-actions-list"></a>Åtgärds lista för testning
| Åtgärd | Beskrivning | Hanterat API | PowerShell-cmdlet | Korrekt/oäkta fel |
| --- | --- | --- | --- | --- |
| CleanTestState |Tar bort alla test tillstånd från klustret i händelse av en felaktig avstängning av test driv rutinen. |CleanTestStateAsync |Remove-ServiceFabricTestState |Inte tillämpligt |
| InvokeDataLoss |Inducerar data förlust i en tjänstmall. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Korrekt |
| InvokeQuorumLoss |Placerar en tilldelad tillstånds känslig tjänst partition i kvorum förlust. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Korrekt |
| En moveprimary |Flyttar den angivna primära repliken av en tillstånds känslig tjänst till den angivna klusternoden. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Korrekt |
| MoveSecondary |Flyttar den aktuella sekundära repliken av en tillstånds känslig tjänst till en annan klusternod. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Korrekt |
| RemoveReplica |Simulerar ett replik fel genom att ta bort en replik från ett kluster. Detta stänger replikeringen och kommer att övergå till rollen "ingen", vilket tar bort alla dess status från klustret. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Korrekt |
| RestartDeployedCodePackage |Simulerar ett kod paket process fel genom att starta om ett kod paket som har distribuerats på en nod i ett kluster. Detta avbryter kod paket processen, som startar om alla användare tjänst repliker som finns i den processen. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Okontrollerad |
| RestartNode |Simulerar ett fel med en Service Fabric klusternod genom att starta om en nod. |RestartNodeAsync |Restart-ServiceFabricNode |Okontrollerad |
| RestartPartition |Simulerar ett inaktive rad-eller kluster inaktive rad-scenario genom att starta om några eller alla repliker av en partition. |RestartPartitionAsync |Restart-ServiceFabricPartition |Korrekt |
| RestartReplica |Simulerar ett replik fel genom att starta om en bestående replik i ett kluster, stänga replikeringen och sedan öppna den igen. |RestartReplicaAsync |Restart-ServiceFabricReplica |Korrekt |
| StartNode |Startar en nod i ett kluster som redan har stoppats. |StartNodeAsync |Start-ServiceFabricNode |Inte tillämpligt |
| StopNode |Simulerar ett nodfel genom att stoppa en nod i ett kluster. Noden kommer att stanna kvar tills StartNode anropas. |StopNodeAsync |Stop-ServiceFabricNode |Okontrollerad |
| ValidateApplication |Kontrollerar tillgängligheten och hälsan för alla Service Fabric tjänster i ett program, vanligt vis efter inducing av fel i systemet. |ValidateApplicationAsync |Test-ServiceFabricApplication |Inte tillämpligt |
| ValidateService |Verifierar tillgängligheten och hälsan för en Service Fabric tjänst, vanligt vis efter inducing av fel i systemet. |ValidateServiceAsync |Test-ServiceFabricService |Inte tillämpligt |

## <a name="running-a-testability-action-using-powershell"></a>Köra en test åtgärd med PowerShell
Den här självstudien visar hur du kör en test åtgärd med hjälp av PowerShell. Du får lära dig hur du kör en test åtgärd mot ett lokalt kluster (en enda ruta) eller ett Azure-kluster. Microsoft.Fabric.Powershell.dll--Service Fabric PowerShell-modulen--installeras automatiskt när du installerar Microsoft Service Fabric MSI. Modulen läses in automatiskt när du öppnar en PowerShell-prompt.

Själv studie segment:

* [Kör en åtgärd mot ett kluster med en enda ruta](#run-an-action-against-a-one-box-cluster)
* [Köra en åtgärd mot ett Azure-kluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Kör en åtgärd mot ett kluster med en enda ruta
Om du vill köra en test åtgärd mot ett lokalt kluster ansluter du först till klustret och öppnar PowerShell-prompten i administratörs läge. Låt oss titta på åtgärden **restart-ServiceFabricNode** .

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Här är åtgärden **restart-ServiceFabricNode** körs på en nod med namnet "Nod1". Slut för ande läget anger att det inte ska verifiera om åtgärden restart-Node faktiskt lyckades. Om du anger slut för ande läget som "verifiera" kommer det att verifiera om åtgärden startats om i verkligheten. I stället för att ange noden direkt efter dess namn, kan du ange den via en partitionsnyckel och typen av replik, enligt följande:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** ska användas för att starta om en Service Fabric-nod i ett kluster. Detta stoppar Fabric.exe processen, vilket kommer att starta om alla system tjänster och användar tjänst repliker som finns på noden. Genom att använda det här API: et för att testa tjänsten kan du få hjälp med att återställa buggar vid redundans. Den hjälper till att simulera nodfel i klustret.

Följande skärm bild visar kommandot **restart-ServiceFabricNode** testable i praktiken.

![Skärm bild som visar hur du kör Restart-ServiceFabricNode-kommandot i PowerShell.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Utdata från den första **Get-ServiceFabricNode** (en cmdlet från Service Fabric PowerShell-modulen) visar att det lokala klustret har fem noder: Node. 1 till Node. 5. När cmdleten **restart-ServiceFabricNode** körs på noden, heter Node. 4, ser vi att nodens drift tid har återställts.

### <a name="run-an-action-against-an-azure-cluster"></a>Köra en åtgärd mot ett Azure-kluster
Att köra en test åtgärd (genom att använda PowerShell) mot ett Azure-kluster liknar att köra åtgärden mot ett lokalt kluster. Den enda skillnaden är att innan du kan köra åtgärden, i stället för att ansluta till det lokala klustret, måste du först ansluta till Azure-klustret.

## <a name="running-a-testability-action-using-c35"></a>Köra en test åtgärd med hjälp av C&#35;
Om du vill köra en test åtgärd genom att använda C# måste du först ansluta till klustret med hjälp av FabricClient. Hämta sedan de parametrar som krävs för att köra åtgärden. Olika parametrar kan användas för att köra samma åtgärd.
Att titta på RestartServiceFabricNode-åtgärden är ett sätt att köra den med hjälp av Node-informationen (nodnamn och instans-ID för Node) i klustret.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parameter förklaring:

* **CompleteMode** anger att läget inte ska verifiera om åtgärden för omstart faktiskt lyckades. Om du anger slut för ande läget som "verifiera" kommer det att verifiera om åtgärden startats om i verkligheten.  
* **OperationTimeout** anger hur lång tid som ska slutföras innan ett TimeoutException-undantag genereras.
* **CancellationToken** tillåter att ett väntande anrop avbryts.

I stället för att ange noden direkt efter dess namn, kan du ange den via en partitionsnyckel och typen av replik.

Mer information finns i PartitionSelector och ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector och ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector är en hjälp som exponeras för testning och används för att välja en speciell partition där du vill utföra någon av test åtgärder. Den kan användas för att välja en viss partition om partitions-ID: t är känt i förväg. Eller också kan du ange partitionsnyckel och åtgärden kommer att matcha partitions-ID: t internt. Du kan också välja att välja en slumpmässig partition.

Om du vill använda den här hjälpen skapar du PartitionSelector-objektet och väljer partitionen genom att använda någon av metoderna Select *. Skicka sedan PartitionSelector-objektet till det API som kräver det. Om inget alternativ är markerat används en slumpmässig partition som standard.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector är en hjälpare som är utsatt för testning och används för att välja en replik som du kan använda för att utföra test åtgärder. Den kan användas för att välja en viss replik om replik-ID: t är känt i förväg. Dessutom har du möjlighet att välja en primär replik eller en slumpmässig sekundär. ReplicaSelector härleds från PartitionSelector, så du måste välja både repliken och den partition där du vill utföra testnings åtgärden.

Om du vill använda den här hjälpen skapar du ett ReplicaSelector-objekt och anger hur du vill välja repliken och partitionen. Du kan sedan skicka den till det API som kräver det. Om inget alternativ är markerat används en slumpmässig replik och slumpmässig partition som standard.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Nästa steg
* [Testnings scenarier](service-fabric-testability-scenarios.md)
* Så här testar du tjänsten
  * [Simulera problem under tjänst arbets belastningar](service-fabric-testability-workload-tests.md)
  * [Kommunikations problem från tjänst till tjänst](service-fabric-testability-scenarios-service-communication.md)

