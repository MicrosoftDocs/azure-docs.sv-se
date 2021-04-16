---
title: Köra aktiviteter samtidigt för att maximera användningen av Batch-beräkningsnoder
description: Öka effektiviteten och sänk kostnaderna genom att använda färre beräkningsnoder och köra aktiviteter parallellt på varje nod i en Azure Batch pool
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389306"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Köra aktiviteter samtidigt för att maximera användningen av Batch-beräkningsnoder

Du kan maximera resursanvändningen på ett mindre antal beräkningsnoder i poolen genom att köra fler än en aktivitet samtidigt på varje nod.

Vissa scenarier fungerar bäst med alla resurser för en nod som är dedikerade till en enskild uppgift, men vissa arbetsbelastningar kan se kortare jobbtider och lägre kostnader när flera aktiviteter delar dessa resurser. Beakta följande scenarier:

- **Minimera dataöverföring för** uppgifter som kan dela data. Du kan avsevärt minska kostnaderna för dataöverföring genom att kopiera delade data till ett mindre antal noder och sedan köra aktiviteter parallellt på varje nod. Detta gäller särskilt om de data som ska kopieras till varje nod måste överföras mellan geografiska regioner.
- **Maximera minnesanvändningen** för uppgifter som kräver en stor mängd minne, men endast under korta tidsperioder och vid varierande tidpunkter under körningen. Du kan använda färre, men större, beräkningsnoder med mer minne för att effektivt hantera sådana toppar. Dessa noder har flera aktiviteter som körs parallellt på varje nod, men varje uppgift kan dra nytta av nodernas stora minne vid olika tidpunkter.
- **Minimera gränser för nodnummer** när kommunikation mellan noder krävs i en pool. Pooler som har konfigurerats för kommunikation mellan noder är för närvarande begränsade till 50 beräkningsnoder. Om varje nod i en sådan pool kan köra aktiviteter parallellt kan ett större antal aktiviteter köras samtidigt.
- **Replikera ett lokalt beräkningskluster,** till exempel när du först flyttar en beräkningsmiljö till Azure. Om din aktuella lokala lösning kör flera aktiviteter per beräkningsnod kan du öka det maximala antalet nodaktiviteter för att bättre spegla konfigurationen.

## <a name="example-scenario"></a>Exempelscenario

Tänk dig till exempel ett aktivitetsprogram med processor- och minneskrav så att [Standard \_ D1-noderna](../cloud-services/cloud-services-sizes-specs.md#d-series) räcker. Men för att slutföra jobbet inom den tid som krävs krävs 1 000 av dessa noder.

I stället för att använda Standard D1-noder som har 1 processorkärna kan du använda \_ [Standard \_ D14-noder](../cloud-services/cloud-services-sizes-specs.md#d-series) som har 16 kärnor vardera och aktivera parallell uppgiftskörning. Det innebär att 16 gånger färre noder kan användas – i stället för 1 000 noder krävs bara 63. Om stora programfiler eller referensdata krävs för varje nod förbättras jobbets varaktighet och effektivitet, eftersom data endast kopieras till 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera parallell uppgiftskörning

Du konfigurerar beräkningsnoder för parallell uppgiftskörning på poolnivå. Med Batch .NET-biblioteket anger du egenskapen [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) när du skapar en pool. Om du använder Batch-REST API anger du [elementet taskSlotsPerNode](/rest/api/batchservice/pool/add) i begärandetexten när poolen skapas.

> [!NOTE]
> Du kan bara ange `taskSlotsPerNode` elementet och [egenskapen TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) när poolen skapas. De kan inte ändras när en pool redan har skapats.

Azure Batch kan du ange aktivitetsfack per nod upp till (4x) antalet nodkärnor. Om poolen till exempel är konfigurerad med noder av storleken "Large" (fyra kärnor) kan den vara `taskSlotsPerNode` inställd på 16. Men oavsett hur många kärnor noden har kan du inte ha fler än 256 uppgiftsplatser per nod. Mer information om antalet kärnor för var och en av nodstorlekarna finns [i Storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänstbegränsningar finns [i Kvoter och begränsningar för Azure Batch tjänsten](batch-quota-limit.md).

> [!TIP]
> Se till att ta hänsyn till värdet `taskSlotsPerNode` när du skapar en [autoskalningsformel](/rest/api/batchservice/pool/enableautoscale) för din pool. En formel som utvärderas kan till exempel `$RunningTasks` påverkas dramatiskt av en ökning av aktiviteter per nod. Mer information finns i Skala [beräkningsnoder automatiskt i en Azure Batch pool](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Ange uppgiftsdistribution

När du aktiverar samtidiga aktiviteter är det viktigt att ange hur du vill att aktiviteterna ska distribueras över noderna i poolen.

Med hjälp av [egenskapen CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) kan du ange att aktiviteter ska tilldelas jämnt över alla noder i poolen ("spridning"). Eller så kan du ange att så många aktiviteter som möjligt ska tilldelas till varje nod innan aktiviteter tilldelas till en annan nod i poolen ("packning").

Tänk dig till exempel poolen med [Standard \_ D14-noder](../cloud-services/cloud-services-sizes-specs.md#d-series) (i exemplet ovan) som är konfigurerad med ett [CloudPool.TaskSlotsPerNode-värde](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) på 16. Om [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) har konfigurerats med en [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) av *Pack* maximerar den användningen av alla 16 kärnor i varje nod och tillåter att en pool för [automatisk](batch-automatic-scaling.md) skalning tar bort oanvända noder (noder utan tilldelade uppgifter) från poolen. Detta minimerar resursanvändningen och sparar pengar.

## <a name="define-variable-slots-per-task"></a>Definiera variabelfack per uppgift

En uppgift kan definieras med [egenskapen CloudTask.RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) och ange hur många platser som krävs för att köras på en beräkningsnod. Standardvärdet är 1. Du kan ange variabla aktivitetsfack om aktiviteterna har olika vikter för resursanvändningen på beräkningsnoden. På så sätt kan varje beräkningsnod ha ett rimligt antal samtidiga aktiviteter som körs utan att överväldiga systemresurser som CPU eller minne.

För en pool med egenskapen kan du till exempel skicka processorintensiva uppgifter med flera kärnor med , medan andra aktiviteter `taskSlotsPerNode = 8` `requiredSlots = 8` kan anges till `requiredSlots = 1` . När den här blandade arbetsbelastningen schemaläggs körs de processorintensiva aktiviteterna uteslutande på sina beräkningsnoder, medan andra aktiviteter kan köras samtidigt (upp till åtta aktiviteter samtidigt) på andra noder. På så sätt kan du balansera din arbetsbelastning mellan beräkningsnoder och förbättra resursanvändningens effektivitet.

Se till att du inte anger att en uppgift `requiredSlots` ska vara större än poolens `taskSlotsPerNode` . Detta resulterar i att uppgiften aldrig kan köras. Batch-tjänsten validerar för närvarande inte den här konflikten när du skickar aktiviteter eftersom ett jobb kanske inte har en poolbunden vid överföringstiden, eller så kan den ändras till en annan pool genom att inaktivera/återaktivera.

> [!TIP]
> När du använder variabla uppgiftsfack är det möjligt att stora uppgifter med fler nödvändiga platser tillfälligt kan schemaläggas eftersom det inte finns tillräckligt med platser på en beräkningsnod, även om det fortfarande finns inaktiva platser på vissa noder. Du kan öka jobbprioritet för dessa aktiviteter för att öka deras chans att konkurrera om tillgängliga platser på noder.
>
> Batch-tjänsten skickar [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) när det inte går att schemalägga att en aktivitet ska köras och fortsätter att försöka schemalägga igen tills nödvändiga platser blir tillgängliga. Du kan lyssna på händelsen för att identifiera potentiella problem med schemaläggning av aktiviteter och åtgärda detta.

## <a name="batch-net-example"></a>Batch .NET-exempel

Följande [kodfragment för Batch .NET](/dotnet/api/microsoft.azure.batch) API visar hur du skapar en pool med flera uppgiftsfack per nod och hur du skickar en uppgift med nödvändiga platser.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Skapa en pool med flera uppgiftsfack per nod

Det här kodfragmentet visar en begäran om att skapa en pool som innehåller fyra noder, med fyra tillåtna uppgiftsplatser per nod. Den anger en schemaläggningsprincip för aktiviteter som fyller varje nod med aktiviteter innan aktiviteter tilldelas till en annan nod i poolen.

Mer information om hur du lägger till pooler med hjälp av Batch .NET API finns [i BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Skapa en uppgift med nödvändiga platser

Det här kodfragmentet skapar en uppgift som inte är `requiredSlots` standard. Den här aktiviteten körs bara när det finns tillräckligt med lediga platser på en beräkningsnod.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Lista beräkningsnoder med antal för aktiviteter och platser som körs

Det här kodfragmentet visar en lista över alla beräkningsnoder i poolen och skriver ut antalet för att köra aktiviteter och uppgiftsplatser per nod.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Lista uppgiftsantal för jobbet

Det här kodfragmentet hämtar antalet uppgifter för jobbet, vilket inkluderar antalet aktiviteter och aktivitetsfack per aktivitetstillstånd.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch REST-exempel

Följande [Batch REST](/rest/api/batchservice/) API-kodfragment visar hur du skapar en pool med flera uppgiftsplatser per nod och hur du skickar en uppgift med nödvändiga platser.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Skapa en pool med flera uppgiftsplatser per nod

Det här kodfragmentet visar en begäran om att skapa en pool som innehåller två stora noder med högst fyra aktiviteter per nod.

Mer information om hur du lägger till pooler med hjälp av REST API finns i [Lägga till en pool i ett konto](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Skapa en uppgift med nödvändiga platser

Det här kodfragmentet visar en begäran om att lägga till en uppgift som inte är `requiredSlots` standard. Den här aktiviteten körs bara när det finns tillräckligt med lediga platser på beräkningsnoden.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Kodexempel på GitHub

[ParallelTasks-projektet](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) på GitHub illustrerar användningen av egenskapen [CloudPool.TaskSlotsPerNode.](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode)

Det här C#-konsolprogrammet använder [Batch .NET-biblioteket](/dotnet/api/microsoft.azure.batch) för att skapa en pool med en eller flera beräkningsnoder. Den kör ett konfigurerbart antal aktiviteter på dessa noder för att simulera en variabel belastning. Utdata från programmet visar vilka noder som körde varje uppgift. Programmet innehåller också en sammanfattning av jobbparametrarna och varaktigheten.

Nedan visas till exempel sammanfattningsdelen av utdata från två olika körningar av ParallelTasks-exempelprogrammet. Jobbvaraktigheterna som visas här inkluderar inte skapandetiden för poolen, eftersom varje jobb  skickades till en tidigare skapad pool vars beräkningsnoder var i inaktivt tillstånd vid inskickningstiden.

Den första körningen av exempelprogrammet visar att jobbets varaktighet är över 30 minuter med en enda nod i poolen och standardinställningen för en aktivitet per nod.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den andra körningen av exemplet visar en betydande minskning av jobbets varaktighet. Det beror på att poolen har konfigurerats med fyra aktiviteter per nod, vilket gör att parallell uppgiftskörning kan slutföra jobbet nästan en fjärdedel av tiden.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Nästa steg

- Prova [den Batch Explorer Heat](https://azure.github.io/BatchExplorer/) Map. Batch Explorer är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. När du kör [exempelprogrammet ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) kan du med Batch Explorer Heat Map enkelt visualisera körningen av parallella uppgifter på varje nod.
- Utforska [Azure Batch exempel på GitHub](https://github.com/Azure/azure-batch-samples).
- Läs mer om [Batch-aktivitetsberoenden](batch-task-dependencies.md).
