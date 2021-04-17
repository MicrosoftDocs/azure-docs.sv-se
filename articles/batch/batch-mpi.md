---
title: Använda aktiviteter med flera instanser för att köra MPI-program
description: Lär dig hur du kör Message Passing Interface-program (MPI) med hjälp av aktivitetstyp med flera instanser i Azure Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: e96cfb89b186d69f6ad969949b8df609956114d2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389408"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>Använda aktiviteter med flera instanser för att Message Passing Interface MPI-program i Batch

Med aktiviteter med flera instanser kan du köra en Azure Batch på flera beräkningsnoder samtidigt. De här uppgifterna möjliggör scenarier med databehandling med höga prestanda som Message Passing Interface MPI-program (MPI) i Batch. I den här artikeln får du lära dig hur du kör uppgifter med flera instanser med hjälp av [Batch .NET-biblioteket.](/dotnet/api/microsoft.azure.batch)

> [!NOTE]
> Exemplen i den här artikeln fokuserar på Batch .NET, MS-MPI och Windows-beräkningsnoder, men de aktivitetsbegrepp med flera instanser som beskrivs här gäller för andra plattformar och tekniker (till exempel Python och Intel MPI på Linux-noder).

## <a name="multi-instance-task-overview"></a>Översikt över aktiviteter med flera instanser

I Batch körs varje aktivitet normalt på en enda beräkningsnod – du skickar flera aktiviteter till ett jobb och Batch-tjänsten schemalägger varje aktivitet för körning på en nod. Men genom att konfigurera en uppgifts inställningar för flera instanser uppmanar du Batch att i stället skapa en primär uppgift och flera underuppgifter som sedan körs på flera noder.

:::image type="content" source="media/batch-mpi/batch-mpi-01.png" alt-text="Diagram som visar en översikt över inställningar för flera instanser.":::

När du skickar en uppgift med inställningar för flera instanser till ett jobb utför Batch flera steg som är unika för aktiviteter med flera instanser:

1. Batch-tjänsten skapar **en primär** och flera **underuppgifter** baserat på inställningarna för flera instanser. Det totala antalet aktiviteter (primär plus alla underuppgifter) matchar antalet instanser **(beräkningsnoder)** som du anger i inställningarna för flera instanser.
2. Batch anger en av beräkningsnoderna som **huvudnod** och schemalägger den primära aktiviteten så att den körs på huvudnoden. Den schemalägger underaktiviteterna så att de körs på resten av beräkningsnoderna som allokerats till uppgiften med flera instanser, en underuppgift per nod.
3. Den primära och alla underaktiviteter laddar ned **alla vanliga resursfiler som** du anger i inställningarna för flera instanser.
4. När de gemensamma resursfilerna har laddats ned kör  de primära och underaktiviteterna samordningskommandot som du anger i inställningarna för flera instanser. Samordningskommandot används vanligtvis för att förbereda noder för att köra uppgiften. Detta kan inkludera start av bakgrundstjänster (till exempel [Microsoft MPI)](/message-passing-interface/microsoft-mpi) och verifiering av att noderna är redo att `smpd.exe` bearbeta meddelanden mellan noder.
5. Den primära uppgiften kör **programkommandot**  på huvudnoden när samordningskommandot har slutförts av den primära och alla underaktiviteter. Programkommandot är kommandoraden för själva uppgiften med flera instanser och körs endast av den primära aktiviteten. I en [MS-MPI-baserad](/message-passing-interface/microsoft-mpi) lösning är det här du kör ditt MPI-aktiverade program med hjälp av `mpiexec.exe` .

> [!NOTE]
> Även om den är funktionellt distinkt är "multiinstansaktiviteten" inte en unik uppgiftstyp som [StartTask](/dotnet/api/microsoft.azure.batch.starttask) eller [JobPreparationTask.](/dotnet/api/microsoft.azure.batch.jobpreparationtask) Uppgiften för flera instanser är helt enkelt en vanlig Batch-uppgift ([CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) i Batch .NET) vars inställningar för flera instanser har konfigurerats. I den här artikeln refererar vi till detta som **uppgiften med flera instanser.**

## <a name="requirements-for-multi-instance-tasks"></a>Krav för uppgifter med flera instanser

Aktiviteter med flera instanser kräver en pool med **kommunikation mellan noder aktiverad,** och med samtidig **uppgiftskörning inaktiverad**. Om du vill inaktivera körning av samtidiga aktiviteter anger du egenskapen [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) till 1.

> [!NOTE]
> Batch [begränsar](batch-quota-limit.md#pool-size-limits) storleken på en pool som har kommunikation mellan noder aktiverat.

Det här kodfragmentet visar hur du skapar en pool för aktiviteter med flera instanser med hjälp av Batch .NET-biblioteket.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
        imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
        nodeAgentSkuId: "batch.node.windows amd64");

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.TaskSlotsPerNode = 1;
```

> [!NOTE]
> Om du försöker köra en aktivitet med flera instanser i en pool med internnodkommunikation inaktiverad, eller med ett *taskSlotsPerNode-värde* större än 1, schemaläggs aldrig aktiviteten – den förblir obestämd i tillståndet "aktiv".

### <a name="use-a-starttask-to-install-mpi"></a>Använda en StartTask för att installera MPI

Om du vill köra MPI-program med en aktivitet med flera instanser måste du först installera en MPI-implementering (till exempel MS-MPI eller Intel MPI) på beräkningsnoderna i poolen. Det här är ett bra tillfälle att använda [en StartTask](/dotnet/api/microsoft.azure.batch.starttask), som körs när en nod ansluter till en pool eller startas om. Det här kodfragmentet skapar en StartTask som anger MS-MPI-konfigurationspaketet som en [resursfil](/dotnet/api/microsoft.azure.batch.resourcefile). Startaktivitetens kommandorad körs när resursfilen har laddats ned till noden. I det här fallet utför kommandoraden en obevakad installation av MS-MPI.

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

### <a name="remote-direct-memory-access-rdma"></a>Direktåtkomst till fjärrminne (RDMA)

När du väljer en [RDMA-kompatibel](../virtual-machines/sizes-hpc.md?toc=/azure/virtual-machines/windows/toc.json) storlek, till exempel A9 för beräkningsnoderna i Batch-poolen, kan MPI-programmet dra nytta av Azures RDMA-nätverk (Remote Direct Memory Access) med korta svarstider.

Leta efter storlekar som anges som "RDMA-kompatibla" i Storlekar för virtuella datorer i [Azure](../virtual-machines/sizes.md) (för VirtualMachineConfiguration-pooler) eller Storlekar för [Cloud Services](../cloud-services/cloud-services-sizes-specs.md) (för CloudServicesConfiguration-pooler).

> [!NOTE]
> Om du vill dra nytta av RDMA [på Linux-beräkningsnoder](batch-linux-nodes.md)måste du använda **Intel MPI** på noderna.

## <a name="create-a-multi-instance-task-with-batch-net"></a>Skapa en aktivitet med flera instanser med Batch .NET

Nu när vi har gått in på poolkraven och installationen av MPI-paketet ska vi skapa uppgiften för flera instanser. I det här kodfragmentet skapar vi en [Standard CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask)och konfigurerar sedan dess [MultiInstanceSettings-egenskap.](/dotnet/api/microsoft.azure.batch.cloudtask) Som tidigare nämnts är uppgiften med flera instanser inte en distinkt uppgiftstyp, utan en Standard Batch-uppgift som konfigurerats med inställningar för flera instanser.

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

## <a name="primary-task-and-subtasks"></a>Primär uppgift och underuppgifter

När du skapar inställningarna för flera instanser för en aktivitet anger du antalet beräkningsnoder som ska köra uppgiften. När du skickar uppgiften till ett jobb  skapar Batch-tjänsten en primär uppgift och tillräckligt med underaktiviteter som tillsammans matchar det antal noder som du har angett. 

Dessa uppgifter tilldelas ett heltals-ID i intervallet 0 *till numberOfInstances* – 1. Uppgiften med ID 0 är den primära uppgiften och alla andra ID:n är underuppgifter. Om du till exempel skapar följande inställningar för flera instanser för en aktivitet, skulle den primära aktiviteten ha ID:t 0 och underaktiviteterna skulle ha ID:n 1 till och med 9.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>Huvudnod

När du skickar en aktivitet med flera instanser utser Batch-tjänsten en av beräkningsnoderna som huvudnod och schemalägger den primära aktiviteten att köras på huvudnoden. Underaktiviteterna schemaläggs att köras på resten av noderna som allokerats till uppgiften med flera instanser.

## <a name="coordination-command"></a>Samordningskommando

**Samordningskommandot** körs av både primära och underordnade uppgifter.

Anropet av samordningskommandot blockerar – Batch kör inte programkommandot förrän samordningskommandot har returnerats för alla underuppgifter. Samordningskommandot bör därför starta alla nödvändiga bakgrundstjänster, kontrollera att de är redo att användas och sedan avsluta. Till exempel startar det här samordningskommandot för en lösning som använder MS-MPI version 7 SMPD-tjänsten på noden och avslutar sedan:

`cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d`

Observera användningen av i `start` det här samordningskommandot. Detta krävs eftersom programmet `smpd.exe` inte returneras omedelbart efter körningen. Utan startkommandot skulle det här samordningskommandot inte returnera och skulle därför blockera programkommandot från att köras.

## <a name="application-command"></a>Programkommando

När den primära uppgiften och alla underaktiviteter har körts klart med samordningskommandot körs multiinstansaktivitetens kommandorad endast av den primära *aktiviteten*. Vi anropar det här **programkommandot** för att skilja det från samordningskommandot.

För MS-MPI-program använder du programkommandot för att köra ditt MPI-aktiverade program med `mpiexec.exe` . Här är till exempel ett programkommando för en lösning som använder MS-MPI version 7:

`cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe`

> [!NOTE]
> Eftersom MS-MPI använder variabeln som standard `mpiexec.exe` `CCP_NODES` (se [Miljövariabler](#environment-variables)), utesluter exempelprogrammets kommandorad ovan den.

## <a name="environment-variables"></a>Miljövariabler

Batch skapar flera [miljövariabler som](batch-compute-node-environment-variables.md) är specifika för aktiviteter med flera instanser på beräkningsnoderna som allokerats till en aktivitet med flera instanser. Dina kommandorader för samordning och program kan referera till dessa miljövariabler, liksom de skript och program som de kör.

Följande miljövariabler skapas av Batch-tjänsten för användning av aktiviteter med flera instanser:

- `CCP_NODES`
- `AZ_BATCH_NODE_LIST`
- `AZ_BATCH_HOST_LIST`
- `AZ_BATCH_MASTER_NODE`
- `AZ_BATCH_TASK_SHARED_DIR`
- `AZ_BATCH_IS_CURRENT_NODE_MASTER`

Fullständig information om dessa och de andra miljövariablerna för Batch-beräkningsnoder, inklusive deras innehåll och synlighet, finns i [Miljövariabler för beräkningsnoder.](batch-compute-node-environment-variables.md)

> [!TIP]
> Batch [Linux MPI-kodexempel](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch/article_samples/mpi) innehåller ett exempel på hur flera av dessa miljövariabler kan användas.

## <a name="resource-files"></a>Resursfiler

Det finns två uppsättningar resursfiler att överväga för aktiviteter  med flera **instanser:** vanliga resursfiler  som alla aktiviteter laddar ned (både  primära och underuppgifter) och resursfilerna som anges för själva uppgiften med flera instanser, som endast den primära aktiviteten hämtar.

Du kan ange en eller flera **vanliga resursfiler** i inställningarna för flera instanser för en aktivitet. Dessa vanliga resursfiler laddas ned från [Azure Storage](../storage/common/storage-introduction.md) till  varje nods delade katalog för aktiviteter av den primära och alla underuppgifter. Du kan komma åt den delade katalogen för aktiviteter från program- och samordningskommandorader med hjälp av `AZ_BATCH_TASK_SHARED_DIR` miljövariabeln . Sökvägen är identisk på varje nod som tilldelats till uppgiften med flera instanser, så du kan dela ett enda samordningskommando mellan den primära och `AZ_BATCH_TASK_SHARED_DIR` alla underuppgifter. Batch "delar inte" katalogen i en fjärråtkomsts sense, men du kan använda den som en monterings- eller resurspunkt som nämnts tidigare i tipset om miljövariabler.

Resursfiler som du anger för själva uppgiften med flera instanser laddas ned till aktivitetens arbetskatalog, `AZ_BATCH_TASK_WORKING_DIR` , som standard. Till skillnad från vanliga resursfiler laddar som sagt bara den primära aktiviteten ned resursfiler som angetts för själva uppgiften med flera instanser.

> [!IMPORTANT]
> Använd alltid miljövariablerna `AZ_BATCH_TASK_SHARED_DIR` och för att referera till dessa kataloger i dina `AZ_BATCH_TASK_WORKING_DIR` kommandorader. Försök inte att skapa sökvägarna manuellt.

## <a name="task-lifetime"></a>Aktivitetens livslängd

Livslängden för den primära aktiviteten styr livslängden för hela uppgiften med flera instanser. När den primära avslutas avslutas alla underaktiviteter. Slutkoden för den primära är slutkoden för uppgiften och används därför för att fastställa om aktiviteten lyckades eller misslyckades i syfte att försöka igen.

Om någon av underaktiviteterna misslyckas och till exempel avslutas med en returkod som inte är noll misslyckas hela uppgiften med flera instanser. Uppgiften för flera instanser avslutas sedan och försöks igen, upp till dess gräns för återförsök.

När du tar bort en aktivitet med flera instanser tas även den primära och alla underaktiviteter bort av Batch-tjänsten. Alla underuppgiftskataloger och deras filer tas bort från beräkningsnoderna, precis som för en standardaktivitet.

[TaskConstraints](/dotnet/api/microsoft.azure.batch.taskconstraints) för en aktivitet med flera instanser, till exempel [egenskaperna MaxTaskRetryCount,](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount) [MaxWallClockTime](/dotnet/api/microsoft.azure.batch.taskconstraints.maxwallclocktime)och [RetentionTime,](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) respekteras som de är för en standarduppgift och gäller för den primära och alla underaktiviteter. Men om du ändrar egenskapenRetentionTime när du har lagt till uppgiften med flera instanser i jobbet tillämpas den här ändringen endast på den primära aktiviteten, och alla underaktiviteter fortsätter att använda den ursprungliga RetentionTime.

En beräkningsnods senaste uppgiftslista visar ID:t för en underaktivitet om den senaste uppgiften ingick i en aktivitet med flera instanser.

## <a name="obtain-information-about-subtasks"></a>Hämta information om underuppgifter

Om du vill hämta information om underaktiviteter med hjälp av Batch .NET-biblioteket anropar du [metoden CloudTask.ListSubtasks.](/dotnet/api/microsoft.azure.batch.cloudtask.listsubtasks) Den här metoden returnerar information om alla underaktiviteter och information om beräkningsnoden som körde aktiviteterna. Med den här informationen kan du fastställa rotkatalogen för varje undergrupp, pool-ID, aktuellt tillstånd, slutkod med mera. Du kan använda den här informationen i kombination med [metoden PoolOperations.GetNodeFile](/dotnet/api/microsoft.azure.batch.pooloperations.getnodefile) för att hämta undergruppens filer. Observera att den här metoden inte returnerar information för den primära aktiviteten (ID 0).

> [!NOTE]
> Om inget annat anges gäller Batch .NET-metoder som fungerar på själva [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) med flera instanser *endast* den primära uppgiften. När du till exempel anropar [metoden CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask.listnodefiles) för en aktivitet med flera instanser returneras bara den primära aktivitetens filer.

Följande kodfragment visar hur du hämtar information om underuppgifter, samt begär filinnehåll från noderna som de kördes på.

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

[MultiInstanceTasks-kodexempel](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks) på GitHub visar hur du använder en aktivitet med flera instanser för att köra ett [MS-MPI-program](/message-passing-interface/microsoft-mpi) på Batch-beräkningsnoder. Följ stegen nedan för att köra exemplet.

### <a name="preparation"></a>Förberedelse

1. Ladda ned [MS-MPI SDK och Redist-installationsprogram](/message-passing-interface/microsoft-mpi) och installera dem. Efter installationen kan du kontrollera att MS-MPI-miljövariablerna har angetts.
1. Skapa en *version av* [MPIHelloWorld-exempelprogrammet](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld) för MPI. Det här är det program som ska köras på beräkningsnoder av aktiviteten med flera instanser.
1. Skapa en zip-fil `MPIHelloWorld.exe` som innehåller (som du skapade i steg 2) `MSMpiSetup.exe` och (som du laddade ned i steg 1). Du laddar upp zip-filen som ett programpaket i nästa steg.
1. Använd [Azure Portal](https://portal.azure.com) för att skapa ett [Batch-program](batch-application-packages.md) med namnet "MPIHelloWorld" och ange zip-filen som du skapade i föregående steg som version "1.0" av programpaketet. Mer information [finns i Ladda upp](batch-application-packages.md#upload-and-manage-applications) och hantera program.

> [!TIP]
> Genom att *skapa* en version av behöver du inte inkludera några ytterligare `MPIHelloWorld.exe` beroenden (till exempel eller ) i `msvcp140d.dll` `vcruntime140d.dll` programpaketet.

### <a name="execution"></a>Körnings-

1. Ladda ned [.zip-filen azure-batch-samples](https://github.com/Azure/azure-batch-samples/archive/master.zip) från GitHub.
1. Öppna MultiInstanceTasks-lösningen i Visual Studio 2019.  `MultiInstanceTasks.sln`Lösningsfilen finns i:

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
1. Ange autentiseringsuppgifterna för Batch- och `AccountSettings.settings` **Storage-kontot iMicrosoft.Azure.Batch.Samples.Common-projektet.**
1. **Skapa och kör** MultiInstanceTasks-lösningen för att köra MPI-exempelprogrammet på beräkningsnoder i en Batch-pool.
1. *Valfritt:* Använd [Azure Portal](https://portal.azure.com) eller [Batch Explorer](https://azure.github.io/BatchExplorer/) för att undersöka exempelpoolen, jobbet och uppgiften ("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask") innan du tar bort resurserna.

> [!TIP]
> Du kan [ladda Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) utan kostnad om du inte redan har Visual Studio.

Utdata `MultiInstanceTasks.exe` från liknar följande:

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
- Lär dig hur du [skapar pooler med Linux-beräkningsnoder](batch-linux-nodes.md) för användning i dina Azure Batch MPI-lösningar.
