---
title: 'Självstudie – köra en parallell arbets belastning med python-API: et'
description: Självstudie – Bearbeta mediefiler parallellt med ffmpeg i Azure Batch med hjälp av klientbiblioteket Batch Python
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7013ef1e9b6dda4554b665fa8199edb86a8a9adb
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106520"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Självstudie: Köra en parallell arbetsbelastning med Azure Batch med hjälp av Python API

Använd Azure Batch till att effektivt köra storskaliga parallella program och HPC-program (databehandling med höga prestanda) i Azure. I den här självstudien går vi igenom ett Python-exempel på att köra en parallell arbetsbelastning med Batch. Du lär dig ett vanligt arbetsflöde för Batch-program och hur du interagerar programmatiskt med Batch- och Storage-resurser. Lär dig att:

> [!div class="checklist"]
> * autentisera med Batch- och Storage-konton
> * ladda upp indatafiler till Storage
> * skapa en pool med beräkningsnoder för att köra ett program
> * skapa ett jobb och uppgifter som bearbetar indatafilerna
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

I den här självstudien konverterar du MP4-mediefiler parallellt till MP3-format med verktyget [ffmpeg](https://ffmpeg.org/) som har öppen källkod. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Python 2.7 eller 3.3 eller senare](https://www.python.org/downloads/)

* [pip](https://pip.pypa.io/en/stable/installing/)-pakethanterare

* Ett Azure Batch-konto och ett länkat Azure Storage-konto. Information om hur du skapar de här kontona finns Batch-snabbstarterna som du kommer åt via [Azure-portalen](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Ladda ned och kör exemplet

### <a name="download-the-sample"></a>Ladda ned exemplet

[Ladda ned eller klona exempelappen](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) från GitHub. Om du vill klona lagringsplatsen för exempelappen med en Git-klient använder du följande kommando:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Navigera till den katalog som innehåller filen `batch_python_tutorial_ffmpeg.py`.

I Python-miljön installerar du de nödvändiga paketen med `pip`.

```bash
pip install -r requirements.txt
```

Öppna filen `config.py`. Uppdatera autentiseringssträngarna med Batch- och lagringskontouppgifter med de värden som är unika för dina konton. Exempel:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>Kör appen

Kör skriptet så här:

```
python batch_python_tutorial_ffmpeg.py
```

När du kör exempelappen ser utdata i konsolen ut ungefär så här. Under körningen uppstår det en paus vid `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` medan poolens beräkningsnoder startas. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Gå till Batch-kontot i Azure-portalen för att övervaka poolen, beräkningsnoderna, jobbet och uppgifterna. Om du till exempel vill visa en värme karta över datornoderna i din pool klickar du på **pooler**  >  *LinuxFFmpegPool*.

När uppgifter körs ser den termiska kartan ut ungefär så här:

![Termisk karta för pool](./media/tutorial-parallel-python/pool.png)

Den normala körnings tiden är ungefär **5 minuter** när du kör programmet med standard konfigurationen. Att skapa poolen är det som tar mest tid. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Granska koden

I följande avsnitt bryter vi ned exempelprogrammet i de steg som utförs när en arbetsbelastning bearbetas i Batch-tjänsten. Följ med i Python-koden medan du läser resten av den här artikeln eftersom inte alla kodrader i exemplet beskrivs.

### <a name="authenticate-blob-and-batch-clients"></a>Autentisera Blob- och Batch-klienter

För att interagera med ett lagringskonto använder appen paketet [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) för att skapa ett [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice)-objekt.

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

Appen skapar ett [BatchServiceClient](/python/api/azure.batch.batchserviceclient)-objekt för att skapa och hantera pooler, jobb och aktiviteter i Batch-tjänsten. Batch-klienten i exemplet använder autentisering med delad nyckel. Batch har även stöd för autentisering via [Azure Active Directory](batch-aad-auth.md) när enskilda användare eller övervakade program ska autentiseras.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Ladda upp indatafiler

Appen använder referensen `blob_client` för att skapa en lagringscontainer för MP4-indatafilerna och en container för uppgiftsutdata. Sedan anropas `upload_file_to_container`-funktionen för att ladda upp MP4-filer i den lokala `InputFiles`-katalogen till containern. De lagrade filerna har definierats som Batch [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile)-objekt som Batch senare kan hämta till beräkningsnoder.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Skapa en pool med beräkningsnoder

Därefter skapar exempelkoden en pool med beräkningsnoder i Batch-kontot med ett anrop till `create_pool`. Den här definierade funktionen använder Batch-klassen [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter) för att ange antalet noder, VM-storlek och en poolkonfiguration. Här anger ett [VirtualMachineConfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration)-objekt en [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) till en Ubuntu Server 18.04 LTS-avbildning som har publicerats på Azure Marketplace. Batch har stöd för ett stort antal Linux- och Windows Server-avbildningar på Azure Marketplace samt för anpassade VM-avbildningar.

Antalet noder och VM-storleken anges med definierade konstanter. Batch har stöd för dedikerade noder och [noder med låg prioritet](batch-low-pri-vms.md), och du kan använda antingen eller båda i dina pooler. Dedikerade noder är reserverade för din pool. Noder med låg prioritet erbjuds till ett reducerat pris från VM-överskottskapacitet i Azure. Noder med låg prioritet är inte tillgängliga om Azure inte har tillräckligt med kapacitet. Exemplet skapar som standard en pool som endast innehåller 5 noder med låg prioritet i storleken *Standard_A1_v2*. 

Förutom fysiska nodegenskaper innehåller den här poolkonfigurationen ett [StartTask](/python/api/azure-batch/azure.batch.models.starttask)-objekt. StartTask körs på varje nod när noden ansluter till poolen och varje gång en nod startas om. I det här exemplet kör StartTask Bash shell-kommandon för att installera paketet ffmpeg och beroenden på noderna.

Metoden [pool.add](/python/api/azure-batch/azure.batch.operations.pooloperations) skickar poolen till Batch-tjänsten.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Skapa ett jobb

Ett Batch-jobb anger en pool för körning av uppgifter, samt valfria inställningar som en prioritet och ett schema för arbetet. I exemplet skapas ett jobb med ett anrop till `create_job`. Den här definierade funktionen använder klassen [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter) för att skapa ett jobb på din pool. Metoden [job.add](/python/api/azure-batch/azure.batch.operations.joboperations) skickar poolen till Batch-tjänsten. Från början har jobbet inga uppgifter.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Skapa uppgifter

Appen skapar aktiviteter i jobbet med ett anrop till `add_tasks`. Den här definierade funktionen skapar en lista med aktivitetsobjekt med hjälp av klassen [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter). Varje aktivitet kör ffmpeg för bearbetning av ett `resource_files`-indataobjekt med en `command_line`-parameter. ffmpeg installerades tidigare på varje nod när poolen skapades. Här kör kommandoraden ffmpeg för att konvertera varje MP4-indatafil (video) till en MP3-fil (ljud).

I exemplet skapas ett [OutputFile](/python/api/azure-batch/azure.batch.models.outputfile)-objekt för MP3-filen när du kör kommandoraden. Varje uppgifts utdatafiler (i det här fallet en) laddas upp till en container i länkade lagringskontot med uppgiftsegenskapen `output_files`.

Sedan lägger appen till aktiviteter i jobbet med metoden [task.add_collection](/python/api/azure-batch/azure.batch.operations.taskoperations) som köar dem för att köras på beräkningsnoderna. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Övervaka aktiviteter

När aktiviteter läggs till i ett jobb placerar Batch dem automatiskt i kö och schemalägger dem för körning vid beräkningsnoder i den associerade poolen. Baserat på de inställningar du anger sköter Batch all köhantering, all schemaläggning, alla omförsök och all annan uppgiftsadministration åt dig. 

Du kan övervaka aktivitetskörningen på många sätt. Funktionen `wait_for_tasks_to_complete` i det här exemplet använder objektet [TaskState](/python/api/azure-batch/azure.batch.models.taskstate) för att övervaka aktiviteter för ett visst tillstånd, i det här fallet tillståndet slutfört, inom en tidsgräns.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Rensa resurser

När uppgifterna har körts tar appen automatiskt bort den lagringscontainer som skapades och du får möjlighet att ta bort Batch-poolen och jobbet. Klasserna [JobOperations](/python/api/azure-batch/azure.batch.operations.joboperations) och [PoolOperations](/python/api/azure-batch/azure.batch.operations.pooloperations) i BatchClient har båda borttagningsmetoder som anropas om du bekräftar borttagningen. Även om du inte debiteras för själva jobben och uppgifterna så debiteras du för beräkningsnoder. Vi rekommenderar därför att du endast allokerar pooler efter behov. När du tar bort poolen raderas alla aktivitetsutdata på noderna. In- och utdatafilerna ligger däremot kvar i lagringskontot.

När de inte längre behövs tar du bort resursgruppen, Batch-kontot och lagringskontot. Om du vill göra det i Azure-portalen väljer du resursgruppen för Batch-kontot och klickar på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * autentisera med Batch- och Storage-konton
> * ladda upp indatafiler till Storage
> * skapa en pool med beräkningsnoder för att köra ett program
> * skapa ett jobb och uppgifter som bearbetar indatafilerna
> * övervaka körningen av uppgiften
> * hämta utdatafilerna.

Fler exempel på hur du använder Python API till att schemalägga och bearbeta Batch-arbetsbelastningar finns i exemplen på GitHub.

> [!div class="nextstepaction"]
> [Batch Python-exempel](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

