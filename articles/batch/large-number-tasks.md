---
title: Skicka in ett stort antal uppgifter till ett batch-jobb
description: Lär dig hur du effektivt skickar ett mycket stort antal uppgifter i ett enda Azure Batch jobb.
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831524"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Skicka in ett stort antal uppgifter till ett batch-jobb

När du kör storskaliga Azure Batch arbets belastningar kanske du vill skicka flera tusen tusen, hundratals tusen eller ännu fler uppgifter till ett enda jobb.

Den här artikeln visar hur du skickar in ett stort antal uppgifter med betydligt större data flöde till ett enda batch-jobb. När uppgifterna har skickats anger de batchkön för bearbetning i den pool som du anger för jobbet.

## <a name="use-task-collections"></a>Använd aktivitets samlingar

När du lägger till ett stort antal aktiviteter använder du lämpliga metoder eller överlagringar som tillhandahålls av batch-API: erna för att lägga till uppgifter som en *samling* i stället för en i taget. I allmänhet skapar du en uppgifts samling genom att definiera aktiviteter när du itererar över en uppsättning indatafiler eller parametrar för jobbet.

Den maximala storleken på aktivitets samlingen som du kan lägga till i ett enda anrop beror på vilket batch-API som du använder.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>API: er som tillåter samlingar på upp till 100 uppgifter

De här batch-API: erna begränsar samlingen till 100 uppgifter. Gränsen kan vara mindre beroende på aktiviteternas storlek (till exempel om aktiviteterna har ett stort antal resursfiler eller miljövariabler).

- [REST-API](/rest/api/batchservice/task/addcollection)
- [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js-API](/javascript/api/@azure/batch/task)

När du använder dessa API: er måste du tillhandahålla logik för att dela upp antalet aktiviteter för att uppfylla samlings gränsen och för att hantera fel och försök i händelse av ytterligare fel i aktiviteten. Om en aktivitets samling är för stor för att kunna läggas till genererar begäran ett fel och bör försöka igen med färre uppgifter.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>API: er som tillåter samlingar med större mängder aktiviteter

Andra batch-API: er stöder mycket större aktivitets samlingar, begränsas bara av RAM-tillgänglighet på den sändande klienten. De här API: erna hanterar transparent delning av aktivitets samlingen i "segment" för API: er på lägre nivå och nya försök för aktivitets tillägg.

- [.NET-API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java-API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- [Azure Batch CLI-tillägg](batch-cli-templates.md) med batch CLI-mallar
- [Python SDK-tillägg](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>Öka genom strömning av uppgifts överföring

Det kan ta lite tid att lägga till en stor mängd aktiviteter i ett jobb. Till exempel kan det ta upp till en minut att lägga till 20 000 uppgifter via .NET-API: et. Beroende på batch-API och din arbets belastning kan du förbättra aktivitets data flödet genom att ändra ett eller flera av följande.

### <a name="task-size"></a>Aktivitets storlek

Att lägga till stora uppgifter tar längre tid än att lägga till mindre. Om du vill minska storleken på varje aktivitet i en samling kan du förenkla aktivitetens kommando rad, minska antalet miljövariabler eller hantera krav för aktivitets körningen mer effektivt.

I stället för att använda ett stort antal resursfiler måste du till exempel installera aktivitets beroenden med en [Start aktivitet](jobs-and-tasks.md#start-task) i poolen eller använda ett [programpaket](batch-application-packages.md) eller en [Docker-behållare](batch-docker-container-workloads.md).

### <a name="number-of-parallel-operations"></a>Antal parallella åtgärder

Beroende på batch-API: et kan du öka data flödet genom att öka det maximala antalet samtidiga åtgärder av batch-klienten. Konfigurera den här inställningen med egenskapen [BatchClientParallelOptions. MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) i .NET-API: et eller `threads` parameter för metoder som [TASKOPERATIONS.ADD_COLLECTION](/python/api/azure-batch/azure.batch.operations.TaskOperations) i batch python SDK-tillägget. (Den här egenskapen är inte tillgänglig i den inbyggda python SDK: n för batch.)

Som standard är den här egenskapen inställd på 1, men du kan ställa in den på högre sätt för att förbättra genomflödet av åtgärder. Du förbrukar större data flöden genom att använda nätverks bandbredden och vissa CPU-prestanda. Uppgifts flödet ökar med upp till 100 gånger `MaxDegreeOfParallelism` eller `threads` . I praktiken bör du ange antalet samtidiga åtgärder till under 100.

 Azure Batch CLI-tillägget med batch-mallar ökar antalet samtidiga åtgärder automatiskt baserat på antalet tillgängliga kärnor, men den här egenskapen kan inte konfigureras i CLI.

### <a name="http-connection-limits"></a>Begränsningar för HTTP-anslutning

Att ha många samtidiga HTTP-anslutningar kan begränsa prestandan för batch-klienten när du lägger till ett stort antal aktiviteter. Vissa API: er begränsar antalet HTTP-anslutningar. När du utvecklar med .NET-API: t kan [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) -egenskapen ha värdet 2 som standard. Vi rekommenderar att du ökar värdet till ett tal nära eller större än antalet parallella åtgärder.

## <a name="example-batch-net"></a>Exempel: batch .NET

Följande C#-kodfragment visar inställningar som du kan konfigurera när du lägger till ett stort antal aktiviteter med hjälp av batch .NET-API: et.

Öka aktivitetens data flöde genom att öka värdet för egenskapen [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) för [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient). Exempel:

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

Lägg till en aktivitets samling i jobbet med lämplig överlagring för metoden [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) eller [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) . Exempel:

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>Exempel: batch CLI-tillägg

Använd Azure Batch CLI-tillägg med [batch CLI-mallar](batch-cli-templates.md)för att skapa en JSON-fil för jobbmall som innehåller en [aktivitets fabrik](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md). Uppgifts fabriken konfigurerar en samling relaterade uppgifter för ett jobb från en enda aktivitets definition.  

Följande är en exempel jobb mal len för ett endimensionellt parameter rensnings jobb med ett stort antal uppgifter (i det här fallet 250 000). Aktivitetens kommando rad är ett enkelt `echo` kommando.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Information om hur du kör ett jobb med mallen finns i [använda Azure Batch CLI-mallar och fil överföring](batch-cli-templates.md).

## <a name="example-batch-python-sdk-extension"></a>Exempel: batch python SDK-tillägg

Om du vill använda Azure Batch python SDK-tillägget måste du först installera python SDK och tillägget:

```
pip install azure-batch
pip install azure-batch-extensions
```

Konfigurera en `BatchExtensionsClient` som använder SDK-tillägget:

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

Skapa en samling uppgifter som ska läggas till i ett jobb. Exempel:

```python
tasks = list()
# Populate the list with your tasks
...
```

Lägg till aktivitets samlingen med [Task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations). Ange `threads` parametern för att öka antalet samtidiga åtgärder:

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch python SDK-tillägget stöder också att du lägger till aktivitets parametrar till jobb med hjälp av en JSON-specifikation för en aktivitets fabrik. Konfigurera till exempel jobb parametrar för en parameter svep som liknar den i föregående exempel för batch CLI-mallen:

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

Lägg till jobb parametrarna till jobbet. Ange `threads` parametern för att öka antalet samtidiga åtgärder:

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att använda Azure Batch CLI-tillägget med [batch CLI-mallar](batch-cli-templates.md).
- Läs mer om [batch python SDK-tillägget](https://pypi.org/project/azure-batch-extensions/).
- Läs om [metod tips för Azure Batch](best-practices.md).
