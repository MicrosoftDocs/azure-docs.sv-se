---
title: Övervaka batch med Azure Application insikter
description: Lär dig att instrumentera ett Azure Batch .NET-program med hjälp av Azure Application Insights-biblioteket.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 04/05/2018
ms.openlocfilehash: 9decb99c3de798df43dedc2441208066d18e3a13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104605791"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Övervaka och felsöka ett Azure Batch .NET-program med Application Insights

[Application Insights](../azure-monitor/app/app-insights-overview.md) är ett elegant och kraftfullt sätt för utvecklare att övervaka och felsöka program som distribueras till Azure-tjänster. Använd Application Insights för att övervaka prestanda räknare och undantag samt utforma koden med anpassade mått och spårning. Genom att integrera Application Insights med ditt Azure Batchs program kan du få djupgående insikter om beteenden och undersöka problem i nära real tid.

Den här artikeln visar hur du lägger till och konfigurerar Application Insights-biblioteket i din Azure Batch .NET-lösning och instrumenterar program koden. Det visar också hur du övervakar ditt program via Azure Portal och skapar anpassade instrument paneler. För Application Insights support på andra språk tittar du på [språk, plattformar och integrerings dokumentation](../azure-monitor/app/platforms.md).

Ett exempel på en C#-lösning med kod som medföljer den här artikeln finns på [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). I det här exemplet läggs Application Insights Instrumentation-kod till i [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) -exemplet. Om du inte är bekant med det exemplet kan du försöka skapa och köra TopNWords först. Detta hjälper dig att förstå ett grundläggande batch-arbetsflöde för bearbetning av en uppsättning BLOB-instanser parallellt på flera Compute-noder. 

> [!TIP]
> Alternativt kan du konfigurera batch-lösningen så att den visar Application Insights data, till exempel prestanda räknare för virtuella datorer i Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) är ett kostnads fritt, fristående klient verktyg med omfattande funktioner som hjälper dig att skapa, felsöka och övervaka Azure Batch program. Hämta ett [installationspaketet](https://azure.github.io/BatchExplorer/) för Mac, Linux eller Windows. Se [batch-Insights-lagrings platsen](https://github.com/Azure/batch-insights) för snabb steg för att aktivera Application Insights data i batch Explorer. 
>

## <a name="prerequisites"></a>Förutsättningar
* [Visual Studio 2017 eller senare](https://www.visualstudio.com/vs)

* [Batch-konto och länkat lagrings konto](batch-account-create-portal.md)

* [Application Insights-resurs](../azure-monitor/app/create-new-resource.md )
  
   * Använd Azure Portal för att skapa en Application Insights *resurs*. Välj *allmän* **program typ**.

   * Kopiera [Instrumentation-nyckeln](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) från portalen. Det krävs senare i den här artikeln.
  
  > [!NOTE]
  > Du kanske [debiteras](https://azure.microsoft.com/pricing/details/application-insights/) för data som lagras i Application Insights. Detta omfattar diagnostik-och övervaknings data som diskuteras i den här artikeln.
  > 

## <a name="add-application-insights-to-your-project"></a>Lägg till Application Insights i projektet

NuGet-paketet **Microsoft. ApplicationInsights. Windows Server** och dess beroenden krävs för ditt projekt. Lägg till eller Återställ dem till programmets projekt. Om du vill installera paketet använder du `Install-Package` kommandot eller NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referens Application Insights från ditt .NET-program med hjälp av namn området **Microsoft. ApplicationInsights** .

## <a name="instrument-your-code"></a>Instrumentera din kod

För att kunna instrumentera din kod måste din lösning skapa en Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). I exemplet laddar TelemetryClient konfigurationen från [ApplicationInsights.configs ](../azure-monitor/app/configuration-with-applicationinsights-config.md) filen. Se till att uppdatera ApplicationInsights.config i följande projekt med din Application Insights Instrumentation-nyckel: Microsoft.Azure.BatCH. Samples. TelemetryStartTask och TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Lägg också till Instrumentation-nyckeln i filen TopNWords. cs.

Exemplet i TopNWords. cs använder följande [Instrumentation-anrop](../azure-monitor/app/api-custom-events-metrics.md) från Application Insights API:
* `TrackMetric()` – Spårar hur lång tid det tar för en beräknings nod att ladda ned den nödvändiga text filen.
* `TrackTrace()` – Lägger till fel söknings anrop till din kod.
* `TrackEvent()` – Spårar intressanta händelser som ska fångas.

I det här exemplet används inte undantags hanteringen. I stället rapporterar Application Insights automatiskt ohanterade undantag, vilket avsevärt förbättrar fel söknings upplevelsen. 

Följande fragment illustrerar hur du använder dessa metoder.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Hjälp program för telemetri till Azure Batch telemetri
Vid rapportering av telemetri för en specifik server och instans använder Application Insights den virtuella Azure-rollen och VM-namnet för standardvärdena. I samband med Azure Batch visar exemplet hur du använder poolnamn och Compute Node-namnet i stället. Använd en [telemetri-initierare](../azure-monitor/app/api-filtering-sampling.md#add-properties) för att åsidosätta standardvärdena. 

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

För att aktivera telemetri-initieraren innehåller ApplicationInsights.config filen i TopNWordsSample-projektet följande:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Uppdatera jobbet och aktiviteterna så att de inkluderar Application Insights-binärfiler

För att Application Insights ska kunna köras korrekt på dina datornoder kontrollerar du att binärfilerna är rätt placerade. Lägg till de binärfiler som krävs i aktivitetens resurs fil samling så att de hämtas när uppgiften körs. Följande kodfragment liknar kod i Job. cs.

Skapa först en statisk lista med Application Insights filer som ska överföras.

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

Skapa sedan de uppsamlingsfiler som används av uppgiften.
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

`FileToStage`Metoden är en hjälp funktion i kod exemplet som gör att du enkelt kan ladda upp en fil från en lokal disk till en Azure Storage-blob. Varje fil laddas ned senare till en Compute-nod och refereras till av en aktivitet.

Slutligen lägger du till aktiviteterna i jobbet och inkluderar nödvändiga Application Insights binärfiler.
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

Nu när du har konfigurerat jobbet och aktiviteterna för att använda Application Insights kör du exempel jobbet i poolen. Navigera till Azure Portal och öppna den Application Insights resurs som du har etablerad. När poolen är etablerad bör du starta för att se data som flödar och loggar in. Resten av den här artikeln berör bara några få Application Insights funktioner, men du kan inte utforska den fullständiga funktions uppsättningen.

### <a name="view-live-stream-data"></a>Visa real tids data ström

Om du vill visa spårnings loggar i din program insikts resurs klickar du på **Live Stream**. Följande skärm bild visar hur du visar live-data som kommer från datornoderna i poolen, till exempel processor användning per Compute-nod.

![Data för data bearbetning av real tids data behandling](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Visa spårnings loggar

Om du vill visa spårnings loggar i din program insikts resurs klickar du på **Sök**. I den här vyn visas en lista med diagnostikdata som har registrerats med Application Insights inklusive spår, händelser och undantag. 

Följande skärm bild visar hur en enskild spårning för en aktivitet loggas och senare efter frågas i fel söknings syfte.

![Bild av spårnings loggar](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Visa ohanterade undantag

Följande skärm bilder visar hur Application Insights loggar undantag som har utlösts av ditt program. I det här fallet kan du, inom några sekunder av programmet, identifiera undantaget, öka detalj nivån i ett särskilt undantag och diagnostisera problemet.

![Ohanterade undantag](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mäta BLOB hämtnings tid

Anpassade mått är också ett värdefullt verktyg i portalen. Du kan till exempel Visa den genomsnittliga tid det tog för varje Compute-nod att ladda ned den nödvändiga text filen som den bearbetade.

Så här skapar du ett exempel diagram:
1. I Application Insights resurs klickar du på **Metrics Explorer**  >  **Lägg till diagram**.
2. Klicka på **Redigera** i diagrammet som har lagts till.
2. Uppdatera diagram informationen på följande sätt:
   * Ange **diagram typ** till **rutnät**.
   * Ange **agg regering** till **genomsnitt**.
   * Ange **Gruppera efter** till **nodeId**.
   * I **mått** väljer du **anpassad**  >  **BLOB-hämtning på några sekunder**.
   * Justera **paletten** för bildskärms färg efter eget val. 

![Hämtnings tid för BLOB per nod](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Övervaka Compute-noder kontinuerligt

Du kanske har märkt att alla mått, inklusive prestanda räknare, bara loggas när aktiviteterna körs. Det här beteendet är användbart eftersom det begränsar mängden data som Application Insights loggar. Det finns dock fall då du alltid vill övervaka datornoderna. De kan till exempel köra bakgrunds arbete som inte är schemalagt via batch-tjänsten. I det här fallet konfigurerar du en övervaknings process så att den körs under Compute-nodens livs längd. 

Ett sätt att åstadkomma detta är att skapa en process som läser in Application Insightss biblioteket och körs i bakgrunden. I exemplet laddar start aktiviteten binärfilerna på datorn och bevarar en process som körs på obestämd tid. Konfigurera Application Insights konfigurations filen för den här processen för att generera ytterligare data som du är intresse rad av, till exempel prestanda räknare.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
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
> Om du vill öka hanteringen av din lösning kan du paketera sammansättningen i ett [programpaket](./batch-application-packages.md). Om du vill distribuera programpaketet automatiskt till dina pooler lägger du till en programpaket-referens i konfigurationen för poolen.
>

## <a name="throttle-and-sample-data"></a>Begränsa och sampla data 

På grund av storskalig natur för Azure Batch program som körs i produktion, kanske du vill begränsa mängden data som samlas in av Application Insights för att hantera kostnader. Se [sampling i Application Insights](../azure-monitor/app/sampling.md) för vissa mekanismer för att åstadkomma detta.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [Application Insights](../azure-monitor/app/app-insights-overview.md).

* För Application Insights support på andra språk tittar du på [språk, plattformar och integrerings dokumentation](../azure-monitor/app/platforms.md).


