---
title: Kör uppgifter samtidigt för att maximera användningen av batch Compute-noder
description: Öka effektiviteten och sänk kostnaderna genom att använda färre Compute-noder och köra aktiviteter parallellt på varje nod i en Azure Batch pool
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 8bc9f03f05d52df6e400be5c57033ab2a38fa8eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92102973"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Kör uppgifter samtidigt för att maximera användningen av batch Compute-noder

Du kan maximera resursanvändningen på ett mindre antal datornoder i poolen genom att köra mer än en aktivitet samtidigt på varje nod.

Vissa scenarier fungerar bäst med alla noders resurser som är dedikerade till en enda uppgift, men vissa arbets belastningar kan se kortare jobb tider och lägre kostnader när flera aktiviteter delar dessa resurser:

- **Minimera data överföringen** för uppgifter som kan dela data. Du kan drastiskt minska avgifterna för data överföring genom att kopiera delade data till ett mindre antal noder och sedan köra aktiviteter parallellt på varje nod. Detta gäller särskilt om data som ska kopieras till varje nod måste överföras mellan geografiska regioner.
- **Maximera minnes användningen** för aktiviteter som kräver stor mängd minne, men endast under korta tids perioder och vid varierande tidpunkter under körningen. Du kan använda färre, men större Compute-noder med mer minne för att effektivt hantera sådana toppar. De här noderna skulle ha flera aktiviteter som körs parallellt på varje nod, men varje aktivitet skulle dra nytta av nodernas plentiful minne vid olika tidpunkter.
- **Minska antalet nodnummer** vid kommunikation mellan noder i en pool. För närvarande är pooler som kon figurer ATS för kommunikation mellan noder begränsade till 50-datornoder. Om varje nod i en sådan pool kan köra aktiviteter parallellt, kan ett större antal uppgifter köras samtidigt.
- **Replikera ett lokalt beräknings kluster**, till exempel när du först flyttar en beräknings miljö till Azure. Om din aktuella lokala lösning kör flera aktiviteter per Compute-nod kan du öka det maximala antalet noder för att bättre kunna spegla konfigurationen.

## <a name="example-scenario"></a>Exempelscenario

Ett exempel är att anta ett aktivitets program med processor-och minnes krav, så att [standard \_ D1](../cloud-services/cloud-services-sizes-specs.md) -noderna är tillräckliga. För att kunna slutföra jobbet inom den tid som krävs behövs dock 1 000 av de här noderna.

I stället för att använda standard \_ D1-noder som har 1 processor kärna kan du använda [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -noder som har 16 kärnor och aktivera parallell körning av aktiviteter. Det innebär att *16 gånger färre noder* kan användas – i stället för 1 000-noder krävs bara 63. Om stora programfiler eller referens data krävs för varje nod förbättras jobb varaktigheten och effektiviteten, eftersom data kopieras till endast 63 noder.

## <a name="enable-parallel-task-execution"></a>Aktivera parallell körning av uppgift

Du konfigurerar Compute-noder för parallell körning av aktiviteter på Poolnivå. Med batch .NET-biblioteket ställer du in egenskapen [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) när du skapar en pool. Om du använder batch-REST API anger du [taskSlotsPerNode](/rest/api/batchservice/pool/add) -elementet i begär ande texten när du skapar en pool.

> [!NOTE]
> Du kan endast ange `taskSlotsPerNode` element-och [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) -egenskapen när du skapar en pool. De kan inte ändras efter att en pool redan har skapats.

Med Azure Batch kan du ange aktivitets platser per nod upp till (4x) antalet Node-kärnor. Om poolen till exempel har kon figurer ATS med noder med storleken "stor" (fyra kärnor) `taskSlotsPerNode` kan du ange 16. Men oavsett hur många kärnor noden har kan du inte ha fler än 256 aktivitets platser per nod. Mer information om antalet kärnor för varje nods storlek finns i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Mer information om tjänst begränsningar finns i [kvoter och begränsningar för tjänsten Azure Batch](batch-quota-limit.md).

> [!TIP]
> Se till att ta hänsyn till `taskSlotsPerNode` värdet när du skapar en [autoskalning-formel](/rest/api/batchservice/pool/enableautoscale) för poolen. En formel som utvärderar kan till exempel `$RunningTasks` dramatiskt påverkas av en ökning av aktiviteter per nod. Mer information finns i [automatisk skalning av Compute-noder i en Azure Batch pool](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Ange aktivitets distribution

När du aktiverar samtidiga uppgifter är det viktigt att ange hur du vill att aktiviteterna ska distribueras mellan noderna i poolen.

Genom att använda egenskapen [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) kan du ange att aktiviteterna ska tilldelas jämnt över alla noder i poolen ("spridning"). Du kan också ange att så många aktiviteter som möjligt ska tilldelas varje nod innan aktiviteter tilldelas till en annan nod i poolen ("packning").

Anta till exempel poolen med [standard \_ D14](../cloud-services/cloud-services-sizes-specs.md) -noder (i exemplet ovan) som är konfigurerad med ett [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) -värde på 16. Om [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool) har kon figurer ATS med [ett ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) - *paket* skulle det maximera användningen av alla 16 kärnor i varje nod och tillåta att en [pool](batch-automatic-scaling.md) för automatisk skalning tar bort oanvända noder (noder utan tilldelade aktiviteter) från poolen. Detta minimerar resursanvändningen och sparar pengar.

## <a name="define-variable-slots-per-task"></a>Definiera variabla platser per uppgift

En uppgift kan definieras med egenskapen [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) och ange hur många platser som krävs för att köra på en Compute-nod. Standardvärdet är 1. Du kan ställa in variabla uppgifts platser om aktiviteterna har olika vikt för resursanvändningen på Compute-noden. Detta gör att varje Compute-nod har ett rimligt antal samtidiga körnings aktiviteter utan att överbelasta system resurser som processor eller minne.

För en pool med egenskap `taskSlotsPerNode = 8` kan du till exempel skicka nödvändiga processor intensiva uppgifter med flera kärnor `requiredSlots = 8` , medan andra aktiviteter kan ställas in på `requiredSlots = 1` . När den här blandade arbets belastningen schemaläggs körs processor intensiva aktiviteter exklusivt på sina datornoder, medan andra aktiviteter kan köras samtidigt (upp till åtta aktiviteter på en gång) på andra noder. Detta hjälper dig att balansera arbets belastningen mellan datornoder och förbättra effektiviteten för resursanvändning.

> [!TIP]
> När du använder variabla aktivitets platser, är det möjligt att stora aktiviteter med fler begärda platser tillfälligt kan schemaläggas på grund av att det inte finns tillräckligt många platser tillgängliga på en Compute-nod, även om det fortfarande finns inaktiva platser på vissa noder. Du kan öka jobb prioriteten för dessa aktiviteter för att öka sin chans att konkurrera om tillgängliga platser på noder.
>
> Batch-tjänsten genererar [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) när det inte går att schemalägga en aktivitet för körning och försöker att försöka schemalägga igen tills nödvändiga platser blir tillgängliga. Du kan lyssna på händelsen för att identifiera potentiella problem med aktivitets schemaläggningen och åtgärda detta.

> [!NOTE]
> Ange inte att en aktivitet ska `requiredSlots` vara större än poolens `taskSlotsPerNode` . Detta leder till att aktiviteten aldrig kan köras. Batch-tjänsten validerar för närvarande inte den här konflikten när du skickar uppgifter eftersom ett jobb inte kan ha en pool som är kopplad till överförings tiden, eller så kan den ändras till en annan pool genom att inaktivera/återaktivera.

## <a name="batch-net-example"></a>Batch .NET-exempel

Följande [batch .net](/dotnet/api/microsoft.azure.batch) API-kodfragment visar hur du skapar en pool med flera aktivitets platser per nod och hur du skickar en uppgift med nödvändiga platser.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Skapa en pool med flera aktivitets platser per nod

Det här kodfragmentet visar en begäran om att skapa en pool som innehåller fyra noder, med fyra aktivitets platser tillåtna per nod. Den specificerar en schemaläggnings princip för aktiviteter som fyller varje nod med aktiviteter innan aktiviteter tilldelas till en annan nod i poolen.

Mer information om hur du lägger till pooler med hjälp av batch .NET-API: et finns i [metoden batchclient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Skapa en uppgift med nödvändiga platser

Det här kodfragmentet skapar en aktivitet som inte är standard `requiredSlots` . Den här uppgiften körs bara när det finns tillräckligt med lediga fack på en Compute-nod.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Lista Compute-noder med antal för att köra aktiviteter och platser

Det här kodfragmentet visar en lista över alla datornoder i poolen och skriver ut antalet för aktiviteter som körs och aktivitets platser per nod.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Lista antal aktiviteter för jobbet

Det här kodfragmentet hämtar antalet uppgifter för jobbet, som innehåller både aktiviteter och antal aktivitets platser per uppgifts tillstånd.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Batch-REST-exempel

Följande fragment i [batch rest](/rest/api/batchservice/) API-kodfragment visar hur du skapar en pool med flera aktivitets platser per nod och hur du skickar en uppgift med nödvändiga platser.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Skapa en pool med flera aktivitets platser per nod

Det här kodfragmentet visar en begäran om att skapa en pool som innehåller två stora noder med högst fyra uppgifter per nod.

Mer information om hur du lägger till pooler med hjälp av REST API finns i [lägga till en pool till ett konto](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Skapa en uppgift med nödvändiga platser

Det här kodfragmentet visar en begäran om att lägga till en aktivitet med icke-standardvärden `requiredSlots` . Den här uppgiften körs bara när det finns tillräckligt med lediga fack på Compute-noden.

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

## <a name="code-sample-on-github"></a>Kod exempel på GitHub

[ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) -projektet på GitHub illustrerar användningen av egenskapen [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) .

Det här C#-konsol programmet använder [batch .net](/dotnet/api/microsoft.azure.batch) -biblioteket för att skapa en pool med en eller flera Compute-noder. Den kör ett konfigurerbart antal aktiviteter på noderna för att simulera en variabel belastning. Utdata från programmet visar vilka noder som utförde varje aktivitet. Programmet innehåller också en sammanfattning av jobb parametrarna och varaktigheten.

Som exempel nedan är sammanfattnings delen av utdata från två olika körningar av exempel programmet ParallelTasks. Jobb varaktigheterna som visas här inkluderar inte skapandet av poolen, eftersom varje jobb skickades till en tidigare skapad pool vars datornoder var i *inaktivt* läge vid överförings tillfället.

Den första körningen av exempel programmet visar att med en enda nod i poolen och standardinställningen för en aktivitet per nod, är jobbets varaktighet över 30 minuter.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

Den andra körningen av exemplet visar en betydande minskning av jobbets varaktighet. Detta beror på att poolen har kon figurer ATS med fyra aktiviteter per nod, vilket gör att körningen av parallell aktivitet kan slutföra jobbet i nästan en fjärdedel av tiden.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Nästa steg

- Prova [batch Explorer](https://azure.github.io/BatchExplorer/) värme kartan. Batch Explorer är ett kostnads fritt, fristående klient verktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. När du kör exempel programmet [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) kan du enkelt visualisera körningen av parallella aktiviteter på varje nod genom att använda funktionen batch Explorer termisk karta.
- Utforska [Azure Batch exempel på GitHub](https://github.com/Azure/azure-batch-samples).
- Lär dig mer om [aktivitets beroenden för batch](batch-task-dependencies.md).
