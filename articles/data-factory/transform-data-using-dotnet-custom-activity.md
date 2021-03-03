---
title: Använd anpassade aktiviteter i en pipeline
description: Lär dig hur du skapar anpassade aktiviteter med hjälp av .NET och sedan använder aktiviteterna i en Azure Data Factory pipeline.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 64588d5968df635c3bb017bd1ff1d10951968f32
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724956"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-use-custom-activities.md)
> * [Aktuell version](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Det finns två typer av aktiviteter som du kan använda i en Azure Data Factory pipeline.

- [Data förflyttnings aktiviteter](copy-activity-overview.md) för att flytta data mellan [käll-och mottagar data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- [Data omvandlings aktiviteter](transform-data.md) för att transformera data med hjälp av beräknings tjänster som Azure HDInsight, Azure Batch och Azure Machine Learning.

Om du vill flytta data till/från ett data lager som Data Factory inte stöder, eller om du vill transformera/bearbeta data på ett sätt som inte stöds av Data Factory, kan du skapa en **anpassad aktivitet** med din egen data förflyttning eller omvandlings logik och använda aktiviteten i en pipeline. Den anpassade aktiviteten Kör din anpassade kod logik på en **Azure Batch** pool av virtuella datorer.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se följande artiklar om du är nybörjare på Azure Batch-tjänsten:

* [Azure Batch grunderna](../batch/batch-technical-overview.md) för en översikt över Azure Batchs tjänsten.
* [New-AzBatchAccount-](/powershell/module/az.batch/New-azBatchAccount) cmdlet för att skapa ett Azure Batch konto (eller) [Azure Portal](../batch/batch-account-create-portal.md) för att skapa Azure Batch-kontot med hjälp av Azure Portal. Detaljerade anvisningar om hur du använder cmdleten finns i [använda PowerShell för att hantera Azure Batch konto](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) artikel.
* [New-AzBatchPool-](/powershell/module/az.batch/New-AzBatchPool) cmdlet för att skapa en Azure Batch-pool.

> [!IMPORTANT]
> När du skapar en ny Azure Batch-pool måste "VirtualMachineConfiguration" användas och inte "CloudServiceConfiguration". Mer information finns i [vägledning för migrering av Azure Batch pool](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md). 

## <a name="azure-batch-linked-service"></a>Azure Batch länkad tjänst

Följande JSON definierar ett exempel på en länkad tjänst Azure Batch. Mer information finns i [beräknings miljöer som stöds av Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Mer information om hur du Azure Batch länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .

## <a name="custom-activity"></a>Anpassad aktivitet

Följande JSON-kodfragment definierar en pipeline med en enkel anpassad aktivitet. Aktivitets definitionen har en referens till den Azure Batch länkade tjänsten.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

I det här exemplet är helloworld.exe ett anpassat program som lagras i mappen customactv2/HelloWorld för det Azure Storage-konto som används i resourceLinkedService. Den anpassade aktiviteten skickar det här anpassade programmet som ska köras på Azure Batch. Du kan ersätta kommandot med alla önskade program som kan köras på mål åtgärds systemet för Azure Batch pool-noderna.

I följande tabell beskrivs namn och beskrivningar av egenskaper som är unika för den här aktiviteten.

| Egenskap              | Beskrivning                              | Obligatorisk |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Namn på aktiviteten i pipelinen     | Ja      |
| beskrivning           | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                  | För anpassad aktivitet är aktivitets typen **anpassad**. | Ja      |
| linkedServiceName     | Länkad tjänst till Azure Batch. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .  | Ja      |
| command               | Kommando för det anpassade program som ska köras. Om programmet redan är tillgängligt i noden Azure Batch pool kan resourceLinkedService och folderPath hoppas över. Du kan till exempel ange kommandot som `cmd /c dir` är inbyggt i Windows batch pool-noden. | Ja      |
| resourceLinkedService | Azure Storage länkad tjänst till lagrings kontot där det anpassade programmet lagras | Inga &#42;       |
| folderPath            | Sökväg till mappen för det anpassade programmet och alla dess beroenden<br/><br/>Om du har beroenden lagrade i undermappar – det vill säga i en hierarkisk mappstruktur under *folderPath* , är mappstrukturen för närvarande utplattad när filerna kopieras till Azure Batch. Det innebär att alla filer kopieras till en enda mapp utan undermappar. Undvik problemet genom att komprimera filerna, kopiera den komprimerade filen och packa upp den med anpassad kod på önskad plats. | Inga &#42;       |
| referenceObjects      | En matris med befintliga länkade tjänster och data uppsättningar. Refererade länkade tjänster och data uppsättningar skickas till det anpassade programmet i JSON-format så att din anpassade kod kan referera till resurser i Data Factory | Inga       |
| extendedProperties    | Användardefinierade egenskaper som kan skickas till det anpassade programmet i JSON-format så att din anpassade kod kan referera till ytterligare egenskaper | Inga       |
| retentionTimeInDays | Retentions tiden för de filer som skickas för den anpassade aktiviteten. Standardvärdet är 30 dagar. | Inga |

&#42; egenskaperna `resourceLinkedService` och `folderPath` måste antingen anges eller båda utelämnas.

> [!NOTE]
> Om du skickar länkade tjänster som referenceObjects i en anpassad aktivitet, är det en bra säkerhets rutin att skicka en Azure Key Vault aktive rad länkad tjänst (eftersom den inte innehåller några säkra strängar) och hämta autentiseringsuppgifterna med hjälp av hemligt namn direkt från Key Vault från koden. Du hittar ett exempel [här](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) som hänvisar till AKV-aktiverade länkade tjänster, hämtar autentiseringsuppgifterna från Key Vault och sedan ansluter till lagringen i koden.

## <a name="custom-activity-permissions"></a>Anpassade aktivitets behörigheter

Den anpassade aktiviteten anger Azure Batch automatiskt användar konto till *icke-administratörs åtkomst med uppgifts omfattning* (standard specifikationen för automatisk användare). Du kan inte ändra behörighets nivån för det automatiska användar kontot. Mer information finns i [Kör uppgifter under användar konton i batch | Konton för automatisk användare](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Kör kommandon

Du kan köra ett kommando direkt med anpassad aktivitet. I följande exempel körs kommandot "ECHO Hello World" på mål Azure Batch pool-noderna och skriver ut utdata till stdout.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Skicka objekt och egenskaper

Det här exemplet visar hur du kan använda referenceObjects och extendedProperties för att skicka Data Factory objekt och användardefinierade egenskaper till ditt anpassade program.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

När aktiviteten körs lagras referenceObjects och extendedProperties i följande filer som distribueras till samma körnings mapp i SampleApp.exe:

- `activity.json`

  Lagrar extendedProperties och egenskaper för den anpassade aktiviteten.

- `linkedServices.json`

  Lagrar en matris med länkade tjänster som definieras i egenskapen referenceObjects.

- `datasets.json`

  Lagrar en matris med data uppsättningar som definierats i egenskapen referenceObjects.

Följande exempel kod visar hur SampleApp.exe kan komma åt nödvändig information från JSON-filer:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Hämta utdata som körs

Du kan starta en pipeline-körning med följande PowerShell-kommando:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

När pipelinen körs kan du kontrol lera körnings resultatet med följande kommandon:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

**STDOUT** och **stderr** för ditt anpassade program sparas i **adfjobs** -behållaren i den Azure Storage länkade tjänsten som du definierade när du skapade Azure Batch länkad tjänst med ett GUID för uppgiften. Du kan hämta den detaljerade sökvägen från aktiviteten kör utdata som visas i följande kodfragment:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Om du vill använda innehållet i stdout.txt underordnade aktiviteter kan du hämta sökvägen till stdout.txt-filen i uttrycket " \@ Activity (' MyCustomActivity '). output. outputs [0]".

> [!IMPORTANT]
> - activity.jspå, linkedServices.jspå och datasets.jspå lagras i mappen körning i batch-aktiviteten. I det här exemplet lagras activity.jspå, linkedServices.jspå och datasets.jspå i `https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/` sökvägen. Om det behövs måste du rensa dem separat.
> - För länkade tjänster som använder Self-Hosted Integration Runtime krypteras känslig information, t. ex. nycklar och lösen ord, av Self-Hosted Integration Runtime för att säkerställa att autentiseringsuppgiften finns kvar i kundens definierade privata nätverks miljö. Vissa känsliga fält kan saknas när din anpassade program kod refereras till på det här sättet. Använd SecureString i extendedProperties i stället för att använda den länkade tjänst referensen om det behövs.

## <a name="pass-outputs-to-another-activity"></a>Skicka utdata till en annan aktivitet

Du kan skicka anpassade värden från koden i en anpassad aktivitet tillbaka till Azure Data Factory. Du kan göra det genom att skriva dem i `outputs.json` från ditt program. Data Factory kopierar innehållet i `outputs.json` och lägger till det i aktivitetens utdata som `customOutput` egenskap svärdet. (Storleks gränsen är 2 MB.) Om du vill använda innehållet i `outputs.json` underordnade aktiviteter kan du hämta värdet med hjälp av uttrycket `@activity('<MyCustomActivity>').output.customOutput` .

## <a name="retrieve-securestring-outputs"></a>Hämta SecureString-utdata

Känsliga egenskaps värden som anges som typ *SecureString*, som du ser i några av exemplen i den här artikeln, maskeras ut i fliken övervakning i Data Factory användar gränssnitt.  I faktisk pipeline-körning serialiseras dock egenskapen *SecureString* som JSON i `activity.json` filen som oformaterad text. Exempel:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Den här serialiseringen är inte riktigt säker och är inte avsedd att vara säker. Avsikten är att tipsa Data Factory för att maskera värdet på fliken övervakning.

För att få åtkomst till egenskaper av typen *SecureString* från en anpassad aktivitet, kan du läsa `activity.json` filen som placeras i samma mapp som din. EXE, deserialisera JSON och öppna sedan JSON-egenskapen (extendedProperties => [propertyName] => värde).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> Jämför v2-aktivitet för anpassad aktivitet och version 1 (anpassad) DotNet

I Azure Data Factory version 1 implementerar du en (anpassad) DotNet-aktivitet genom att skapa ett biblioteks projekt för .NET-klass med en klass som implementerar- `Execute` metoden för `IDotNetActivity` gränssnittet. Länkade tjänster, data uppsättningar och utökade egenskaper i JSON-nyttolasten för en (anpassad) DotNet-aktivitet skickas till körnings metoden som starkt skrivna objekt. Mer information om beteendet för version 1 finns i [(anpassad) dotNet i version 1](v1/data-factory-use-custom-activities.md). På grund av den här implementeringen måste din version 1 DotNet-aktivitets kod riktas mot .NET Framework 4.5.2. En DotNet-aktivitet från version 1 måste också köras på Windows-baserade Azure Batch pool-noder.

I den anpassade aktiviteten Azure Data Factory v2 behöver du inte implementera ett .NET-gränssnitt. Du kan nu köra kommandon, skript och din egen anpassade kod direkt, kompilerad som en körbar fil. Om du vill konfigurera den här implementeringen anger du `Command` egenskapen tillsammans med `folderPath` egenskapen. Den anpassade aktiviteten laddar upp den körbara filen och dess beroenden till `folderpath` och kör kommandot åt dig.

De länkade tjänsterna, data uppsättningarna (definierade i referenceObjects) och utökade egenskaper som definierats i JSON-nyttolasten för en Data Factory v2 anpassad aktivitet kan nås av den körbara filen som JSON-filer. Du kan komma åt de obligatoriska egenskaperna med hjälp av en JSON-serialisering som visas i exemplet ovan SampleApp.exe kod.

Med de ändringar som introducerades i den anpassade aktiviteten Data Factory v2 kan du skriva din anpassade kod logik på önskat språk och köra den på Windows-och Linux-operativsystem som stöds av Azure Batch.

I följande tabell beskrivs skillnaderna mellan den anpassade aktiviteten Data Factory v2 och Data Factory version 1 (anpassad) DotNet-aktivitet:

|Skillnader      | Anpassad aktivitet      | version 1 (anpassad) DotNet-aktivitet      |
| ---- | ---- | ---- |
|Så här definieras anpassad logik      |Genom att tillhandahålla en körbar fil      |Genom att implementera en .NET-DLL      |
|Körnings miljö för den anpassade logiken      |Windows eller Linux      |Windows (.NET Framework 4.5.2)      |
|Kör skript      |Stöder körning av skript direkt (till exempel "cmd/c ECHO Hello World" på Windows VM)      |Kräver implementering i .NET-DLL      |
|Data mängd krävs      |Valfritt      |Krävs för att kedja aktiviteter och skicka information      |
|Skicka information från aktivitet till anpassad logik      |Genom ReferenceObjects (LinkedServices och data uppsättningar) och ExtendedProperties (anpassade egenskaper)      |Via ExtendedProperties (anpassade egenskaper), indata och utdata för data uppsättningar      |
|Hämta information i anpassad logik      |Parsar activity.jspå, linkedServices.jspå och datasets.jslagrade i samma mapp som den körbara filen      |Via .NET SDK (.NET Frame 4.5.2)      |
|Loggning      |Skriver direkt till STDOUT      |Implementera loggar i .NET-DLL      |

Om du har en befintlig .NET-kod skriven för en version 1 (anpassad) DotNet-aktivitet måste du ändra koden för att den ska fungera med den aktuella versionen av den anpassade aktiviteten. Uppdatera din kod genom att följa dessa rikt linjer på hög nivå:

  - Ändra projektet från ett .NET-klass bibliotek till en-konsol program.
  - Starta ditt program med- `Main` metoden. `Execute` `IDotNetActivity` Gränssnittets metod krävs inte längre.
  - Läsa och analysera länkade tjänster, data uppsättningar och aktiviteter med en JSON-serialisering och inte som starkt skrivna objekt. Överför värdena för obligatoriska egenskaper till din huvudsakliga anpassade kod logik. Se föregående SampleApp.exes kod som ett exempel.
  - Objektet för loggning stöds inte längre. Utdata från den körbara filen kan skrivas ut till-konsolen och sparas i stdout.txt.
  - NuGet-paketet Microsoft. Azure. Management. DataFactories krävs inte längre.
  - Kompilera din kod, överför den körbara filen och dess beroenden till Azure Storage och definiera sökvägen i `folderPath` egenskapen.

Ett fullständigt exempel på hur end-to-end-DLL och pipeline-exemplet som beskrivs i artikeln Data Factory version 1 [använder anpassade aktiviteter i en Azure Data Factory pipelinen](./v1/data-factory-use-custom-activities.md) kan skrivas om som en Data Factory anpassad aktivitet, se [Data Factory anpassad aktivitets exempel](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatisk skalning av Azure Batch

Du kan också skapa en Azure Batch pool med funktionen för **autoskalning** . Du kan till exempel skapa en Azure Batch-pool med 0 dedikerade virtuella datorer och en formel för autoskalning baserat på antalet väntande aktiviteter.

Exempel formeln här uppnår följande beteende: när poolen skapas första gången börjar den med 1 virtuell dator. $PendingTasks måttet definierar antalet aktiviteter som körs och är aktiva (i kö). Formeln hittar det genomsnittliga antalet väntande aktiviteter under de senaste 180 sekunderna och anger TargetDedicated i enlighet med detta. Det garanterar att TargetDedicated aldrig hamnar bortom 25 virtuella datorer. När nya uppgifter skickas, växer automatiskt poolen och när aktiviteterna har slutförts blir de virtuella datorerna gratis en i taget och den automatiska skalningen minskar de virtuella datorerna. startingNumberOfVMs och maxNumberofVMs kan anpassas efter dina behov.

Autoskalning-formel:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Se [automatisk skalning av Compute-noder i en Azure Batch pool](../batch/batch-automatic-scaling.md) för mer information.

Om poolen använder standard- [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)kan batch-tjänsten ta 15-30 minuter att förbereda den virtuella datorn innan den anpassade aktiviteten körs. Om poolen använder en annan autoScaleEvaluationInterval kan batch-tjänsten ta autoScaleEvaluationInterval + 10 minuter.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)