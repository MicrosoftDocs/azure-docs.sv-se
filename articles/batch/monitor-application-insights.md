---
title: Övervaka Batch med Azure Application Insights
description: Lär dig hur du instrumenterar Azure Batch .NET-program med hjälp Azure Application Insights-biblioteket.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/13/2021
ms.openlocfilehash: 8bc8ff0a04996d988a642062f118e9e6792abbf0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389357"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Övervaka och felsöka ett Azure Batch .NET-program med Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) ett elegant och kraftfullt sätt för utvecklare att övervaka och felsöka program som distribueras till Azure-tjänster. Använd Application Insights för att övervaka prestandaräknare och undantag samt instrumentera din kod med anpassade mått och spårning. Genom Application Insights integrering med Azure Batch-programmet kan du få djupgående insikter om beteenden och undersöka problem i nära realtid.

Den här artikeln visar hur du lägger till och konfigurerar Application Insights-biblioteket i din Azure Batch .NET-lösning och instrumenterar programkoden. Den visar också olika sätt att övervaka ditt program via Azure Portal och skapa anpassade instrumentpaneler. Mer Application Insights på andra språk finns i dokumentationen [om språk, plattformar och integreringar.](../azure-monitor/app/platforms.md)

En C#-exempellösning med kod som medföljer den här artikeln finns på [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Det här exemplet Application Insights till instrumentationskoden i [TopNWords-exemplet.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Om du inte är bekant med det exemplet kan du prova att skapa och köra TopNWords först. Detta hjälper dig att förstå ett grundläggande Batch-arbetsflöde för bearbetning av en uppsättning indatablobar parallellt på flera beräkningsnoder.

> [!TIP]
> Du kan också konfigurera Batch-lösningen så att den Application Insights data, till exempel prestandaräknare för virtuella datorer, Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnadsfritt, fristående klientverktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. I [lagringsplatsen batch-insights](https://github.com/Azure/batch-insights) finns snabba steg för att Application Insights data i Batch Explorer.

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2017 eller senare](https://www.visualstudio.com/vs)
- [Batch-konto och länkat lagringskonto](batch-account-create-portal.md)
- [Application Insights resurs](../azure-monitor/app/create-new-resource.md). Använd Azure Portal för att skapa Application Insights *resurs*. Välj *programtypen* **Allmänt.**
- Kopiera [instrumenteringsnyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) från Azure Portal. Du behöver det här värdet senare.
  
  > [!NOTE]
  > Du kan [debiteras för](https://azure.microsoft.com/pricing/details/application-insights/) data som lagras i Application Insights. Detta inkluderar diagnostik- och övervakningsdata som beskrivs i den här artikeln.

## <a name="add-application-insights-to-your-project"></a>Lägga Application Insights i projektet

**NuGet-paketet Microsoft.ApplicationInsights.WindowsServer** och dess beroenden krävs för projektet. Lägg till eller återställ dem i programmets projekt. Installera paketet med kommandot eller `Install-Package` NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```

Referera Application Insights .NET-programmet med hjälp av **namnområdet Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrumentera din kod

För att instrumentera koden måste din lösning skapa en Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). I det här exemplet läser TelemetryClient in konfigurationen från [ denApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) filen. Se till att ApplicationInsights.config i följande projekt med din Application Insights instrumentationsnyckel: Microsoft.Azure.Batch. Samples.TelemetryStartTask och TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```

Lägg även till instrumenteringsnyckeln i filen TopNWords.cs.

I exemplet i TopNWords.cs används följande [instrumentationsanrop från](../azure-monitor/app/api-custom-events-metrics.md) api:et Application Insights:

- `TrackMetric()` – Spårar hur lång tid i genomsnitt det tar för en beräkningsnod att ladda ned den textfil som krävs.
- `TrackTrace()` – Lägger till felsökningssamtal i koden.
- `TrackEvent()` – Spårar intressanta händelser att samla in.

Det här exemplet lämnar undantagshanteringen bort. I stället Application Insights rapporterar ohanterade undantag, vilket avsevärt förbättrar felsökningen.

Följande kodfragment illustrerar hur du använder dessa metoder.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch för telemetriinitiering

När du rapporterar telemetri för en viss server och instans använder Application Insights Azure VM-rollen och VM-namnet för standardvärdena. I kontexten för Azure Batch visar exemplet hur du använder poolnamnet och beräkningsnodnamnet i stället. Använd en [telemetriinitiering för](../azure-monitor/app/api-filtering-sampling.md#add-properties) att åsidosätta standardvärdena.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

För att aktivera telemetriinitieraren innehåller ApplicationInsights.config i TopNWordsSample-projektet följande:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
```

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Uppdatera jobbet och aktiviteterna så att de inkluderar Application Insights binärfiler

För att Application Insights ska köras korrekt på dina beräkningsnoder kontrollerar du att binärfilerna är korrekt placerade. Lägg till de binärfiler som krävs i aktivitetens resursfilsamling så att de laddas ned när aktiviteten körs. Följande kodfragment liknar kod i Job.cs.

Skapa först en statisk lista över de Application Insights som ska laddas upp.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Skapa sedan mellanlagringsfilerna som används av aktiviteten.

```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

Metoden är en hjälpfunktion i kodexe exemplet som gör att du enkelt kan ladda upp en fil från en lokal disk till `FileToStage` en Azure Storage blob. Varje fil laddas senare ned till en beräkningsnod och refereras till av en aktivitet.

Slutligen lägger du till aktiviteterna i jobbet och inkluderar de nödvändiga Application Insights binärfilerna.

```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Visa data i Azure Portal

Nu när du har konfigurerat jobbet och aktiviteterna att använda Application Insights kör du exempeljobbet i poolen. Gå till Azure Portal och öppna den Application Insights som du etablerade. När poolen har etablerats bör du börja se data flöda och loggas. Resten av den här artikeln berör bara några få Application Insights funktioner, men du kan utforska den fullständiga funktionsuppsättningen.

### <a name="view-live-stream-data"></a>Visa liveuppspelningsdata

Om du vill visa spårningsloggar i din Applications Insights-resurs klickar **du på Live Stream**. Följande skärmbild visar hur du visar livedata som kommer från beräkningsnoderna i poolen, till exempel processoranvändningen per beräkningsnod.

![Skärmbild av data från beräkningsnoder i direktsänd dataström.](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visa spårningsloggar

Om du vill visa spårningsloggar i din Applications Insights-resurs klickar du på **Sök**. Den här vyn visar en lista över diagnostikdata som samlas in Application Insights inklusive spårningar, händelser och undantag. 

Följande skärmbild visar hur en enda spårning för en uppgift loggas och senare efterfrågas i felsökningssyfte.

![Skärmbild som visar loggar för en enda spårning.](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visa ohanterade undantag

Application Insights loggar undantag som utslängdes från ditt program. I det här fallet kan du gå in på detaljgranska ett specifikt undantag och diagnostisera problemet inom några sekunder efter att programmet utlöste undantaget.

![Skärmbild som visar ohanterade undantag.](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mäta hämtningstid för blob

Anpassade mått är också ett värdefullt verktyg i portalen. Du kan till exempel visa den genomsnittliga tid det tog varje beräkningsnod att ladda ned den nödvändiga textfilen som bearbetades.

Så här skapar du ett exempeldiagram:

1. I din Application Insights klickar du på lägg **Metrics Explorer**  >  **Lägg till diagram.**
1. Klicka **på** Redigera i diagrammet som lades till.
1. Uppdatera diagraminformationen på följande sätt:
   - Ange **Diagramtyp** till **Rutnät.**
   - Ange **Sammansättning** till **Genomsnitt.**
   - Ange **Gruppera efter** till **NodeId**.
   - I **Mått väljer** du Ladda ned **anpassad** blob  >  **i sekunder.**
   - Justera **färgpaletten** efter ditt val.

![Skärmbild av ett diagram som visar hämtningstiden för blobar per nod.](./media/monitor-application-insights/blobdownloadtime.png)

## <a name="monitor-compute-nodes-continuously&quot;></a>Övervaka beräkningsnoder kontinuerligt

Du kanske har märkt att alla mått, inklusive prestandaräknare, endast loggas när aktiviteterna körs. Det här beteendet är användbart eftersom det begränsar mängden data som Application Insights loggar. Det finns dock fall då du alltid vill övervaka beräkningsnoderna. De kan till exempel köra bakgrundsarbete som inte schemaläggs via Batch-tjänsten. I det här fallet ställer du in en övervakningsprocess som körs under beräkningsnodens livslängd. 

Ett sätt att uppnå det här beteendet är att skapa en process som läser Application Insights biblioteket och körs i bakgrunden. I det här exemplet läser startaktiviteten in binärfilerna på datorn och håller en process som körs på obestämd tid. Konfigurera konfigurationsfilen Application Insights den här processen för att generera ytterligare data som du är intresserad av, till exempel prestandaräknare.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = &quot;StartTask&quot;;
private const string BatchStartTaskTelemetryRunnerName = &quot;Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe&quot;;
private const string BatchStartTaskTelemetryRunnerAIConfig = &quot;ApplicationInsights.config&quot;;
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: &quot;standard_d1_v2&quot;,
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: &quot;MicrosoftWindowsServer&quot;,
                        offer: &quot;WindowsServer&quot;,
                        sku: &quot;2019-datacenter-core&quot;,
                        version: &quot;latest"),
    nodeAgentSkuId: "batch.node.windows amd64");
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Om du vill öka hanterbarheten för din lösning kan du paketera sammansättningen i ett [programpaket](./batch-application-packages.md). Om du sedan vill distribuera programpaketet automatiskt till dina pooler lägger du till en programpaketreferens till poolkonfigurationen.

## <a name="throttle-and-sample-data"></a>Begränsning och exempeldata

På grund av den storskaliga typen Azure Batch program som körs i produktion kanske du vill begränsa mängden data som samlas in av Application Insights för att hantera kostnader. Se [Sampling i Application Insights](../azure-monitor/app/sampling.md) några mekanismer för att åstadkomma detta.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [att Application Insights](../azure-monitor/app/app-insights-overview.md).
- Mer Application Insights på andra språk finns i dokumentationen [om språk, plattformar och integreringar.](../azure-monitor/app/platforms.md)
