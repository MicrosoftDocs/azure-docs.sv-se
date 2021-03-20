---
title: Antal tillstånd för aktiviteter och noder
description: Räkna antalet Azure Batch uppgifter och Compute-noder för att hantera och övervaka batch-lösningar.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85960596"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Övervaka batch-lösningar genom att räkna aktiviteter och noder efter tillstånd

För att övervaka och hantera storskaliga Azure Batch-lösningar kan du behöva bestämma antalet resurser i olika tillstånd. Azure Batch tillhandahåller effektiva åtgärder för att hämta antal batch-uppgifter och Compute-noder. Du kan använda dessa åtgärder i stället för ibland tids krävande List frågor som returnerar detaljerad information om stora mängder av aktiviteter eller noder.

- [Hämta aktivitets antal](/rest/api/batchservice/job/gettaskcounts) får ett sammanlagt antal aktiva, pågående och slutförda aktiviteter i ett jobb, samt uppgifter som har lyckats eller misslyckats. 

  Genom att räkna aktiviteter i varje tillstånd kan du enklare Visa jobb förloppet för en användare eller upptäcka oväntade fördröjningar eller fel som kan påverka jobbet. Hämta aktivitets antal är tillgängligt som batch service API-version 2017 -06-01.5.1 och relaterade SDK: er och verktyg.

- Antal noder i en [adresspool visar](/rest/api/batchservice/account/listpoolnodecounts) antalet dedikerade och låg prioritets-datornoder i varje pool i olika tillstånd: skapa, inaktiv, offline, omstart, omstart, avbildning, start och andra.

  Genom att räkna noder i varje tillstånd kan du fastställa när du har tillräckliga beräknings resurser för att köra dina jobb och identifiera eventuella problem med dina pooler. Antalet noder i en adresspool är tillgängligt som batch service API-version 2018 -03-01.6.1 och relaterade SDK: er och verktyg.

Observera att de tal som returneras av de här åtgärderna inte är aktuella. Om du behöver vara säker på att antalet är korrekt använder du en lista fråga för att räkna resurserna. Med list frågor kan du också få information om andra batch-resurser, till exempel program. Mer information om hur du använder filter för att lista frågor finns i [skapa frågor för att lista batch-resurser effektivt](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Antal uppgifts tillstånd

Åtgärden hämta antal aktiviteter räknas i följande tillstånd:

- **Aktiv** – en uppgift som är i kö och kan köras, men som för närvarande inte är kopplad till en Compute-nod. En aktivitet är också `active` [beroende av en överordnad uppgift](batch-task-dependencies.md) som ännu inte har slutförts. 
- **Körs** – en aktivitet som har tilldelats till en Compute-nod, men som ännu inte har slutförts. En aktivitet räknas som `running` när dess tillstånd är antingen `preparing` eller `running` , enligt vad som anges i [Hämta information om en uppgifts](/rest/api/batchservice/task/get) åtgärd.
- **Slutförd** – en aktivitet som inte längre är berättigad att köras, eftersom den antingen har slutförts eller slutat fungera och även förbrukat gränsen för återförsök. 
- **Lyckades** – en uppgift vars resultat av uppgifts körning är `success` . Batch avgör om en aktivitet har lyckats eller misslyckats genom att kontrol lera `TaskExecutionResult` egenskapen för egenskapen [executionInfo](/rest/api/batchservice/task/get) .
- **Misslyckades** En aktivitet vars resultat av uppgifts körning är `failure` .

Följande exempel på .NET-kod visar hur du hämtar antal aktiviteter efter status:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta aktivitets antal för ett jobb. 

> [!NOTE]
> API-versioner för batch-tjänsten innan 2018 -08-01.7.0 returnerar även en `validationStatus` egenskap i svars antalet Hämta uppgift. Den här egenskapen anger om batch kontrollerade antalet tillstånd för konsekvens med de tillstånd som rapporteras i API: et för List aktiviteter. Värdet `validated` anger bara att den här gruppen har kontroller ATS för konsekvens minst en gång för jobbet. Värdet för `validationStatus` egenskapen anger inte om antalet som erhåller aktivitets antal returnerar är aktuellt.

## <a name="node-state-counts"></a>Antal Node-tillstånd

Antalet noder i noden lista räknar antalet beräknade noder efter följande tillstånd i varje pool. Separata mängd antal anges för dedikerade noder och noder med låg prioritet i varje pool.

- **Skapa** – en Azure-ALLOKERAD virtuell dator som ännu inte har börjat anslutas till en pool.
- **Inaktiv** – en tillgänglig Compute-nod som inte kör en aktivitet för tillfället.
- **LeavingPool** – en nod som lämnar poolen, antingen på grund av att användaren uttryckligen har tagit bort den eller eftersom poolen ändrar storlek eller skalar ned igen.
- **Offline** – en nod som batchen inte kan använda för att schemalägga nya aktiviteter.
- I **förväg –** en nod med låg prioritet som har tagits bort från poolen eftersom Azure frigjorde den virtuella datorn. En `preempted` nod kan återinitieras när den virtuella datorns kapacitet med låg prioritet är tillgänglig.
- Startar **om** en nod som startar om.
- **Reavbildning** – en nod där operativ systemet installeras om.
- **Kör** en nod som kör en eller flera aktiviteter (förutom start aktiviteten).
- **Starta** en nod där batch-tjänsten startas. 
- **StartTaskFailed** – en nod där [Start uppgiften](/rest/api/batchservice/pool/add#starttask) misslyckades och förbrukade alla återförsök och på vilken som `waitForSuccess` anges på Start aktiviteten. Noden kan inte användas för att köra uppgifter.
- **Okänd** -en nod som tappade kontakten med batch-tjänsten och vars tillstånd inte är känt.
- **Oanvändbar** -en nod som inte kan användas för uppgifts körning på grund av fel.
- **WaitingForStartTask** – en nod där start aktiviteten började köras, men `waitForSuccess` har angetts och start aktiviteten inte har slutförts.

Följande C#-kodfragment visar hur man listar antal noder för alla pooler i det aktuella kontot:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Följande C#-kodfragment visar hur man listar antal noder för en specifik pool i det aktuella kontot.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Du kan använda ett liknande mönster för REST och andra språk som stöds för att hämta antal noder för pooler.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om att använda filter på frågor som visar batch-resurser, se [skapa frågor för att skapa en lista med batch-resurser effektivt](batch-efficient-list-queries.md).
