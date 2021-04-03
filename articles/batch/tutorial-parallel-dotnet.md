---
title: 'Självstudie – köra en parallell arbets belastning med .NET-API: et'
description: Självstudie – Omkoda mediefiler parallellt med ffmpeg i Azure Batch med hjälp av klientbiblioteket Batch .NET
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/29/2020
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: a990a5480a8a6462bb6ef9f84070b78768628fd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106554"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Självstudie: Kör en parallell arbetsbelastning med Azure Batch med hjälp av .NET API

Använd Azure Batch till att effektivt köra storskaliga parallella program och HPC-program (databehandling med höga prestanda) i Azure. I den här självstudien går vi igenom ett C#-exempel på att köra en parallell arbetsbelastning med Batch. Du lär dig ett vanligt arbetsflöde för Batch-program och hur du interagerar programmatiskt med Batch- och Storage-resurser. Lär dig att:

> [!div class="checklist"]
> * lägga till ett programpaket i Batch-kontot
> * autentisera med Batch- och Storage-konton
> * ladda upp indatafiler till Storage
> * skapa en pool med beräkningsnoder för att köra ett program
> * skapa ett jobb och uppgifter som bearbetar indatafilerna
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

I den här självstudien konverterar du MP4-mediefiler parallellt till MP3-format med verktyget [ffmpeg](https://ffmpeg.org/) som har öppen källkod. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Visual Studio 2017 eller senare](https://www.visualstudio.com/vs), eller [.net Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1) för Linux, MacOS eller Windows.

* Ett Batch-konto och ett länkat Azure Storage-konto. Information om hur du skapar de här kontona finns Batch-snabbstarterna som du kommer åt via [Azure-portalen](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

* [Windows 64-bitars version av ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) (. zip). Ladda ned zip-filen till din lokala dator. I den här självstudien behöver du bara zip-filen. Du behöver inte packa upp filen eller installera den lokalt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="add-an-application-package"></a>Lägg till ett programpaket

Använd Azure-portalen och lägg till ffmpeg i Batch-kontot som ett [programpaket](batch-application-packages.md). Med programpaket kan du hantera uppgiftsprogram och deras distribution till beräkningsnoderna i din pool. 

1. Klicka på **fler tjänster**  >  **batch-konton** i Azure Portal och klicka på namnet på batch-kontot.
3. Klicka på **program**  >  **Lägg till**.
4. För **program-ID** anger du *ffmpeg* och en paket version av *4.3.1*. Välj zip-filen för ffmpeg som du laddade ned tidigare och klicka på **OK**. Programpaketet för ffmpeg läggs till i Batch-kontot.

![Lägg till programpaket](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Ladda ned och kör exemplet

### <a name="download-the-sample"></a>Ladda ned exemplet

[Ladda ned eller klona exempelappen](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) från GitHub. Om du vill klona lagringsplatsen för exempelappen med en Git-klient använder du följande kommando:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Gå till den katalog som innehåller filen med Visual Studio-lösningen `BatchDotNetFfmpegTutorial.sln`.

Öppna lösningsfilen i Visual Studio och uppdatera strängarna med autentiseringsuppgifterna i `Program.cs` med värdena för dina konton. Exempel:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Kontrollera också att referensen till programpaketet för ffmpeg i lösningen har samma id och version som ffmpeg-paketet du laddade upp till Batch-kontot.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "4.3.1";
```

### <a name="build-and-run-the-sample-project"></a>Skapa och kör exempelprojektet

Skapa och kör programmet i Visual Studio eller på kommandoraden med kommandona `dotnet build` och `dotnet run`. När du har kört appen ska du granska koden för att lära dig hur varje del av appen fungerar. Till exempel i Visual Studio:

* Högerklicka på lösningen i Solution Explorer och klicka på **Skapa lösning**. 

* Bekräfta återställningen av NuGet-paket om du uppmanas att göra det. Om du behöver ladda ned paket som saknas ska du se till att [NuGet Package Manager](https://docs.nuget.org/consume/installing-nuget) är installerat.

Kör det. När du kör exempelappen ser utdata i konsolen ut ungefär så här. Under körningen uppstår det en paus vid `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` medan poolens beräkningsnoder startas. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Gå till Batch-kontot i Azure-portalen för att övervaka poolen, beräkningsnoderna, jobbet och uppgifterna. Om du till exempel vill visa en värme karta över datornoderna i din pool klickar du på **pooler**  >  *WinFFmpegPool*.

När uppgifter körs ser den termiska kartan ut ungefär så här:

![Termisk karta för pool](./media/tutorial-parallel-dotnet/pool.png)

Körningen tar normalt runt **10 minuter** om du kör programmet med standardkonfigurationen. Att skapa poolen är det som tar mest tid.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Granska koden

I följande avsnitt bryter vi ned exempelprogrammet i de steg som utförs när en arbetsbelastning bearbetas i Batch-tjänsten. Läs filen `Program.cs` i lösningen medan du läser resten av den här artikeln eftersom inte alla kodrader i exemplet beskrivs.

### <a name="authenticate-blob-and-batch-clients"></a>Autentisera Blob- och Batch-klienter

I interaktionen med det länkade lagringskontot använder appen Azure Storage Client Library för .NET. En referens till kontot skapas med [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) och autentiseringen görs med hjälp av delad nyckel. Sedan skapas en [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Appen skapar ett [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)-objekt för att skapa och hantera pooler, jobb och uppgifter i Batch-tjänsten. Batch-klienten i exemplet använder autentisering med delad nyckel. Batch har även stöd för autentisering via [Azure Active Directory](batch-aad-auth.md) för att autentisera enskilda användare eller obevakade program.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Ladda upp indatafiler

Appen skickar `blobClient`-objektet till metoden `CreateContainerIfNotExistAsync` för att skapa en lagringscontainer för indatafilerna (MP4-format) och en container för uppgiftsutdata.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName);
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Sedan laddas filerna upp till containern för indata från den lokala mappen `InputFiles`. De lagrade filerna har definierats som Batch [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile)-objekt som Batch senare kan hämta till beräkningsnoder. 

Två metoder i `Program.cs` hanterar uppladdningen av filerna:

* `UploadFilesToContainerAsync`: returnerar en samling ResourceFile-objekt och anropar `UploadResourceFileToContainerAsync` internt för att ladda upp varje fil som skickas i parametern `inputFilePaths`.
* `UploadResourceFileToContainerAsync`: laddar upp varje fil som en blob till containern för indata. När filen har laddats upp hämtar den en signatur för delad åtkomst (SAS) för bloben och returnerar ett ResourceFile-objekt som representerar den.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Mer information om hur du laddar upp filer som blobar till ett lagringskonto med .NET finns i [Ladda upp, ladda ned och lista blobar med hjälp av .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Därefter skapar exempelkoden en pool med beräkningsnoder i Batch-kontot med ett anrop till `CreatePoolIfNotExistAsync`. I den här definierade metoden används metoden [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) till att ange antalet noder, VM-storlek och en poolkonfiguration. Här anger ett [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration)-objekt en [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) till en Windows Server-avbildning som publicerats på Azure Marketplace. Batch har stöd för ett stort antal Linux- och Windows Server-avbildningar på Azure Marketplace samt för anpassade VM-avbildningar.

Antalet noder och VM-storleken anges med definierade konstanter. Batch har stöd för dedikerade noder och [noder med låg prioritet](batch-low-pri-vms.md), och du kan använda antingen eller båda i dina pooler. Dedikerade noder är reserverade för din pool. Noder med låg prioritet erbjuds till ett reducerat pris från VM-överskottskapacitet i Azure. Noder med låg prioritet är inte tillgängliga om Azure inte har tillräckligt med kapacitet. Exemplet skapar som standard en pool som endast innehåller 5 noder med låg prioritet i storleken *Standard_A1_v2*.

>[!Note]
>Se till att du kontrollerar dina Node-kvoter. Se [batch-tjänstens kvoter och begränsningar](batch-quota-limit.md#increase-a-quota) för instruktioner om hur du skapar en kvot förfrågan. "

Programmet ffmpeg distribueras till beräkningsnoderna genom att en [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) läggs till i poolkonfigurationen.

Metoden [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) skickar poolen till Batch-tjänsten.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Skapa ett jobb

Ett Batch-jobb anger en pool för körning av uppgifter, samt valfria inställningar som en prioritet och ett schema för arbetet. I exemplet skapas ett jobb med ett anrop till `CreateJobAsync`. I den här definierade metoden används metoden [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) till att skapa ett jobb i din pool.

Metoden [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync) skickar jobbet till Batch-tjänsten. Från början har jobbet inga uppgifter.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Skapa uppgifter

I exemplet skapas uppgifterna i jobbet med ett anrop till metoden `AddTasksAsync`, som skapar en lista med [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)-objekt. Varje `CloudTask` kör ffmpeg för bearbetning av ett `ResourceFile`-indataobjekt med egenskapen [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). ffmpeg installerades tidigare på varje nod när poolen skapades. Här kör kommandoraden ffmpeg för att konvertera varje MP4-indatafil (video) till en MP3-fil (ljud).

I exemplet skapas ett [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile)-objekt för MP3-filen när du kör kommandoraden. Varje uppgifts utdatafiler (i det här fallet en) laddas upp till en container i länkade lagringskontot med uppgiftsegenskapen [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles). Tidigare i kodexemplet hämtades en signatur-URL för delad åtkomst (`outputContainerSasUrl`) för att ge skrivåtkomst till utdatacontainern. Observera de villkor som angetts på objektet `outputFile`. En utdatafil från en uppgift laddas upp till containern först när uppgiften har slutförts (`OutputFileUploadCondition.TaskSuccess`). Ytterligare information om implementering finns i det fullständiga [kodexemplet](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) på GitHub.

Sedan lägger exemplet till uppgifter i jobbet med metoden [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync), som placerar dem i kö för att köras på beräkningsnoderna.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-4.3.1-2020-09-21-full_build\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Övervaka aktiviteter

När Batch lägger till uppgifter i ett jobb placerar tjänsten dem automatiskt i kö och schemalägger dem för körning vid beräkningsnoder i den associerade poolen. Baserat på de inställningar du anger sköter Batch all köhantering, all schemaläggning, alla omförsök och all annan uppgiftsadministration åt dig.

Du kan övervaka aktivitetskörningen på många sätt. I det här exemplet definieras metoden `MonitorTasks` för att rapportera endast vid slutförande och vid uppgiftstillstånden för fel eller framgång. Koden i `MonitorTasks` anger en [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) så att endast minimalt med information om uppgifterna väljs. Sedan skapas en [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) som tillhandahåller hjälpkomponenter för övervakning av uppgiftstillstånd. I `MonitorTasks` väntar exemplet på att alla uppgifter ska nå `TaskState.Completed` inom en tidsgräns. Sedan avslutas jobbet och alla uppgifter som slutfördes men där det kan ha uppstått ett fel rapporteras, till exempel vid slutkoder skilda från noll.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Rensa resurser

När uppgifterna har körts tar appen automatiskt bort den lagringscontainer som skapades och du får möjlighet att ta bort Batch-poolen och jobbet. Klasserna [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) och [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) i BatchClient har båda motsvarande borttagningsmetoder som anropas om du bekräftar borttagningen. Även om du inte debiteras för själva jobben och uppgifterna så debiteras du för beräkningsnoder. Vi rekommenderar därför att du endast allokerar pooler efter behov. När du tar bort poolen raderas alla aktivitetsutdata på noderna. Utdatafilerna ligger däremot kvar i lagringskontot.

När de inte längre behövs tar du bort resursgruppen, Batch-kontot och lagringskontot. Om du vill göra det i Azure-portalen väljer du resursgruppen för Batch-kontot och klickar på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * lägga till ett programpaket i Batch-kontot
> * autentisera med Batch- och Storage-konton
> * ladda upp indatafiler till Storage
> * skapa en pool med beräkningsnoder för att köra ett program
> * skapa ett jobb och uppgifter som bearbetar indatafilerna
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

Fler exempel på hur du använder .NET API till att schemalägga och bearbeta Batch-arbetsbelastningar finns i exemplen på GitHub.

> [!div class="nextstepaction"]
> [Batch C#-exempel](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
