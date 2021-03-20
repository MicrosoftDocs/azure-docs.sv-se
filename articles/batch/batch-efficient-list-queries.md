---
title: Utforma effektiva List frågor
description: Öka prestanda genom att filtrera dina frågor när du begär information om batch-resurser som pooler, jobb, uppgifter och Compute-noder.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88933522"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Skapa frågor för att lista batch-resurser effektivt

Nästan alla batch-program behöver utföra någon typ av övervakning eller annan åtgärd som skickar frågor till batch-tjänsten, ofta med jämna mellanrum. Om du till exempel vill fastställa om det finns några köade uppgifter kvar i ett jobb måste du hämta data för alla aktiviteter i jobbet. Om du vill fastställa status för noder i poolen måste du hämta data på varje nod i poolen. Den här artikeln förklarar hur du kör sådana frågor på det mest effektiva sättet.

Du kan öka Azure Batch programmets prestanda genom att minska mängden data som returneras av tjänsten när du frågar jobb, aktiviteter, Compute-noder och andra resurser med [batch .net](/dotnet/api/microsoft.azure.batch) -biblioteket.

> [!NOTE]
> Batch-tjänsten tillhandahåller API-stöd för vanliga scenarier för att räkna aktiviteter i ett jobb och inventera datornoder i batch-poolen. I stället för att använda en lista fråga för dessa kan du anropa åtgärderna [Hämta antal aktiviteter](/rest/api/batchservice/job/gettaskcounts) och [lista antal noder i noden](/rest/api/batchservice/account/listpoolnodecounts) . Dessa åtgärder är mer effektiva än en List fråga, men returnerar mer begränsad information som kanske inte alltid är aktuell. Mer information finns i [räkna aktiviteter och Compute-noder efter tillstånd](batch-get-resource-counts.md).

## <a name="specify-a-detail-level"></a>Ange en detalj nivå

I ett batch-program för produktion kan entiteter som jobb, aktiviteter och Compute-noder numreras i tusental. När du begär information om de här resurserna måste en potentiellt stor mängd data "korsa kabeln" från batch-tjänsten till ditt program på varje fråga. Genom att begränsa antalet objekt och vilken typ av information som returneras av en fråga kan du öka hastigheten på dina frågor och därmed programmets prestanda.

Det här [batch .net](/dotnet/api/microsoft.azure.batch) API-kodfragmentet listar *varje* aktivitet som är associerad med ett jobb, tillsammans med *alla* egenskaper för varje aktivitet:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan använda en mycket mer effektiv List fråga, men genom att använda en "detalj nivå" i din fråga. Du gör detta genom att tillhandahålla ett [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) -objekt till [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) -metoden. Det här kodfragmentet returnerar bara egenskaperna ID, kommando rad och Compute Node (information) för slutförda aktiviteter:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

I det här exempel scenariot, om det finns tusentals uppgifter i jobbet, kommer resultatet från den andra frågan vanligt vis att returnera mycket snabbare än den första. Mer information om hur du använder ODATADetailLevel när du listerar objekt med batch .NET-API: et finns [nedan](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Vi rekommenderar starkt att du alltid anger ett ODATADetailLevel-objekt till dina .NET API List-anrop för att säkerställa högsta effektivitet och prestanda för ditt program. Genom att ange en detalj nivå kan du hjälpa till att minska svars tiderna för batch-tjänsten, förbättra nätverks användningen och minimera minnes användningen av klient program.

## <a name="filter-select-and-expand"></a>Filtrera, välja och expandera

API: erna för [batch .net](/dotnet/api/microsoft.azure.batch) och [batch rest](/rest/api/batchservice/) ger möjlighet att minska antalet objekt som returneras i en lista, samt hur mycket information som returneras för varje. Du gör detta genom att ange **filter**, **välja** och **expandera strängar** när du utför List frågor.

### <a name="filter"></a>Filtrera

Filter strängen är ett uttryck som minskar antalet objekt som returneras. Du kan till exempel endast lista de aktiviteter som körs för ett jobb, eller en lista med enbart datornoder som är redo att köra uppgifter.

Filter strängen består av ett eller flera uttryck med ett uttryck som består av ett egenskaps namn, en operator och ett värde. De egenskaper som kan anges är specifika för varje entitetstyp som du frågar efter, som är de operatorer som stöds för varje egenskap.  Flera uttryck kan kombineras med hjälp av logiska operatorer `and` och `or` .

I den här exempel filter strängen visas bara de kör åter givnings aktiviteter som körs: `(state eq 'running') and startswith(id, 'renderTask')` .

### <a name="select"></a>Välj

Den valda strängen begränsar de egenskaps värden som returneras för varje objekt. Du kan ange en lista över kommaavgränsade egenskaps namn och bara de egenskaps värden som returneras för objekten i frågeresultatet. Du kan ange någon av egenskaperna för entitetstypen som du frågar.

I det här exemplet väljer du sträng anger att endast tre egenskaps värden ska returneras för varje aktivitet: `id, state, stateTransitionTime` .

### <a name="expand"></a>Visa

Expand-strängen minskar antalet API-anrop som krävs för att hämta viss information. När du använder en Expand-sträng kan mer information om varje objekt hämtas med ett enda API-anrop. I stället för att först hämta listan över entiteter och sedan begära information för varje objekt i listan, använder du en expanderande sträng för att hämta samma information i ett enda API-anrop, vilket bidrar till att förbättra prestanda genom att minska API-anropen.

På samma sätt som med Select-strängen, kontrollerar Expand-strängen om vissa data ingår i lista frågeresultat. När alla egenskaper krävs och ingen Select-sträng anges, *måste* expansions strängen användas för att hämta statistik information. Om en SELECT-sträng används för att hämta en delmängd av egenskaperna, `stats` kan anges i SELECT-strängen och den expanderade strängen behöver inte anges.

Expand strängen stöds bara när den används i ett List jobb, jobb scheman, uppgifter och pooler. För närvarande stöder den bara statistik information.

I det här exemplet expanderas sträng anger du att statistik information ska returneras för varje objekt i listan: `stats` .

> [!NOTE]
> När du skapar någon av de tre typerna av frågesträngar (filter, Välj och expandera) måste du se till att egenskaps namnen och skiftet stämmer överens med de REST API elementens motsvarigheter. När du arbetar med .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) -klassen måste du till exempel ange **State** i stället för **State**, även om .net-egenskapen är [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Se tabellerna nedan för egenskaps mappningar mellan .NET-och REST-API: er.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler för filter, Välj och expandera strängar

- Egenskaps namn i filter, Välj och expandera strängar bör visas som de gör i [batch rest](/rest/api/batchservice/) -API: et, även om du använder [batch .net](/dotnet/api/microsoft.azure.batch) eller någon av de andra batch SDK: erna.
- Alla egenskaps namn är Skift läges känsliga, men egenskaps värden är Skift läges känsliga.
- Datum-och tids strängar kan vara ett av två format och måste föregås av `DateTime` .
  
  - Exempel på W3C-DTF format: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exempel på RFC 1123-format: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Booleska strängar är antingen `true` eller `false` .
- Om en ogiltig egenskap eller operator anges, uppstår ett `400 (Bad Request)` fel.

## <a name="efficient-querying-in-batch-net"></a>Effektiv fråga i batch .NET

I [batch .net](/dotnet/api/microsoft.azure.batch) -API: et används klassen [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) för att ange filter, välja och expandera strängar för att lista åtgärder. ODataDetailLevel-klassen har tre offentliga sträng egenskaper som kan anges i konstruktorn eller anges direkt för objektet. Sedan skickar du ODataDetailLevel-objektet som en parameter till de olika List åtgärderna, till exempel [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations), [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations)och [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations).

- [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): begränsa antalet objekt som returneras.
- [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): ange vilka egenskaps värden som returneras för varje objekt.
- [ODATADetailLevel. ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): Hämta data för alla objekt i ett enda API-anrop i stället för separata anrop för varje objekt.

Följande kodfragment använder batch .NET API för att effektivt fråga batch-tjänsten om statistik för en angiven uppsättning pooler. I det här scenariot har batch-användaren både test-och produktionspooler. TestPool-ID: n föregås av "test" och ID: n för produktionspooler har prefixet "Prod". I kodfragmentet är *myBatchClient* en korrekt initierad instans av [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient) -klassen.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> En instans av [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) som kon figurer ATS med Select and Expand-satser kan också skickas till lämpliga get-metoder, till exempel [PoolOperations. GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), för att begränsa mängden data som returneras.

## <a name="batch-rest-to-net-api-mappings"></a>Batch-REST till .NET API-mappningar

Egenskaps namn i filter-, Select-och Expand-strängar måste reflektera sina REST API-motsvarigheter, både i namn och Skift läge. Tabellerna nedan innehåller mappningar mellan .NET och REST API motsvarigheter.

### <a name="mappings-for-filter-strings"></a>Mappningar för filter strängar

- **.Net-List metoder**: var och en av .NET API-metoderna i den här kolumnen accepterar ett [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) -objekt som en parameter.
- **Rest List-begäranden**: varje REST API sida som är länkad till i den här kolumnen innehåller en tabell som anger de egenskaper och åtgärder som tillåts i *filter* strängar. Du använder dessa egenskaps namn och-åtgärder när du skapar en [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) -sträng.

| .NET-List metoder | REST List-begäranden |
| --- | --- |
| [Metoden certificateoperations. ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[Lista certifikaten i ett konto](/rest/api/batchservice/certificate/list) |
| [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[Lista de filer som associeras med en aktivitet](/rest/api/batchservice/file/listfromtask) |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[Lista status för jobb förberedelse-och jobb publicerings aktiviteter för ett jobb](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations. ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[Visa en lista över jobben i ett konto](/rest/api/batchservice/job/list) |
| [JobOperations. ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[Lista filerna på en nod](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[Lista de uppgifter som är associerade med ett jobb](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Visa en lista med jobb scheman i ett konto](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Lista de jobb som är associerade med ett jobb schema](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations. ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[Visa en lista med datornoderna i en pool](/rest/api/batchservice/computenode/list) |
| [PoolOperations. ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[Lista pooler i ett konto](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>Mappningar för Select-strängar

- **Batch .net-typer**: batch .NET API-typer.
- **REST API entiteter**: varje sida i den här kolumnen innehåller en eller flera tabeller som visar REST API egenskaps namnen för typen. Dessa egenskaps namn används när du skapar *Select* -strängar. Du använder samma egenskaps namn när du skapar en [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) -sträng.

| Batch .NET-typer | REST API entiteter |
| --- | --- |
| [Certifikat](/dotnet/api/microsoft.azure.batch.certificate) |[Hämta information om ett certifikat](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[Hämta information om ett jobb](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[Hämta information om ett jobb schema](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[Hämta information om en nod](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[Hämta information om en pool](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[Hämta information om en uppgift](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>Exempel: skapa en filter sträng

När du skapar en filter sträng för [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause), se tabellen ovan under "mappningar för filter strängar" för att hitta REST API dokumentations sidan som motsvarar den List åtgärd som du vill utföra. Du hittar de filter bara egenskaperna och de operatörer som stöds i den första multirow-tabellen på den sidan. Om du vill hämta alla aktiviteter vars slutkod inte var noll, till exempel den här raden i [lista de uppgifter som är associerade med ett jobb](/rest/api/batchservice/task/list) , anger den tillämpliga egenskaps strängen och tillåtna operatorer:

| Egenskap | Tillåtna åtgärder | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Därför blir filter strängen för att lista alla aktiviteter med en slutkod som inte är noll:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exempel: skapa en SELECT-sträng

Om du vill skapa [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)läser du tabellen ovan under "mappningar för Select Strings" och navigerar till REST API sidan som motsvarar den typ av enhet som du visar. Du hittar de valbara egenskaperna och de operatörer som stöds i den första multirow-tabellen på den sidan. Om du till exempel bara vill hämta ID och kommando rad för varje aktivitet i en lista, så hittar du till exempel raderna i den tillämpliga tabellen på [Hämta information om en aktivitet](/rest/api/batchservice/task/get):

| Egenskap | Typ | Kommentarer |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Den valda strängen för att inkludera endast ID och kommando rad med varje listad aktivitet skulle då vara:

`id, commandLine`

## <a name="code-samples"></a>Kodexempel

### <a name="efficient-list-queries-code-sample"></a>Effektiv lista frågor kod exempel

[EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) -exempelprojektet på GitHub visar hur effektiva List frågor kan påverka prestanda i ett program. Det här C#-konsol programmet skapar och lägger till ett stort antal aktiviteter i ett jobb. Sedan gör det flera anrop till metoden [JobOperations. ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) och överför [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) -objekt som har kon figurer ATS med olika egenskaps värden för att variera mängden data som ska returneras. Den ger utdata som liknar följande:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Som du ser i de förflutna tiderna kan du minska svars tiderna i frågan genom att begränsa egenskaperna och antalet objekt som returneras. Du kan hitta det här och andra exempel projekt i databasen [Azure-Batch-samples](https://github.com/Azure-Samples/azure-batch-samples) på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-bibliotek och kod exempel

Förutom EfficientListQueries-kod exemplet ovan visar [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) -exempelprojektet hur du effektivt övervakar Azure batch jobb förlopp med batch-API: et.

[BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) -exemplet innehåller ett .NET-klass biblioteks projekt som du kan inkludera i dina egna projekt, och ett enkelt kommando rads program för att använda och demonstrera användningen av biblioteket.

Exempel programmet i projektet demonstrerar följande åtgärder:

1. Välja speciella attribut för att bara hämta de egenskaper du behöver
2. Filtrering av tillstånds över gångs tider för att bara hämta ändringar sedan den senaste frågan

Till exempel visas följande metod i BatchMetrics-biblioteket. Den returnerar en ODATADetailLevel som anger att endast `id` egenskaperna och `state` ska hämtas för de entiteter som efter frågas. Det anger också att endast entiteter vars tillstånd har ändrats sedan den angivna `DateTime` parametern ska returneras.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [maximerar Azure Batch beräknings resursanvändning med aktiviteter med samtidiga noder](batch-parallel-node-tasks.md). Vissa typer av arbets belastningar kan dra nytta av att köra parallella uppgifter på större (men färre) datornoder. Se [exempel scenariot](batch-parallel-node-tasks.md#example-scenario) i artikeln för information om ett sådant scenario.
- Lär dig hur du [övervakar batch-lösningar genom att räkna aktiviteter och noder efter tillstånd](batch-get-resource-counts.md)


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
