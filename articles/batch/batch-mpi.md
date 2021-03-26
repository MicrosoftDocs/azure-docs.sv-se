---
title: Använda aktiviteter med flera instanser för att köra MPI-program
description: Lär dig hur du kör MPI-program (Message Passing Interface) med hjälp av uppgifts typen multiinstance i Azure Batch.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 51fc580e0bb31e0e975c53b44887a5889a784eea
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605679"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Använda aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i batch

Med aktiviteter med flera instanser kan du köra en Azure Batch aktivitet på flera Compute-noder samtidigt. De här uppgifterna möjliggör data bearbetnings scenarier med höga prestanda som MPI-program (Message Passing Interface) i batch. I den här artikeln får du lära dig hur du kör aktiviteter med flera instanser med [batch .net](/dotnet/api/microsoft.azure.batch) -biblioteket.

> [!NOTE]
> Medan exemplen i den här artikeln fokuserar på batch .NET-, MS-MPI-och Windows Compute-noder, är de aktiviteter för flera instanser som beskrivs här tillämpliga på andra plattformar och tekniker (till exempel python och Intel MPI på Linux-noder).

## <a name="multi-instance-task-overview"></a>Översikt över aktiviteter med flera instanser

I batch körs varje aktivitet vanligt vis på en enskild Compute-nod – du skickar flera aktiviteter till ett jobb, och batch-tjänsten schemalägger varje aktivitet för körning på en nod. Genom att konfigurera en aktivitets inställningar för **flera instanser** kan du dock i stället skapa en primär aktivitet och flera under aktiviteter som sedan körs på flera noder.

:::image type="content" source="media/batch-mpi/batch_mpi_01.png" alt-text="Diagram som visar en översikt över inställningar för flera instanser.":::

När du skickar en aktivitet med inställningar för flera instanser till ett jobb utför batch flera steg som är unika för aktiviteter med flera instanser:

1. Batch-tjänsten skapar en **primär** och flera **under aktiviteter** baserat på inställningarna för flera instanser. Det totala antalet aktiviteter (primära plus alla under aktiviteter) matchar antalet **instanser** (Compute-noder) som du anger i inställningarna för flera instanser.
2. Batch anger en av datornoderna som **huvud server** och schemalägger den primära aktiviteten så att den körs på huvud servern. Den schemalägger de under aktiviteter som ska köras på resten av de Compute-noder som allokeras till aktiviteten för flera instanser, en under aktivitet per nod.
3. De primära och alla under aktiviteterna hämtar alla **delade resursfiler** som du anger i inställningarna för flera instanser.
4. När de delade resursfiler har hämtats, kör de primära och under aktiviteterna **koordinations kommandot** som du anger i inställningarna för flera instanser. Koordinations kommandot används vanligt vis för att förbereda noder för körning av uppgiften. Detta kan innefatta att starta bakgrunds tjänster (t. ex. [Microsoft-MPI](/message-passing-interface/microsoft-mpi) `smpd.exe` ) och kontrol lera att noderna är redo att bearbeta meddelanden mellan noder.
5. Den primära aktiviteten kör **program kommandot** på huvud-noden *när* koordinations kommandot har slutförts av den primära aktiviteten och alla under aktiviteter. Program kommandot är kommando raden för aktiviteten för flera instanser och körs bara av den primära aktiviteten. I en [MS-MPI](/message-passing-interface/microsoft-mpi) -baserad lösning är det här du kör ditt MPI-aktiverade program med hjälp av `mpiexec.exe` .

> [!NOTE]
> Även om den är funktionellt åtskild, är "Multi-instance Task" inte en unik typ av aktivitet som [StartTask](/dotnet/api/microsoft.azure.batch.starttask) eller [aktivitets typerna jobpreparationtask](/dotnet/api/microsoft.azure.batch.jobpreparationtask). Aktiviteten för flera instanser är helt enkelt en standard-batch-uppgift ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) i batch .net) vars inställningar för flera instanser har kon figurer ATS. I den här artikeln hänvisar vi till detta som **aktiviteten för flera instanser**.

## <a name="requirements-for-multi-instance-tasks"></a>Krav för aktiviteter med flera instanser

Aktiviteter med flera instanser kräver en pool med **kommunikation mellan noder** och med **inaktive rad körning av aktivitet inaktive rad**. Om du vill inaktivera körning av samtidiga aktiviteter ställer du in egenskapen [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) på 1.

> [!NOTE]
> Batch [begränsar](batch-quota-limit.md#pool-size-limits) storleken på en pool som har kommunikation mellan noder aktiverat.

Det här kodfragmentet visar hur du skapar en pool för aktiviteter med flera instanser med hjälp av batch .NET-biblioteket.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Om du försöker köra en multi-instance-aktivitet i en pool med kommunikation mellan noder, eller med ett *taskSlotsPerNode* -värde som är större än 1, så schemaläggs inte aktiviteten, utan tids gräns i "aktivt" läge.

### <a name="use-a-starttask-to-install-mpi"></a>Använd en StartTask för att installera MPI

Om du vill köra MPI-program med en multi-instance-aktivitet måste du först installera en MPI-implementering (MS-MPI eller Intel MPI, till exempel) på datornoderna i poolen. Detta är en lämplig tidpunkt för att använda en [StartTask](/dotnet/api/microsoft.azure.batch.starttask)som körs när en nod ansluter till en pool eller startas om. Det här kodfragmentet skapar en StartTask som anger installations paketet MS-MPI som en [resurs fil](/dotnet/api/microsoft.azure.batch.resourcefile). Start aktivitetens kommando rad körs när resurs filen har laddats ned till noden. I det här fallet utför kommando raden en obevakad installation av MS-MPI.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>Direkt åtkomst till fjärrminne (RDMA)

När du väljer en [RDMA-kompatibel storlek](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) , till exempel A9 för Compute-noderna i batch-poolen, kan ditt MPI-program dra nytta av Azures höga prestanda för direkt åtkomst till fjärrminne (RDMA) med låg latens.

Sök efter de storlekar som anges som "RDMA-kompatibel" i [storlekar för virtuella datorer i Azure](../virtual-machines/sizes.md) (för VirtualMachineConfiguration pooler) eller [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (för CloudServicesConfiguration pooler).

> [!NOTE]
> Om du vill dra nytta av RDMA på [Linux-datornoder](batch-linux-nodes.md)måste du använda **Intel-MPI** på noderna.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Skapa en aktivitet med flera instanser med batch .NET

Nu när vi har täckt kraven för poolen och MPI-paketet, ska vi skapa aktiviteten för flera instanser. I det här kodfragmentet skapar vi en standard- [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)och konfigurerar sedan dess [MultiInstanceSettings](/dotnet/api/microsoft.azure.batch.cloudtask) -egenskap. Som tidigare nämnts är aktiviteten för flera instanser ingen distinkt typ av aktivitet, men en standard-batch-aktivitet som kon figurer ATS med inställningar för flera instanser.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>Primär aktivitet och under aktiviteter

När du skapar inställningar för flera instanser för en aktivitet anger du antalet datornoder som ska utföra aktiviteten. När du skickar uppgiften till ett jobb skapar batch-tjänsten en **primär** aktivitet och tillräckligt många **under aktiviteter** som tillsammans matchar antalet noder som du har angett.

De här uppgifterna tilldelas ett heltals-ID i intervallet 0 till *numberOfInstances* -1. Uppgiften med ID 0 är den primära aktiviteten och alla andra ID: n är under aktiviteter. Om du till exempel skapar följande inställningar för flera instanser för en aktivitet skulle den primära aktiviteten ha ID: t 0, och under aktiviteterna har ID 1 till 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Huvud nod

När du skickar en aktivitet med flera instanser, anger batch-tjänsten en av datornoderna som "Master"-noden och schemalägger den primära aktiviteten så att den körs på huvudnoden. Under aktiviteterna är schemalagda att köras på resten av noderna som allokerats till aktiviteten för flera instanser.

## <a name="coordination-command"></a>Koordinations kommando

**Koordinations kommandot** utförs av både den primära aktiviteten och under aktiviteterna.

Anrops kommandot är blockerande – batch kör inte program kommandot förrän koordinations kommandot har returnerats för alla under aktiviteter. Koordinerings kommandot bör därför starta alla nödvändiga bakgrunds tjänster, kontrol lera att de är klara att använda och sedan avsluta. Detta koordinerings kommando för en lösning som använder MS-MPI version 7 startar till exempel tjänsten SMPD på noden och avslutas sedan:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Observera användningen av `start` i detta koordinations kommando. Detta är obligatoriskt eftersom `smpd.exe` programmet inte returnerar omedelbart efter körning. Utan att använda Start kommandot returneras inte det här koordinations kommandot, och därför blockeras program kommandot från att köras.

## <a name="application-command"></a>Program kommando

När den primära aktiviteten och alla under aktiviteter har slutfört körningen av koordinations kommandot, utförs *endast* körnings aktivitetens kommando rad av den primära aktiviteten. Vi anropar detta **program kommando** för att skilja den från koordinations kommandot.

För MS-MPI-program använder du kommandot Application för att köra ditt MPI-aktiverade program med `mpiexec.exe` . Här är till exempel ett program kommando för en lösning som använder MS-MPI version 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Eftersom MS-MPI `mpiexec.exe` använder `CCP_NODES` variabeln som standard (se [miljövariabler](#environment-variables)), undantas exempel kommando raden ovan.

## <a name="environment-variables"></a>Miljövariabler

Batch skapar flera [miljövariabler](batch-compute-node-environment-variables.md) som är speciella för aktiviteter med flera instanser på de Compute-noder som tilldelas en aktivitet med flera instanser. Dina samordnings-och program kommando rader kan referera till dessa miljövariabler, som de skript och program som de kör.

Följande miljövariabler skapas av batch-tjänsten för användning av aktiviteter med flera instanser:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Fullständig information om dessa och de andra miljövariablerna för batch Compute-noden, inklusive innehåll och synlighet, finns i [Compute Node-miljövariabler](batch-compute-node-environment-variables.md).

> [!TIP]
> [Kod exemplet för batch Linux MPI](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) innehåller ett exempel på hur flera av de här miljövariablerna kan användas.

## <a name="resource-files"></a>Resursfiler

Det finns två uppsättningar resursfiler att överväga för aktiviteter med flera instanser: **delade resursfiler** som *alla* aktiviteter hämtar (både primära och under aktiviteter) och **resursfiler** som anges för aktiviteten för flera instanser, och som *bara hämtas av den primära* aktiviteten.

Du kan ange en eller flera **gemensamma resursfiler** i inställningarna för flera instanser för en aktivitet. Dessa delade resursfiler laddas ned från [Azure Storage](../storage/common/storage-introduction.md) till varje nods **delade katalog** med den primära aktiviteten och alla under aktiviteter. Du kan komma åt den delade katalogen aktivitet från program-och samordnings kommando rader med hjälp av `AZ_BATCH_TASK_SHARED_DIR` miljövariabeln. `AZ_BATCH_TASK_SHARED_DIR`Sökvägen är identisk på varje nod som tilldelas till aktiviteten för flera instanser, och därför kan du dela ett enda koordinerings kommando mellan den primära och alla under aktiviteter. Batch har inte "dela" katalogen i en fjärråtkomst, men du kan använda den som en monterings-eller delnings punkt som tidigare nämnts i tipset på miljövariabler.

Resursfiler som du anger för aktiviteter med flera instanser hämtas som standard till aktivitetens arbets katalog `AZ_BATCH_TASK_WORKING_DIR` . Som det nämnts, i motsats till vanliga resursfiler, är det bara resursfiler som anges för aktiviteten för flera instanser som har angetts för den primära aktiviteten.

> [!IMPORTANT]
> Använd alltid miljövariablerna `AZ_BATCH_TASK_SHARED_DIR` och `AZ_BATCH_TASK_WORKING_DIR` för att referera till dessa kataloger i kommando raderna. Försök inte att skapa Sök vägarna manuellt.

## <a name="task-lifetime"></a>Aktivitetens livs längd

Den primära aktivitetens livs längd styr livs längden för hela aktiviteten för flera instanser. När den primära utgången avslutas avslutas alla under aktiviteter. Slut koden för den primära aktiviteten är avslutnings koden för aktiviteten och används därför för att avgöra om uppgiften lyckades eller Miss lyckas för nya försök.

Om någon av under aktiviteterna Miss lyckas avslutas med en returkod som inte är noll, till exempel hela aktiviteten för flera instanser Miss lyckas. Aktiviteten för flera instanser avbryts sedan och försöker igen, upp till gränsen för återförsök.

När du tar bort en aktivitet med flera instanser raderas även den primära och alla under aktiviteter av batch-tjänsten. Alla under aktivitets kataloger och deras filer tas bort från Compute-noderna, precis som för en standard uppgift.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) för en aktivitet med flera instanser, till exempel egenskaperna [MaxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount), [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)och [RetentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) , kan användas som de är för en standard uppgift och tillämpas på den primära och alla under aktiviteter. Men om du ändrar egenskapen theRetentionTime efter att ha lagt till aktiviteten Multiinstans i jobbet, tillämpas den här ändringen endast på den primära aktiviteten, och alla under aktiviteter fortsätter att använda den ursprungliga RetentionTime.

En Compute Nodes senaste uppgifts lista visar ID för en under aktivitet om den senaste aktiviteten ingick i en aktivitet med flera instanser.

## <a name="obtain-information-about-subtasks"></a>Hämta information om under aktiviteter

Om du vill få information om under aktiviteter med hjälp av batch .NET-biblioteket anropar du metoden [CloudTask. ListSubtasks](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) . Den här metoden returnerar information om alla under aktiviteter och information om den Compute-nod som utförde aktiviteterna. Med hjälp av den här informationen kan du fastställa varje under aktivitets rot Katalog, pool-ID, aktuellt tillstånd, slut kod med mera. Du kan använda den här informationen i kombination med metoden [PoolOperations. GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) för att hämta under aktivitetens filer. Observera att den här metoden inte returnerar information om den primära aktiviteten (ID 0).

> [!NOTE]
> Om inget annat anges gäller batch .NET-metoder som fungerar på [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) för flera instanser *enbart* för den primära aktiviteten. När du till exempel anropar metoden [CloudTask. ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) i en grupp med flera instanser returneras bara den primära aktivitetens filer.

Följande kodfragment visar hur du hämtar information om under aktiviteter, samt innehållet i begär ande filen från de noder där de kördes.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>Kodexempel

[MultiInstanceTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) kod exemplet på GitHub visar hur du använder en aktivitet med flera instanser för att köra ett [MS-MPI](/message-passing-interface/microsoft-mpi) -program i batch Compute-noder. Följ stegen nedan för att köra exemplet.

### <a name="preparation"></a>Förberedelse

1. Ladda ned [MS-MPI SDK och omdist installations](/message-passing-interface/microsoft-mpi) program och installera dem. Efter installationen kan du kontrol lera att miljövariablerna i MS-MPI har ställts in.
1. Bygg en *version* av [MPIHelloWorld](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) Sample MPI-programmet. Detta är det program som ska köras på datornoderna av aktiviteten för flera instanser.
1. Skapa en zip-fil som innehåller `MPIHelloWorld.exe` (som du skapade i steg 2) och `MSMpiSetup.exe` (som du laddade ned i steg 1). Du överför zip-filen som ett programpaket i nästa steg.
1. Använd [Azure Portal](https://portal.azure.com) för att skapa ett batch- [program](batch-application-packages.md) med namnet "MPIHelloWorld" och ange den zip-fil som du skapade i föregående steg som version "1,0" av programpaketet. Mer information finns i [Ladda upp och hantera program](batch-application-packages.md#upload-and-manage-applications) .

> [!TIP]
> Genom att skapa en *version* av `MPIHelloWorld.exe` , ser du till att du inte behöver ta med några ytterligare beroenden (till exempel `msvcp140d.dll` eller `vcruntime140d.dll` ) i programpaketet.

### <a name="execution"></a>Körnings-

1. Hämta [filen Azure-Batch-samples. zip](https://github.com/Azure/azure-batch-samples/archive/master.zip) från GitHub.
1. Öppna MultiInstanceTasks- **lösningen** i Visual Studio 2019. `MultiInstanceTasks.sln`Lösnings filen finns i:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Ange autentiseringsuppgifterna för batch-och lagrings kontot i `AccountSettings.settings` i **Microsoft.Azure.BatCH. Samples. common** Project.
1. **Skapa och kör** MultiInstanceTasks-lösningen för att köra exempel programmet MPI på Compute-noder i en batch-pool.
1. *Valfritt*: Använd [Azure Portal](https://portal.azure.com) eller [batch Explorer](https://azure.github.io/BatchExplorer/) för att undersöka exempel-pool, jobb och aktivitet ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") innan du tar bort resurserna.

> [!TIP]
> Du kan ladda ned [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) kostnads fritt om du inte redan har Visual Studio.

Utdata från `MultiInstanceTasks.exe` liknar följande:

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [MPI-stöd för Linux på Azure Batch](https://docs.microsoft.com/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch).
- Lär dig hur du [skapar pooler för Linux Compute Nodes](batch-linux-nodes.md) för användning i dina Azure Batch MPI-lösningar.
