---
title: Skapa/Schemalägg pipelines, kedje aktiviteter i Data Factory
description: Lär dig att skapa en datapipeline i Azure Data Factory att flytta och transformera data. Skapa ett data drivet arbets flöde för att skapa redo att använda information.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9cc81a8e157c244828a15ac82913ce9a88c3d34f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376878"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines och aktiviteter i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-create-pipelines.md)
> * [Version 2 (aktuell version)](../concepts-pipelines-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [pipelines i v2](../concepts-pipelines-activities.md).

I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för dina dataförflyttnings- och databearbetningsscenarier.

> [!NOTE]
> Den här artikeln förutsätter att du har gått igenom [introduktionen till Azure Data Factory](data-factory-introduction.md). Om du inte har praktisk erfarenhet av att skapa data fabriker kan du gå igenom [självstudierna för datatransformering](data-factory-build-your-first-pipeline.md) och/eller [data flytt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att förstå den här artikeln bättre.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras på dina data. Du kan till exempel använda en kopierings aktivitet för att kopiera data från en SQL Server-databas till en Azure-Blob Storage. Använd sedan en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta/transformera data från Blob Storage för att producera utdata. Använd slutligen en andra kopierings aktivitet för att kopiera utdata till en Azure Synapse-analys över vilka Business Intelligence (BI) rapporterings lösningar skapas.

En aktivitet kan ha noll eller flera [indatauppsättningar](data-factory-create-datasets.md) och kan producera en eller flera [utdatauppsättningar](data-factory-create-datasets.md). I följande diagram visas förhållandet mellan pipeline, aktivitet och datauppsättning i Data Factory:

![Relation mellan pipeline, aktivitet och data uppsättning](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Med en pipeline kan du hantera aktiviteter som en uppsättning i stället för var och en. Du kan till exempel distribuera, schemalägga, pausa och återuppta en pipeline i stället för att hantera aktiviteter i pipelinen separat.

Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering. Varje aktivitet kan ha noll eller fler data [uppsättningar](data-factory-create-datasets.md) för indata och skapa en eller flera data uppsättningar av utdata.

En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. När du har skapat en datauppsättning kan du använda den med aktiviteter i en pipeline. Till exempel kan en datauppsättning vara en in-/utdatauppsättning för en kopieringsaktivitet eller en HDInsightHive-aktivitet. Mer information om datauppsättning finns i artikeln [Datauppsättningar i Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

Mer information finns i artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Mer information finns i artikeln [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Anpassa .NET-aktiviteter
Om du behöver flytta data till/från ett data lager som kopierings aktiviteten inte stöder, eller om du vill transformera data med din egen logik, skapar du en **Anpassad .net-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Schemalägga pipeliner
En pipeline är endast aktiv mellan **Start** tid och **slut** tid. Den körs inte före start tiden eller efter slut tiden. Om pipelinen är pausad utförs den inte, oavsett start-och slut tid. För att en pipeline ska kunna köras bör den inte pausas. Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) för att förstå hur schemaläggning och körning fungerar i Azure Data Factory.

## <a name="pipeline-json"></a>Pipeline JSON
Nu tar vi en närmare titt på hur en pipeline definieras i JSON-format. Den allmänna strukturen för en pipeline ser ut så här:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets":
        [
        ]
    }
}
```

| Tagg | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Namnet på pipeline. Ange ett namn som representerar åtgärden som pipeline utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstavs siffra eller ett under streck ( \_ )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", " \* ", "%", "&", ":", " \\ "</li></ul> |Ja |
| beskrivning | Ange texten som beskriver vad pipeline används till. |Ja |
| activities | Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Se nästa avsnitt för information om JSON-elementet för aktiviteter. | Ja |
| start | Start datum/tid för pipelinen. Måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: `2016-10-14T16:32:41Z`. <br/><br/>Det går att ange en lokal tid, till exempel en EST-tid. Här är ett exempel: `2016-02-27T06:00:00-05:00` ", som är 6 – Est.<br/><br/>Start-och slut egenskaperna anger den aktiva perioden för pipelinen. Utgående segment skapas endast med i den här aktiva perioden. |Inga<br/><br/>Om du anger ett värde för egenskapen end måste du ange ett värde för egenskapen start.<br/><br/>Start-och slut tiderna kan båda vara tomma för att skapa en pipeline. Du måste ange båda värdena om du vill ange en aktiv period som pipelinen ska köras i. Om du inte anger start-och slut tider när du skapar en pipeline kan du ange dem med hjälp av Set-AzDataFactoryPipelineActivePeriod cmdlet senare. |
| slut | Slutdatum/tid för pipelinen. Om det anges måste det vara i ISO-format. Exempelvis: `2016-10-14T17:32:41Z` <br/><br/>Det går att ange en lokal tid, till exempel en EST-tid. Här är ett exempel: `2016-02-27T06:00:00-05:00` , som är 6 am EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, anger du 9999-09-09 som värde för slutegenskapen. <br/><br/> En pipeline är endast aktiv mellan start tid och slut tid. Den körs inte före start tiden eller efter slut tiden. Om pipelinen är pausad utförs den inte, oavsett start-och slut tid. För att en pipeline ska kunna köras bör den inte pausas. Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) för att förstå hur schemaläggning och körning fungerar i Azure Data Factory. |Inga <br/><br/>Om du anger ett värde för egenskapen start måste du ange ett värde för egenskapen End.<br/><br/>Se information om **Start** egenskapen. |
| isPaused | Om värdet är true körs inte pipelinen. Det är i paus läge. Standardvärde = falskt. Du kan använda den här egenskapen för att aktivera eller inaktivera en pipeline. |Inga |
| pipelineMode | Metoden för att schemalägga körningar för pipelinen. Tillåtna värden är: schemalagda (standard), Databasmigrering.<br/><br/>Schemalagd anger att pipelinen körs vid ett visst tidsintervall enligt dess aktiva period (start-och slut tid). ' Databasmigrering ' anger att pipelinen bara körs en gång. Databasmigrering pipelines när de har skapats kan inte ändras/uppdateras för närvarande. Se [Databasmigrering pipeline](#onetime-pipeline) för information om inställningen Databasmigrering. |Inga |
| expirationTime | Tiden efter det att den har skapats för vilken [engångs pipelinen](#onetime-pipeline) är giltig och bör vara etablerad. Om det inte finns några aktiva, misslyckade eller väntande körningar tas pipelinen bort automatiskt när den når förfallo tiden. Standardvärdet: `"expirationTime": "3.00:00:00"`|Inga |
| datauppsättningar |Lista över data uppsättningar som ska användas av aktiviteter som definierats i pipelinen. Den här egenskapen kan användas för att definiera data uppsättningar som är speciella för den här pipelinen och som inte har definierats i data fabriken. Data uppsättningar som definieras i den här pipelinen kan endast användas av den här pipelinen och kan inte delas. Mer information finns i [omfattnings data uppsättningar](data-factory-create-datasets.md#scoped-datasets) . |Inga |

## <a name="activity-json"></a>Aktivitets-JSON
Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Varje aktivitet har följande övergripande struktur:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

I följande tabell beskrivs egenskaperna i definitionen för aktivitets-JSON:

| Tagg | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name | Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstavs siffra eller ett under streck ( \_ )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Ja |
| beskrivning | Text som beskriver vad aktiviteten används till |Ja |
| typ | Typ av aktivitet. Se avsnittet [data förflyttnings aktiviteter](#data-movement-activities) och [data omvandlings aktiviteter](#data-transformation-activities) för olika typer av aktiviteter. |Ja |
| tillför |Ingångs tabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ja |
| utdata |Utgående tabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för aktiviteter i HDInsight och Azure Machine Learning Studio (klassisk) batch-bedömnings aktivitet <br/><br/>Nej för alla andra |
| typeProperties |Egenskaperna i **typeProperties** -avsnittet är beroende av typen av aktivitet. Om du vill visa typegenskaper för en aktivitet klickar du på länkarna till aktiviteten i föregående avsnitt. | Inga |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om den inte anges används standard principerna. |Inga |
| scheduler | Egenskapen Scheduler används för att definiera önskad schemaläggning för aktiviteten. Dess under egenskaper är desamma som i [egenskapen Availability i en data uppsättning](data-factory-create-datasets.md#dataset-availability). |Inga |

### <a name="policies"></a>Principer
Principer påverkar körnings beteendet för en aktivitet, särskilt när en tabell sektor bearbetas. I följande tabell finns information.

| Egenskap | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Max värde: 10 |1 |Antalet samtidiga körningar av aktiviteten.<br/><br/>Det avgör antalet parallella aktivitets körningar som kan ske på olika sektorer. Om en aktivitet till exempel behöver gå igenom en stor uppsättning tillgängliga data, desto högre samtidiga värde, desto snabbare data bearbetning. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Bestämmer ordningen på data sektorer som bearbetas.<br/><br/>Om du till exempel har 2 segment (en på 4pm och en annan på 17) och båda väntar på att köras. Om du ställer in executionPriorityOrder som NewestFirst bearbetas sektorn på 5 PM först. På samma sätt som om du anger att executionPriorityORder ska vara OldestFIrst bearbetas sektorn på 4 PM. |
| retry |Integer<br/><br/>Max värdet kan vara 10 |0 |Antal försök innan data bearbetningen för sektorn markeras som ett haveri. Aktivitets körningen för en data sektor görs igen till det angivna antalet försök. Återförsöket görs så snart som möjligt efter fel. |
| timeout |TimeSpan |00:00:00 |Tids gräns för aktiviteten. Exempel: 00:10:00 (betyder timeout 10 minuter)<br/><br/>Om ett värde inte anges eller är 0, är tids gränsen oändlig.<br/><br/>Om data bearbetnings tiden i en sektor överskrider tids gräns värdet avbryts det och systemet försöker försöka utföra bearbetningen igen. Antalet återförsök beror på egenskapen försök igen. När timeout inträffar anges statusen till stängningsåtgärd. |
| förskjutning |TimeSpan |00:00:00 |Ange fördröjningen innan data bearbetning av sektorn startar.<br/><br/>Körningen av en aktivitet för en data sektor startas efter fördröjningen efter den förväntade körnings tiden.<br/><br/>Exempel: 00:10:00 (betyder fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Max värde: 10 |1 |Antalet nya försök innan sektor körningen misslyckades.<br/><br/>longRetry-försök fördelas med longRetryInterval. Om du behöver ange en tid mellan återförsök ska du använda longRetry. Om både återförsök och longRetry har angetts innehåller varje longRetry-försök nya försök och det högsta antalet försök är att försöka igen * longRetry.<br/><br/>Om vi till exempel har följande inställningar i aktivitets principen:<br/>Nytt försök: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det bara finns en sektor att köra (status väntar) och aktivitets körningen Miss lyckas varje gång. Från början skulle det finnas tre körnings försök i följd. Efter varje försök skulle sektor statusen att försöka igen. Efter de första tre försöken är segmentets status LongRetry.<br/><br/>Efter en timme (det vill säga longRetryInteval värde), skulle det finnas en annan uppsättning av tre körnings försök i följd. Därefter skulle sektor statusen att Miss lyckas och inga fler återförsök skulle göras. Därför gjordes 6 försök.<br/><br/>Om körningen lyckas är segment statusen klar och inga fler återförsök görs.<br/><br/>longRetry kan användas i situationer där beroende data anländer till icke-deterministiska tider eller om den övergripande miljön är flaky under vilken data bearbetning sker. I sådana fall kan du göra nya försök en efter det att en annan kanske inte kan hjälpa dig och göra det när ett tidsintervall ger önskad utdata.<br/><br/>Varnings ord: Ange inte höga värden för longRetry eller longRetryInterval. Vanligt vis innebär högre värden andra problem med systemet. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjningen mellan långsamma försök |

## <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exemplet kopierar [kopierings aktiviteten](data-factory-data-movement-activities.md) data från en Azure Blob storage till Azure SQL Database.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```

Observera följande punkter:

* I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
* Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. I artikeln [Datauppsättningar](data-factory-create-datasets.md) finns information om hur du definierar datauppsättningar i JSON.
* I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. I avsnittet [data förflyttnings aktiviteter](#data-movement-activities) klickar du på det data lager som du vill använda som källa eller mottagare för att lära dig mer om att flytta data till/från det data lagret.

En fullständig genom gång av hur du skapar denna pipeline finns i [Självstudier: kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="sample-transformation-pipeline"></a>Exempel på transfomeringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **HDInsightHive** i avsnittet **activities**. I det här exemplet transformerar [HDInsight Hive-aktiviteten](data-factory-hive-activity.md) data från Azure Blob Storage genom att köra en Hive-skriptfil på ett Azure HDInsight Hadoop-kluster.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Observera följande punkter:

* I activities-avsnittet finns det bara en aktivitet vars **typ** anges till **HDInsightHive**.
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i containern **adfgetstarted**.
* `defines`Avsnittet används för att ange körnings inställningar som skickas till Hive-skriptet som Hive-konfigurations värden (t `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` . ex.).

Avsnittet **typeProperties** är olika för varje transformeringsaktivitet. Om du vill veta mer om typ egenskaper som stöds för en omvandlings aktivitet klickar du på omvandlings aktiviteten i tabellen [data omvandlings aktiviteter](#data-transformation-activities) .

En fullständig genom gång av hur du skapar denna pipeline finns i [Självstudier: skapa din första pipeline för att bearbeta data med Hadoop-kluster](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
De två föregående exemplen innehåller bara en aktivitet. Du kan fler än en aktivitet i en pipeline.

Om du har flera aktiviteter i en pipeline och utdata för en aktivitet inte är indata för en annan aktivitet, kan aktiviteterna köras parallellt om indata-sektorerna för aktiviteterna är klara.

Du kan länka två aktiviteter genom att ha data uppsättningen för utdata för en aktivitet som indata-datauppsättningen för den andra aktiviteten. Den andra aktiviteten körs bara när den första är slutförd.

![Länkning av aktiviteter i samma pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

I det här exemplet har pipelinen två aktiviteter: Activity1 och Activity2. Activity1 tar Dataset1 som indata och genererar en utdata-Dataset2. Aktiviteten tar Dataset2 som indata och producerar utdata Dataset3. Eftersom utdata från Activity1 (Dataset2) är indata för Activity2 körs Activity2 bara när aktiviteten har slutförts och genererar Dataset2-sektorn. Om Activity1 Miss lyckas av någon anledning och inte skapar Dataset2-sektorn, körs inte aktivitet 2 för denna sektor (till exempel: 9 AM till 10 AM).

Du kan också kedja aktiviteter som finns i olika pipeliner.

![Länkning av aktiviteter i två pipeliner](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

I det här exemplet har Pipeline1 bara en aktivitet som tar Dataset1 som indata och producerar Dataset2 som utdata. Pipeline2 har också bara en aktivitet som tar Dataset2 som indata och Dataset3 som utdata.

Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Skapa och övervaka pipeliner
Du kan skapa pipelines med hjälp av något av dessa verktyg eller SDK: er.

- Guiden Kopiera
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-mall
- REST-API
- .NET-API

I följande själv studie kurser finns stegvisa anvisningar för hur du skapar pipelines med hjälp av något av dessa verktyg eller SDK: er.

- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Bygg en pipeline med en data förflyttnings aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline har skapats/distribuerats kan du hantera och övervaka dina pipelines med hjälp av Azure Portal blad eller övervaka och hantera appen. I följande avsnitt finns stegvisa anvisningar.

- [Övervaka och hantera pipelines med hjälp av Azure Portal blad](data-factory-monitor-manage-pipelines.md).
- [Övervaka och hantera pipelines med hjälp av övervaka och hantera app](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Databasmigrering-pipeline
Du kan skapa och schemalägga en pipeline så att den körs regelbundet (till exempel: varje timme eller varje dag) inom den Start-och slut tid som du anger i pipeline-definitionen. Mer information finns i schemalägga aktiviteter. Du kan också skapa en pipeline som bara körs en gång. Det gör du genom att ställa in egenskapen **pipelineMode** i pipeline-definitionen på **Databasmigrering** som visas i följande JSON-exempel. Standardvärdet för den här egenskapen har **schemalagts**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

. Tänk på följande:

* **Start** -och **slut** tider för pipelinen har inte angetts.
* **Tillgänglighet** för indata-och utdata-datauppsättningar anges (**frekvens** och **intervall**), även om Data Factory inte använder värdena.
* Diagramvyn visar inte en pipeline i taget. Det här beteendet är avsiktligt.
* Det går inte att uppdatera en pipeline med en tids period. Du kan klona en engångs pipeline, byta namn på den, uppdatera egenskaperna och distribuera den för att skapa en ny.

## <a name="next-steps"></a>Nästa steg
- Mer information om data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) .
- Mer information om hur pipelines schemaläggs och körs finns i [schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md) artikel.
