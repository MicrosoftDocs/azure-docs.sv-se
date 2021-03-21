---
title: Referens för Azure Data Factory-JSON-skript
description: Tillhandahåller JSON-scheman för Data Factory entiteter.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f17d851e517e1ea0297bf8169c42496068e57533
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371268"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Referens för Azure Data Factory-JSON-skript
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory.


Den här artikeln innehåller JSON-scheman och exempel för att definiera Azure Data Factory entiteter (pipeline, aktivitet, data uppsättning och länkad tjänst).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
Hög nivå strukturen för en pipeline-definition är följande:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

I följande tabell beskrivs egenskaperna inom pipelinens JSON-definition:

| Egenskap | Beskrivning | Obligatorisk
-------- | ----------- | --------
| name | Namnet på pipeline. Ange ett namn som representerar åtgärden som aktiviteten eller pipelinen har kon figurer ATS att göra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstavs siffra eller ett under streck ( \_ )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Ja |
| beskrivning |Text som beskriver vad aktiviteten eller pipelinen används för | Inga |
| activities | Innehåller en lista med aktiviteter. | Ja |
| start |Start datum/tid för pipelinen. Måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41. <br/><br/>Det går att ange en lokal tid, till exempel en EST-tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00` , som är 6 am EST.<br/><br/>Start-och slut egenskaperna anger den aktiva perioden för pipelinen. Utgående segment skapas endast med i den här aktiva perioden. |Inga<br/><br/>Om du anger ett värde för egenskapen end måste du ange ett värde för egenskapen start.<br/><br/>Start-och slut tiderna kan båda vara tomma för att skapa en pipeline. Du måste ange båda värdena om du vill ange en aktiv period som pipelinen ska köras i. Om du inte anger start-och slut tider när du skapar en pipeline kan du ange dem med hjälp av Set-AzDataFactoryPipelineActivePeriod cmdlet senare. |
| slut |Slutdatum/tid för pipelinen. Om det anges måste det vara i ISO-format. Exempel: 2014-10-14T17:32:41 <br/><br/>Det går att ange en lokal tid, till exempel en EST-tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00` , som är 6 am EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, anger du 9999-09-09 som värde för slutegenskapen. |Inga <br/><br/>Om du anger ett värde för egenskapen start måste du ange ett värde för egenskapen End.<br/><br/>Se information om **Start** egenskapen. |
| isPaused |Om värdet är true körs inte pipelinen. Standardvärde = falskt. Du kan använda den här egenskapen för att aktivera eller inaktivera. |Inga |
| pipelineMode |Metoden för att schemalägga körningar för pipelinen. Tillåtna värden är: schemalagda (standard), Databasmigrering.<br/><br/>Schemalagd anger att pipelinen körs vid ett visst tidsintervall enligt dess aktiva period (start-och slut tid). ' Databasmigrering ' anger att pipelinen bara körs en gång. Databasmigrering pipelines när de har skapats kan inte ändras/uppdateras för närvarande. Se [Databasmigrering pipeline](data-factory-create-pipelines.md#onetime-pipeline) för information om inställningen Databasmigrering. |Inga |
| expirationTime |Tid efter hur lång tid som pipelinen är giltig och som ska vara etablerad. Om det inte finns några aktiva, misslyckade eller väntande körningar, tas pipelinen bort automatiskt när den når förfallo tiden. |Inga |


## <a name="activity"></a>Aktivitet
Hög nivå strukturen för en aktivitet i en pipeline-definition (aktivitets element) är följande:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
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

I följande tabell beskrivs egenskaperna i aktivitets-JSON-definitionen:

| Tagg | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten har kon figurer ATS att göra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstavs siffra eller ett under streck ( \_ )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |Ja |
| beskrivning |Text som beskriver vad aktiviteten används för. |Inga |
| typ |Anger typen av aktivitet. Se avsnittet [data lager](#data-stores) och [data omvandlings aktiviteter](#data-transformation-activities) för olika typer av aktiviteter. |Ja |
| tillför |Ingångs tabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nej för HDInsightStreaming-och SqlServerStoredProcedure-aktiviteter <br/> <br/> Ja för alla andra |
| utdata |Utgående tabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight-aktiviteter, Azure Machine Learning Studio (klassiska) aktiviteter och lagrad procedur aktivitet. <br/><br/>Nej för alla andra |
| typeProperties |Egenskaperna i typeProperties-avsnittet är beroende av typen av aktivitet. |Inga |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om den inte anges används standard principerna. |Inga |
| scheduler |Egenskapen Scheduler används för att definiera önskad schemaläggning för aktiviteten. Dess under egenskaper är desamma som i [egenskapen Availability i en data uppsättning](data-factory-create-datasets.md#dataset-availability). |Inga |

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

### <a name="typeproperties-section"></a>avsnittet typeProperties
Avsnittet typeProperties är olika för varje aktivitet. Omvandlings aktiviteter har bara typ egenskaperna. Se avsnittet [data omvandlings aktiviteter](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandlings aktiviteter i en pipeline.

**Kopierings aktiviteten** har två underavsnitt i typeProperties-avsnittet: **källa** och **mottagare**. Se avsnittet [data lager](#data-stores) i den här artikeln för JSON-exempel som visar hur du använder ett data lager som källa och/eller mottagare.

### <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Observera följande punkter:

* I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
* Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**.
* I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

Se avsnittet [data lager](#data-stores) i den här artikeln för JSON-exempel som visar hur du använder ett data lager som källa och/eller mottagare.

En fullständig genom gång av hur du skapar denna pipeline finns i [Självstudier: kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Exempel på transfomeringspipeline
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Observera följande punkter:

* I activities-avsnittet finns det bara en aktivitet vars **typ** anges till **HDInsightHive**.
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i containern **adfgetstarted**.
* Avsnittet **definierar** används för att ange körnings inställningar som skickas till Hive-skriptet som Hive-konfigurations värden (t `${hiveconf:inputtable}` `${hiveconf:partitionedtable}` . ex.).

Se avsnittet [data omvandlings aktiviteter](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandlings aktiviteter i en pipeline.

En fullständig genom gång av hur du skapar denna pipeline finns i [Självstudier: skapa din första pipeline för att bearbeta data med Hadoop-kluster](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Länkad tjänst
En hög nivå struktur för en länkad tjänst definition är följande:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

I följande tabell beskrivs egenskaperna i aktivitets-JSON-definitionen:

| Egenskap | Beskrivning | Obligatorisk |
| -------- | ----------- | -------- |
| name | Namnet på den länkade tjänsten. | Ja |
| egenskaper-typ | Typ av länkad tjänst. Exempel: Azure Storage Azure SQL Database. |
| typeProperties | Avsnittet typeProperties innehåller element som är olika för varje data lager eller beräknings miljö. Se avsnittet data lager för alla länkade tjänster och [beräknings miljöer](#compute-environments) för alla de data lager som är länkade till alla beräknade tjänster |

## <a name="dataset"></a>Datamängd
En data uppsättning i Azure Data Factory definieras enligt följande:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

I följande tabell beskrivs egenskaperna i ovanstående JSON:

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| name | Data uppsättningens namn. Se [Azure Data Factory namngivnings regler](data-factory-naming-rules.md) för namngivnings regler. |Ja |NA |
| typ | Typ av data uppsättning. Ange en av de typer som stöds av Azure Data Factory (till exempel: AzureBlob, AzureSqlTable). Se avsnittet [data lager](#data-stores) för alla data lager och data uppsättnings typer som stöds av Data Factory. |
| hierarkistruktur | Schema för data uppsättningen. Den innehåller kolumner, deras typer osv. | Inga |NA |
| typeProperties | Egenskaper som motsvarar den valda typen. Se avsnittet [data lager](#data-stores) för typer som stöds och deras egenskaper. |Ja |NA |
| extern | Boolesk flagga för att ange om en data uppsättning uttryckligen skapas av en Data Factory-pipeline eller inte. |Inga |falskt |
| availability | Definierar bearbetnings fönstret eller segmenterings modellen för data uppsättnings produktionen. Mer information om den sammanslagna segmenterings modellen finns i avsnittet om [schemaläggning och körning](data-factory-scheduling-and-execution.md) . |Ja |NA |
| policy |Definierar villkoret eller villkoret som data uppsättnings sektorerna måste uppfylla. <br/><br/>Mer information finns i avsnittet princip för data uppsättning. |Inga |NA |

Varje kolumn i avsnittet **struktur** innehåller följande egenskaper:

| Egenskap | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Kolumnens namn. |Ja |
| typ |Kolumnens datatyp.  |Inga |
| substrat |.NET-baserad kultur som ska användas när typ har angetts och är av typen .NET `Datetime` eller `Datetimeoffset` . Standardvärdet är `en-us`. |Inga |
| format |Format strängen som ska användas när typen har angetts och är av typen .NET `Datetime` eller `Datetimeoffset` . |Inga |

I följande exempel har data uppsättningen tre kolumner `slicetimestamp` , `projectname` och `pageviews` de är av typen: sträng, sträng och decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

I följande tabell beskrivs de egenskaper som du kan använda i avsnittet **tillgänglighet** :

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för data uppsättnings sektorns produktion.<br/><br/><b>Frekvens som stöds</b>: minut, timme, dag, vecka, månad |Ja |NA |
| interval |Anger en multiplikator för frekvens<br/><br/>"Frekvens x-intervall" anger hur ofta sektorn produceras.<br/><br/>Om du vill att data uppsättningen ska segmenteras per timme anger du <b>frekvens</b> till <b>timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs!</b>om du anger frekvensen som minut rekommenderar vi att du anger intervallet till högst 15 |Ja |NA |
| stil |Anger om sektorn ska skapas i början/slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och format är inställt på EndOfInterval, produceras sektorn den sista dagen i månaden. Om formatet är inställt på StartOfInterval produceras sektorn den första dagen i månaden.<br/><br/>Om frekvens är inställt på dag och format är inställt på EndOfInterval skapas sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och formatet är inställt på EndOfInterval, skapas sektorn i slutet av timmen. För till exempel en sektor på en timme – 2-timmarsperiod skapas sektorn på 2 PM. |Inga |EndOfInterval |
| anchorDateTime |Definierar den absoluta position i tid som används av Scheduler för att beräkna data uppsättningens sektor gränser. <br/><br/><b>Obs!</b>om AnchorDateTime har datum delar som är mer detaljerade än frekvensen ignoreras fler detaljerade delar. <br/><br/>Om <b>intervallet</b> till exempel är <b>per timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b> , ignoreras de <b>minuter och sekunderna</b> delar av AnchorDateTime. |Inga |01/01/0001 |
| offset |TimeSpan som startar och slutar på alla mängd uppsättnings segment flyttas. <br/><br/><b>Obs</b>: om både anchorDateTime och offset anges, är resultatet det kombinerade skiftet. |Inga |NA |

Följande tillgänglighets avsnitt anger att data uppsättningen för utdata antingen skapas per timme (eller) indata-datamängd är tillgängligt varje timme:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

I **princip** avsnittet i data uppsättnings definitionen definieras villkoren eller villkoret som data uppsättnings sektorerna måste uppfylla.

| Principnamn | Beskrivning | Tillämpas på | Obligatorisk | Standardvärde |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Kontrollerar att data i en Azure- **BLOB** uppfyller minimi kraven för storlek (i megabyte). |Azure-blobb |Inga |NA |
| minimumRows |Verifierar att data i **Azure SQL Database** eller en **Azure-tabell** innehåller det lägsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Inga |NA |

**Exempel:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Om inte en data uppsättning skapas av Azure Data Factory ska den markeras som **extern**. Den här inställningen gäller normalt indata för den första aktiviteten i en pipeline om inte aktivitet eller pipeline-kedja används.

| Name | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tid för att försena kontrollen av tillgängligheten för externa data för den aktuella sektorn. Om data till exempel är tillgängliga varje timme, är kontrollen för att se de externa data tillgängliga och motsvarande sektor kan fördröjas med hjälp av dataDelay.<br/><br/>Gäller endast för aktuell tid.  Om det till exempel är 1:00 PM nu och det här värdet är 10 minuter, börjar verifieringen med 1:10 PM.<br/><br/>Den här inställningen påverkar inte segment tidigare (segment med sektor slut tid + dataDelay < nu) bearbetas utan fördröjning.<br/><br/>Tiden som är större än 23:59 timmar måste anges med `day.hours:minutes:seconds` formatet. Om du till exempel vill ange 24 timmar ska du inte använda 24:00:00; Använd i stället 1,00:00:00. Om du använder 24:00:00 behandlas det som 24 dagar (24.00:00:00). I 1 dag och 4 timmar anger du 1:04:00:00. |Inga |0 |
| retryInterval |Vänte tiden mellan ett fel och nästa försök att försöka igen. Om ett försök Miss lyckas är nästa försök efter retryInterval. <br/><br/>Om det är 1:00 PM börjar vi med det första försöket. Om varaktigheten för att slutföra den första verifierings kontrollen är 1 minut och åtgärden misslyckades, är nästa nytt försök på 1:00 + 1 min (varaktighet) + 1 min (återförsöksintervall) = 1:02 PM. <br/><br/>För sektorer i det förflutna sker ingen fördröjning. Återförsöket sker omedelbart. |Inga |00:01:00 (1 minut) |
| retryTimeout |Tids gränsen för varje nytt försök.<br/><br/>Om den här egenskapen har angetts till 10 minuter måste verifieringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra verifieringen, tids gränsen för återförsök.<br/><br/>Om alla försök för verifierings tiden är slut, markeras sektorn som stängningsåtgärd. |Inga |00:10:00 (10 minuter) |
| maximumRetry |Antal gånger för att kontrol lera tillgängligheten för externa data. Det högsta tillåtna värdet är 10. |Inga |3 |


## <a name="data-stores"></a>DATA LAGER
I avsnittet för den [länkade tjänsten](#linked-service) finns beskrivningar för JSON-element som är gemensamma för alla typer av länkade tjänster. Det här avsnittet innehåller information om JSON-element som är speciella för varje data lager.

I [data uppsättnings](#dataset) avsnittet finns beskrivningar av JSON-element som är gemensamma för alla typer av data uppsättningar. Det här avsnittet innehåller information om JSON-element som är speciella för varje data lager.

Avsnittet [aktivitet](#activity) tillhandahåller beskrivningar av JSON-element som är gemensamma för alla typer av aktiviteter. Det här avsnittet innehåller information om JSON-element som är speciella för varje data lager när det används som källa/mottagare i en kopierings aktivitet.

Klicka på länken för butiken som du är intresse rad av för att se JSON-scheman för länkad tjänst, data uppsättning och källa/mottagare för kopierings aktiviteten.

| Kategori | Datalager
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure Synapse Analytics](#azure-synapse-analytics) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
| &nbsp; |[Azure Table Storage](#azure-table-storage) |
| **Databaser** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Fil** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Fil system](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Övrigt** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webbtabell |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Azure Storage länkade tjänsten och Azure Storage länkad SAS-tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Skapa en Azure Storage länkad tjänst för att länka ditt Azure Storage-konto till en data fabrik med hjälp av **konto nyckeln**. Ange **typen** av länkad tjänst till **AzureStorage** för att definiera en länkad tjänst för Azure Storage. Sedan kan du ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| Begär |Ange information som krävs för att ansluta till Azure Storage för egenskapen connectionString. |Ja |

##### <a name="example"></a>Exempel

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
Med den länkade tjänsten Azure Storage SAS kan du länka ett Azure Storage konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Den tillhandahåller data fabriken med begränsad/tidsbegränsad åtkomst till alla/angivna resurser (BLOB/container) i lagrings utrymmet. Om du vill länka ditt Azure Storage-konto till en data fabrik med hjälp av signaturen för delad åtkomst skapar du en länkad Azure Storage SAS-tjänst. Om du vill definiera en Azure Storage SAS-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorageSas**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sasUri |Ange signatur-URI för delad åtkomst till Azure Storage resurser som BLOB, container eller Table. |Ja |

##### <a name="example"></a>Exempel

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Mer information om dessa länkade tjänster finns i artikeln [Azure Blob Storage Connector](data-factory-azure-blob-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en Azure Blob-datauppsättning anger du **typen** av data uppsättning till **AzureBlob**. Ange sedan följande Azure Blob-speciella egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till behållaren och mappen i blob-lagringen. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namn på blobben. fileName är valfritt och Skift läges känsligt.<br/><br/>Om du anger ett fil namn fungerar aktiviteten (inklusive kopia) på den aktuella blobben.<br/><br/>Om inget fil namn har angetts innehåller kopian alla blobbar i folderPath för indata-datauppsättning.<br/><br/>Om inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: `Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett fil namn för Time Series-data. FolderPath kan till exempel vara parameterstyrda för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Mer information finns i artikeln [Azure Blob Connector](data-factory-azure-blob-connector.md#dataset-properties) .

### <a name="blobsource-in-copy-activity"></a>BlobSource i kopierings aktivitet
Om du kopierar data från en Azure-Blob Storage anger du **käll typen** för kopierings aktiviteten till **BlobSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant (standardvärde), falskt |Inga |

#### <a name="example-blobsource"></a>Exempel: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink i kopierings aktivitet
Om du kopierar data till en Azure-Blob Storage anger du **typ av mottagare** för kopierings aktiviteten till **BlobSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopierings beteendet när källan är BlobSource eller FileSystem. |<b>PreserveHierarchy</b>: filens hierarki bevaras i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns på den första nivån i målmappen. Målfilen har ett namn som skapats automatiskt. <br/><br/><b>MergeFiles (standard):</b> sammanfogar alla filer från källmappen till en fil. Om filen/BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars skapas fil namnet automatiskt. |Inga |

#### <a name="example-blobsink"></a>Exempel: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln [Azure Blob Connector](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure Data Lake Store länkad tjänst anger du typen av länkad tjänst till **AzureDataLakeStore** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzureDataLakeStore** | Ja |
| dataLakeStoreUri | Ange information om Azure Data Lake Store kontot. Det har följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/` . | Ja |
| subscriptionId | Azure-prenumerations-ID som Data Lake Store tillhör. | Krävs för Sink |
| resourceGroupName | Namnet på Azure-resurs gruppen som Data Lake Store tillhör. | Krävs för Sink |
| servicePrincipalId | Ange programmets klient-ID. | Ja (för tjänstens huvud namns autentisering) |
| servicePrincipalKey | Ange programmets nyckel. | Ja (för tjänstens huvud namns autentisering) |
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja (för tjänstens huvud namns autentisering) |
| auktorisering | Klicka på knappen **auktorisera** i **Data Factory redigeraren** och ange autentiseringsuppgifterna som tilldelar den automatiskt GENERERADE behörighets webb adressen till den här egenskapen. | Ja (för autentisering av användar referenser)|
| sessionId | OAuth-sessions-ID från OAuth-auktoriseringsbegäran. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory redigeraren. | Ja (för autentisering av användar referenser) |

#### <a name="example-using-service-principal-authentication"></a>Exempel: använda autentisering av tjänstens huvud namn
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exempel: autentisering med användar referenser
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Mer information finns i artikeln [Azure Data Lake Store Connector](data-factory-azure-datalake-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en Azure Data Lake Store data uppsättning anger du **typ** av data uppsättning till **AzureDataLakeStore** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| folderPath |Sökväg till behållaren och mappen i Azure Data Lake Store. |Ja |
| fileName |Namnet på filen i Azure Data Lake arkivet. fileName är valfritt och Skift läges känsligt. <br/><br/>Om du anger ett fil namn fungerar aktiviteten (inklusive kopia) på den aktuella filen.<br/><br/>Om inget fil namn har angetts innehåller kopian alla filer i folderPath för indata-datauppsättningen.<br/><br/>Om inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: `Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett fil namn för Time Series-data. FolderPath kan till exempel vara parameterstyrda för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

#### <a name="example"></a>Exempel
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln [Azure Data Lake Store Connector](data-factory-azure-datalake-connector.md#dataset-properties) .

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store källa i kopierings aktivitet
Om du kopierar data från en Azure Data Lake Store anger du **käll typen** för kopierings aktiviteten till **AzureDataLakeStoreSource** och anger följande egenskaper i **käll** avsnittet:

**AzureDataLakeStoreSource** stöder följande egenskaper **typeProperties** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant (standardvärde), falskt |Inga |

#### <a name="example-azuredatalakestoresource"></a>Exempel: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln [Azure Data Lake Store Connector](data-factory-azure-datalake-connector.md#copy-activity-properties) .

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store mottagare i kopierings aktivitet
Om du kopierar data till en Azure Data Lake Store anger du typ av **mottagare** för kopierings aktiviteten till **AzureDataLakeStoreSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| copyBehavior |Anger kopierings beteendet. |<b>PreserveHierarchy</b>: filens hierarki bevaras i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas på den första nivån i målmappen. Målattribut skapas med automatiskt genererat namn.<br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen/BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars skapas fil namnet automatiskt. |Inga |

#### <a name="example-azuredatalakestoresink"></a>Exempel: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln [Azure Data Lake Store Connector](data-factory-azure-datalake-connector.md#copy-activity-properties) .

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure Cosmos DB länkad tjänst anger du **typen** av länkad tjänst till **DocumentDb** och anger följande egenskaper i avsnittet **typeProperties** :

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| Begär |Ange information som krävs för att ansluta till Azure Cosmos DB databasen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Mer information finns i artikeln [Azure Cosmos DB Connector](data-factory-azure-documentdb-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en Azure Cosmos DB data uppsättning anger du **typ** av data uppsättning till **DocumentDbCollection** och anger följande egenskaper i avsnittet **typeProperties** :

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| Samling |Namnet på den Azure Cosmos DB samlingen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Mer information finns i artikeln [Azure Cosmos DB Connector](data-factory-azure-documentdb-connector.md#dataset-properties) .

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB samlings källa i kopierings aktivitet
Om du kopierar data från en Azure Cosmos DB anger du **käll typen** för kopierings aktiviteten till **DocumentDbCollectionSource** och anger följande egenskaper i **käll** avsnittet:


| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om inget anges används SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslat |Valfritt Character. <br/><br/>Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användaren att ange hierarki via nestingSeparator, vilket är "." i exemplen ovan. Med avgränsaren genererar kopierings aktiviteten "name"-objektet med tre underordnade element först, i mitten och sist, enligt namnet. First "," Name. mitten "och" Name. last "i tabell definitionen. |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB samlings mottagare i kopierings aktiviteten
Om du kopierar data till Azure Cosmos DB anger du typ av **mottagare** för kopierings aktiviteten till **DocumentDbCollectionSink** och anger följande egenskaper i avsnittet **mottagare** :

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i käll kolumnens namn som anger att det kapslade dokumentet behövs. <br/><br/>Till exempel ovan: `Name.First` i tabellen utdata skapas följande JSON-struktur i Cosmos DB-dokumentet:<br/><br/>"Namn": {<br/>    "First": "John"<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (dot). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (dot). |
| writeBatchSize |Antal parallella förfrågningar till Azure Cosmos DB tjänst för att skapa dokument.<br/><br/>Du kan finjustera prestandan när du kopierar data till/från Azure Cosmos DB med hjälp av den här egenskapen. Du kan vänta en bättre prestanda när du ökar writeBatchSize eftersom fler parallella förfrågningar till Azure Cosmos DB skickas. Du måste dock undvika begränsning som kan resultera i fel meddelandet: "begär ande frekvens är stor".<br/><br/>Begränsningen bestäms av ett antal faktorer, inklusive dokument storlek, antal villkor i dokument, indexerings policy för mål samling osv. För kopierings åtgärder kan du använda en bättre samling (till exempel S3) för att få flest data flöde tillgängligt (2 500 enheter för programbegäran/sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänte tid för att slutföra åtgärden innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Mer information finns i artikeln [Azure Cosmos DB Connector](data-factory-azure-documentdb-connector.md#copy-activity-properties) .

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure SQL Database länkad tjänst anger du **typen** av länkad tjänst till **AzureSqlDatabase** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Begär |Ange information som krävs för att ansluta till Azure SQL Database-instansen för egenskapen connectionString. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Mer information finns i artikeln om [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en Azure SQL Database data uppsättning anger du **typ** av data uppsättning till **AzureSqlTable** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instansen som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i artikeln om [Azure SQL Connector](data-factory-azure-sql-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>SQL-källa i kopierings aktivitet
Om du kopierar data från en Azure SQL Database anger du **käll typen** för kopierings aktiviteten till **SqlSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i artikeln om [Azure SQL Connector](data-factory-azure-sql-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i kopierings aktivitet
Om du kopierar data till Azure SQL Database anger du typ av **mottagare** för kopierings aktiviteten till **SqlSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Ange ett kolumn namn för kopierings aktiviteten som ska fyllas med automatiskt genererad sektor identifierare, som används för att rensa data i en speciell sektor när den körs igen. |Kolumn namnet för en kolumn med data typen Binary (32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som upsertar (uppdaterar/infogar) data i mål tabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |
| sqlWriterTableType |Ange ett tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktivitet gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga data som kopieras med befintliga data. |Ett namn på en tabell typ. |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [Azure SQL Connector](data-factory-azure-sql-connector.md#copy-activity-properties) .

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="linked-service"></a>Länkad tjänst
Definiera en länkad Azure Synapse Analytics-tjänst genom att ange **typen** av länkad tjänst till **AzureSqlDW** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Begär |Ange information som krävs för att ansluta till Azure Synapse Analytics-instansen för egenskapen connectionString. |Ja |



#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Mer information finns i artikeln om [Azure Synapse Analytics Connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en Azure Synapse Analytics-datauppsättning genom att ange **typen** av data uppsättning till **AzureSqlDWTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure Synapse Analytics-databasen som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [Azure Synapse Analytics Connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) .

### <a name="azure-synapse-analytics-source-in-copy-activity"></a>Azure Synapse Analytics-källa i kopierings aktivitet
Om du kopierar data från Azure Synapse Analytics anger du **käll typen** för kopierings aktiviteten till **SqlDWSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [Azure Synapse Analytics Connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

### <a name="azure-synapse-analytics-sink-in-copy-activity"></a>Azure Synapse Analytics-mottagare i kopierings aktivitet
Om du kopierar data till Azure Synapse Analytics anger du typ av **mottagare** för kopierings aktiviteten till **SqlDWSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. |Ett frågeuttryck. |Inga |
| allowPolyBase |Anger om PolyBase ska användas (när det är tillämpligt) i stället för BULKINSERT-mekanismen. <br/><br/> **Att använda PolyBase är det rekommenderade sättet att läsa in data i Azure Synapse Analytics.** |Sant <br/>False (standard) |Inga |
| polyBaseSettings |En grupp egenskaper som kan anges när **allowPolybase** -egenskapen har angetts till **True**. |&nbsp; |Inga |
| rejectValue |Anger antalet rader eller procent av rader som kan avvisas innan frågan Miss lyckas. <br/><br/>Läs mer om polybases avvisnings alternativ i avsnittet **arguments** i avsnittet [skapa en extern tabell (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (standard), 1, 2,... |Inga |
| rejectType |Anger om alternativet rejectValue anges som ett litteralt värde eller i procent. |Värde (standard), procent |Inga |
| rejectSampleValue |Anger det antal rader som ska hämtas innan PolyBase beräknar om procent andelen avvisade rader. |1, 2,... |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen.<br/><br/>Lär dig mer om den här egenskapen från avsnittet argument i [Skapa externt fil format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |Sant, falskt (standard) |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [Azure Synapse Analytics Connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) .

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Länkad tjänst
Definiera en länkad Azure Kognitiv sökning-tjänst genom att ange **typen** av länkad tjänst till **AzureSearch** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| url | URL för Sök tjänsten. | Ja |
| key | Administratörs nyckel för Sök tjänsten. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Mer information finns i artikeln [Azure kognitiv sökning Connector](data-factory-azure-search-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en data uppsättning för Azure Kognitiv sökning anger du **typ** av data uppsättning till **AzureSearchIndex** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen Type måste anges till **AzureSearchIndex**.| Ja |
| indexName | Sök Indexets namn. Data Factory skapar inte indexet. Indexet måste finnas i Azure Kognitiv sökning. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Mer information finns i artikeln [Azure kognitiv sökning Connector](data-factory-azure-search-connector.md#dataset-properties) .

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Kognitiv sökning index-Sink i kopierings aktivitet
Om du kopierar data till ett sökindex ställer du in **mottagar typen** för kopierings aktiviteten till **AzureSearchIndexSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill sammanfoga eller ersätta när ett dokument redan finns i indexet. | Sammanfoga (standard)<br/>Ladda upp| Inga |
| WriteBatchSize | Överför data till Sök indexet när buffertstorleken når writeBatchSize. | 1 till 1 000. Standardvärdet är 1000. | Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln [Azure kognitiv sökning Connector](data-factory-azure-search-connector.md#copy-activity-properties) .

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Azure Storage länkade tjänsten och Azure Storage länkad SAS-tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Skapa en Azure Storage länkad tjänst för att länka ditt Azure Storage-konto till en data fabrik med hjälp av **konto nyckeln**. Ange **typen** av länkad tjänst till **AzureStorage** för att definiera en länkad tjänst för Azure Storage. Sedan kan du ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **AzureStorage** |Ja |
| Begär |Ange information som krävs för att ansluta till Azure Storage för egenskapen connectionString. |Ja |

**Exempel:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
Med den länkade tjänsten Azure Storage SAS kan du länka ett Azure Storage konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Den tillhandahåller data fabriken med begränsad/tidsbegränsad åtkomst till alla/angivna resurser (BLOB/container) i lagrings utrymmet. Om du vill länka ditt Azure Storage-konto till en data fabrik med hjälp av signaturen för delad åtkomst skapar du en länkad Azure Storage SAS-tjänst. Om du vill definiera en Azure Storage SAS-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorageSas**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange signatur-URI för delad åtkomst till Azure Storage resurser som BLOB, container eller Table. |Ja |

**Exempel:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Mer information om dessa länkade tjänster finns i artikeln [Azure Table Storage Connector](data-factory-azure-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en data uppsättning för Azure-tabellen genom att ange **typen** av data uppsättning till **AzureTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell i Azure Table Database-instansen som den länkade tjänsten refererar till. |Ja. När ett tableName anges utan azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från den tabell som uppfyller frågan till målet. |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information om dessa länkade tjänster finns i artikeln [Azure Table Storage Connector](data-factory-azure-table-connector.md#dataset-properties) .

### <a name="azure-table-source-in-copy-activity"></a>Azure-tabell källa i kopierings aktivitet
Om du kopierar data från Azure Table Storage anger du **käll typen** för kopierings aktiviteten till **AzureTableSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd den anpassade frågan för att läsa data. |Sträng för Azure Table-fråga. Se exemplen i nästa avsnitt. |Nej. När ett tableName anges utan azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från den tabell som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om förtäring av undantag för tabell saknas. |TRUE<br/>FALSE |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information om dessa länkade tjänster finns i artikeln [Azure Table Storage Connector](data-factory-azure-table-connector.md#copy-activity-properties) .

### <a name="azure-table-sink-in-copy-activity"></a>Azure Table-mottagare i kopierings aktivitet
Om du kopierar data till Azure Table Storage anger du typ av **mottagare** för kopierings aktiviteten till **AzureTableSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standardvärdet för partitionerings nyckel som kan användas av mottagaren. |Ett sträng värde. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnyckel. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumn namn. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumn värden används som rad nyckel. Om inget anges ska du använda ett GUID för varje rad. |Ett kolumn namn. |Inga |
| azureTableInsertType |Det läge där data ska infogas i Azure-tabellen.<br/><br/>Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och rad nycklar har ersatts eller slås samman. <br/><br/>Information om hur de här inställningarna (sammanfoga och ersätt) fungerar finns i avsnittet [Infoga eller sammanfoga entiteter](/rest/api/storageservices/Insert-Or-Merge-Entity) och [Infoga eller ersätta entiteter](/rest/api/storageservices/Insert-Or-Replace-Entity) . <br/><br> Den här inställningen gäller på radnivå, inte på tabell nivå, och inget av alternativen tar bort rader i den utgående tabellen som inte finns i indata. |Sammanfoga (standard)<br/>bytt |Inga |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts. |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts |tidsintervall<br/><br/>Exempel: "00:20:00" (20 minuter) |Nej (standard-timeout-värdet för Storage-klienten är 90 SEK) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information om dessa länkade tjänster finns i artikeln [Azure Table Storage Connector](data-factory-azure-table-connector.md#copy-activity-properties) .

## <a name="amazon-redshift"></a>Amazon-RedShift

### <a name="linked-service"></a>Länkad tjänst
Definiera en länkad Amazon RedShift-tjänst genom att ange **typen** av länkad tjänst till **AmazonRedshift** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värd namnet för Amazon RedShift-servern. |Ja |
| port |Numret på den TCP-port som Amazon RedShift-servern använder för att lyssna efter klient anslutningar. |Nej, standardvärde: 5439 |
| databas |Namnet på Amazon RedShift-databasen. |Ja |
| användarnamn |Namnet på den användare som har åtkomst till databasen. |Ja |
| password |Lösen ord för användar kontot. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Mer information finns i artikeln om Amazon RedShift Connector.

### <a name="dataset"></a>Datamängd
Definiera en Amazon RedShift-datauppsättning genom att ange **typen** av data uppsättning till **RelationalTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Amazon RedShift-databasen som den länkade tjänsten refererar till. |Nej (om **fråga** för **RelationalSource** har angetts) |


#### <a name="example"></a>Exempel

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i artikeln om Amazon RedShift Connector.

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från Amazon RedShift anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Nej (om **TableName** för **data uppsättningen** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Mer information finns i artikeln om Amazon RedShift Connector.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Länkad tjänst
Definiera en länkad IBM DB2-tjänst genom att ange **typen** av länkad tjänst till **OnPremisesDB2** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på DB2-servern. |Ja |
| databas |Namnet på DB2-databasen. |Ja |
| schema |Namnet på schemat i databasen. Schema namnet är Skift läges känsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder Basic-eller Windows-autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Mer information finns i artikeln om IBM DB2 Connector.

### <a name="dataset"></a>Datamängd
Om du vill definiera en DB2-datauppsättning anger du **typ** av data uppsättning till **RelationalTable** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i den DB2-databas instans som den länkade tjänsten refererar till. TableName är Skift läges känsligt. |Nej (om **fråga** för **RelationalSource** har angetts)

#### <a name="example"></a>Exempel
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om IBM DB2 Connector.

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från IBM DB2 anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `"query": "select * from "MySchema"."MyTable""`. |Nej (om **TableName** för **data uppsättningen** har angetts) |

#### <a name="example"></a>Exempel
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Mer information finns i artikeln om IBM DB2 Connector.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Länkad tjänst
Definiera en MySQL-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OnPremisesMySql** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på MySQL-servern. |Ja |
| databas |Namnet på MySQL-databasen. |Ja |
| schema |Namnet på schemat i databasen. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till MySQL-databasen. Möjliga värden är: `Basic` . |Ja |
| userName |Ange användar namnet för att ansluta till MySQL-databasen. |Ja |
| password |Ange lösen ordet för det användar konto som du har angett. |Ja |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala MySQL-databasen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Mer information finns i artikeln om [MySQL Connector](data-factory-onprem-mysql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en MySQL-datauppsättning genom att ange **typen** av data uppsättning till **RelationalTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i MySQL-databasen som den länkade tjänsten refererar till. |Nej (om **fråga** för **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i artikeln om [MySQL Connector](data-factory-onprem-mysql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från en MySQL-databas anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Nej (om **TableName** för **data uppsättningen** har angetts) |


#### <a name="example"></a>Exempel
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i artikeln om [MySQL Connector](data-factory-onprem-mysql-connector.md#copy-activity-properties) .

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Länkad tjänst
Definiera en Oracle-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OnPremisesOracle** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| driverType | Ange vilken driv rutin som ska användas för att kopiera data från/till Oracle Database. Tillåtna värden är **Microsoft** eller **ODP** (standard). Mer information om vilka versioner och installationer som stöds finns i avsnittet om driv rutiner. | Inga |
| Begär | Ange information som krävs för att ansluta till Oracle Database-instansen för egenskapen connectionString. | Ja |
| gatewayName | Namnet på den gateway som används för att ansluta till den lokala Oracle-servern |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i artikeln om [Oracle Connector](data-factory-onprem-oracle-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en Oracle-datauppsättning genom att ange **typen** av data uppsättning till **OracleTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle Database som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** för **OracleSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i artikeln om [Oracle Connector](data-factory-onprem-oracle-connector.md#dataset-properties) .

### <a name="oracle-source-in-copy-activity"></a>Oracle-källa i kopierings aktivitet
Om du kopierar data från en Oracle-databas anger du **käll typen** för kopierings aktiviteten till **OracleSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempelvis: `select * from MyTable` <br/><br/>Om inget anges används SQL-instruktionen som körs: `select * from MyTable` |Nej (om **TableName** för **data uppsättningen** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

### <a name="oracle-sink-in-copy-activity"></a>Oracle-Sink i kopierings aktivitet
Om du kopierar data till Oracle Database anger du **mottagar typen** för kopierings aktiviteten till **OracleSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: 00:30:00 (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Ange kolumn namn för kopierings aktivitet som ska fyllas med automatiskt genererad sektor identifierare, som används för att rensa data i en speciell sektor när den körs igen. |Kolumn namnet för en kolumn med data typen Binary (32). |Inga |

#### <a name="example"></a>Exempel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i artikeln om [Oracle Connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) .

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad PostgreSQL-tjänst anger du **typen** av länkad tjänst till **OnPremisesPostgreSql** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på PostgreSQL-servern. |Ja |
| databas |Namnet på PostgreSQL-databasen. |Ja |
| schema |Namnet på schemat i databasen. Schema namnet är Skift läges känsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder Basic-eller Windows-autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Mer information finns i artikeln om [postgresql Connector](data-factory-onprem-postgresql-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en PostgreSQL-datauppsättning anger du **typen** av data uppsättning till **RelationalTable** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell i PostgreSQL-databas instansen som den länkade tjänsten refererar till. TableName är Skift läges känsligt. |Nej (om **fråga** för **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i artikeln om [postgresql Connector](data-factory-onprem-postgresql-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från en PostgreSQL-databas anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: "fråga": "Select * from the \" schema \" . \" Tabellen \" ". |Nej (om **TableName** för **data uppsättningen** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i artikeln om [postgresql Connector](data-factory-onprem-postgresql-connector.md#copy-activity-properties) .

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad SAP Business Warehouse (BW)-tjänst anger du **typen** av länkad tjänst till **SapBw** och anger följande egenskaper i avsnittet **typeProperties** :

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
server | Namnet på den server där SAP BW-instansen finns. | sträng | Ja
systemNumber | System numret för det SAP BW systemet. | Tvåsiffrigt decimal tal representeras som en sträng. | Ja
ClientID | Klient-ID för klienten i SAP W-systemet. | Tresiffrigt decimal tal representeras som en sträng. | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala SAP BW-instansen. | sträng | Ja
encryptedCredential | Krypterad Credential-sträng. | sträng | No

#### <a name="example"></a>Exempel

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i artikeln [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Ange **typen** av data uppsättning till **RelationalTable** för att definiera en SAP BW data uppsättning. Det finns inga typ-/regionsspecifika egenskaper som stöds för SAP BW-datauppsättningen av typen **RelationalTable**.

#### <a name="example"></a>Exempel

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i artikeln [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från SAP Business Warehouse, anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB | Anger MDX-frågan för att läsa data från SAP BW-instansen. | MDX-fråga. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Mer information finns i artikeln [SAP Business Warehouse Connector](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) .

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en SAP HANA länkad tjänst anger du **typen** av länkad tjänst till **SapHana** och anger följande egenskaper i avsnittet **typeProperties** :

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | --------
server | Namnet på den server där SAP HANA-instansen finns. Om servern använder en anpassad port anger du `server:port` . | sträng | Ja
authenticationType | Typ av autentisering. | nollängd. "Basic" eller "Windows" | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala SAP HANA-instansen. | sträng | Ja
encryptedCredential | Krypterad Credential-sträng. | sträng | No

#### <a name="example"></a>Exempel

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Mer information finns i artikeln [SAP HANA Connector](data-factory-sap-hana-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Ange **typen** av data uppsättning till **RelationalTable** för att definiera en SAP HANA data uppsättning. Det finns inga typ-/regionsspecifika egenskaper som stöds för SAP HANA-datauppsättningen av typen **RelationalTable**.

#### <a name="example"></a>Exempel

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i artikeln [SAP HANA Connector](data-factory-sap-hana-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från ett SAP HANA data lager, anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan för att läsa data från SAP HANA-instansen. | SQL-fråga. | Ja |


#### <a name="example"></a>Exempel


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Mer information finns i artikeln [SAP HANA Connector](data-factory-sap-hana-connector.md#copy-activity-properties) .


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** för att länka en SQL Server databas till en data fabrik. Följande tabell innehåller en beskrivning av JSON-element som är speciella för SQL Server länkade tjänsten.

Följande tabell innehåller en beskrivning av JSON-element som är speciella för SQL Server länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till: **OnPremisesSqlServer**. |Ja |
| Begär |Ange connectionString-information som krävs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till SQL Server-databasen. |Ja |
| användarnamn |Ange användar namn om du använder Windows-autentisering. Exempel: **domän \\ namn användar namn**. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |

Du kan kryptera autentiseringsuppgifter med cmdleten **New-AzDataFactoryEncryptValue** och använda dem i anslutnings strängen som visas i följande exempel (**EncryptedCredential** -egenskap):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exempel: JSON för att använda SQL-autentisering

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exempel: JSON för att använda Windows-autentisering

Om användar namn och lösen ord anges använder Gateway dem för att personifiera det angivna användar kontot för att ansluta till den SQL Server databasen. Annars ansluter gatewayen till SQL Server direkt med säkerhets kontexten för gatewayen (dess start konto).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i artikeln [SQL Server Connector](data-factory-sqlserver-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en SQL Server data uppsättning anger du **typ** av data uppsättning till **SqlServerTable** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server databas instansen som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln [SQL Server Connector](data-factory-sqlserver-connector.md#dataset-properties) .

### <a name="sql-source-in-copy-activity"></a>SQL-källa i kopierings aktivitet
Om du kopierar data från en SQL Server databas anger du **käll typen** för kopierings aktiviteten till **SqlSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. Kan referera till flera tabeller från databasen som refereras av data uppsättningen. Om det inte anges används SQL-instruktionen som körs: Välj från tabellen. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource kör kopierings aktiviteten den här frågan mot SQL Server databas källan för att hämta data.

Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName, används kolumnerna som definieras i avsnittet struktur för att skapa en urvals fråga som ska köras mot SQL Server databasen. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName** måste du fortfarande ange ett värde för egenskapen **TableName** i data uppsättnings-JSON. Det finns inga verifieringar som utförts i den här tabellen.


#### <a name="example"></a>Exempel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

I det här exemplet har **sqlReaderQuery** angetts för SqlSource. Kopierings aktiviteten kör den här frågan mot SQL Server databas källan för att hämta data. Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av data uppsättningen. Den är inte begränsad till endast den tabell som angetts som data uppsättningens tableName-typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i avsnittet struktur för att skapa en urvals fråga som ska köras mot SQL Server databasen. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

Mer information finns i artikeln [SQL Server Connector](data-factory-sqlserver-connector.md#copy-activity-properties) .

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i kopierings aktivitet
Om du kopierar data till en SQL Server databas, anger du **mottagar typen** för kopierings aktiviteten till **SqlSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. Mer information finns i avsnittet repeterbarhet. |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Ange kolumn namn för kopierings aktivitet som ska fyllas med automatiskt genererad sektor identifierare, som används för att rensa data i en speciell sektor när den körs igen. Mer information finns i avsnittet repeterbarhet. |Kolumn namnet för en kolumn med data typen Binary (32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som upsertar (uppdaterar/infogar) data i mål tabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |
| sqlWriterTableType |Ange tabell typ namn som ska användas i den lagrade proceduren. Kopierings aktivitet gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga data som kopieras med befintliga data. |Ett namn på en tabell typ. |Inga |

#### <a name="example"></a>Exempel
Pipelinen innehåller en kopierings aktivitet som är konfigurerad för att använda dessa data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln [SQL Server Connector](data-factory-sqlserver-connector.md#copy-activity-properties) .

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Länkad tjänst
Definiera en Sybase-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OnPremisesSybase** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Sybase-serverns namn. |Ja |
| databas |Sybase-databasens namn. |Ja |
| schema |Namnet på schemat i databasen. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till Sybase-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder Basic-eller Windows-autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala Sybase-databasen. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Mer information finns i artikeln om [Sybase Connector](data-factory-onprem-sybase-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en Sybase-datauppsättning genom att ange **typen** av data uppsättning till **RelationalTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Sybase Database-instansen som den länkade tjänsten refererar till. |Nej (om **fråga** för **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [Sybase Connector](data-factory-onprem-sybase-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från en Sybase-databas anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Nej (om **TableName** för **data uppsättningen** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i artikeln om [Sybase Connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) .

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Länkad tjänst
Definiera en Teradata-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OnPremisesTeradata** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Teradata-servern. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till Teradata-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder Basic-eller Windows-autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala Teradata-databasen. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Mer information finns i artikeln om [Teradata Connector](data-factory-onprem-teradata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Ange **typen** av data uppsättning till **RelationalTable** för att definiera en Teradata BLOB-datauppsättning. Det finns för närvarande inga typ egenskaper som stöds för Teradata-datauppsättningen.

#### <a name="example"></a>Exempel
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [Teradata Connector](data-factory-onprem-teradata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från en Teradata-databas anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Mer information finns i artikeln om [Teradata Connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) .

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad Cassandra-tjänst anger du **typen** av länkad tjänst till **OnPremisesCassandra** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| värd |En eller flera IP-adresser eller värd namn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klient anslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Basic eller Anonymous |Ja |
| användarnamn |Ange användar namn för användar kontot. |Ja, om authenticationType har angetts till Basic. |
| password |Ange lösen ordet för användar kontot. |Ja, om authenticationType har angetts till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifterna har krypterats av gatewayen. |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i artikeln om [Cassandra Connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en Cassandra-datauppsättning anger du **typen** av data uppsättning till **CassandraTable** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på det här utrymmet eller schemat i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** inte har definierats). |
| tableName |Namnet på tabellen i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** inte har definierats). |

#### <a name="example"></a>Exempel

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [Cassandra Connector](data-factory-onprem-cassandra-connector.md#dataset-properties) .

### <a name="cassandra-source-in-copy-activity"></a>Cassandra-källa i kopierings aktivitet
Om du kopierar data från Cassandra anger du **käll typen** för kopierings aktiviteten till **CassandraSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92 fråga eller CQL-fråga. Se [referens för CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga anger du namnet på det **. tabell namn** som ska representera den tabell som du vill fråga. |Nej (om tableName och disk utrymme på data mängden har definierats). |
| consistencyLevel |Konsekvens nivån anger hur många repliker som måste svara på en Read-begäran innan data returneras till klient programmet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla Read-begäran. |ETT, TVÅ, TRE, KVORUM, ALLA, LOCAL_QUORUM, EACH_QUORUM LOCAL_ONE. Mer information finns i [Konfigurera data konsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nej. Standardvärdet är ett. |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [Cassandra Connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) .

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad MongoDB-tjänst anger du **typen** av länkad tjänst till **OnPremisesMongoDB** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värd namnet för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klient anslutningar. |Valfritt värde: 27017 |
| authenticationType |Basic eller Anonymous. |Ja |
| användarnamn |Användar konto för åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrol lera autentiseringsuppgifterna för autentisering. |Valfritt (om grundläggande autentisering används). standard: använder administratörs kontot och den databas som anges med egenskapen databaseName. |
| Databas |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som har åtkomst till data lagret. |Ja |
| encryptedCredential |Autentiseringsuppgifterna har krypterats av gateway. |Valfritt |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [artikeln om MongoDB Connector](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datamängd
Om du vill definiera en MongoDB-datauppsättning anger du **typen** av data uppsättning till **MongoDbCollection** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Samling |Namnet på samlingen i MongoDB-databasen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Mer information finns i [artikeln om MongoDB Connector](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB-källa i kopierings aktivitet
Om du kopierar data från MongoDB anger du **käll typen** för kopierings aktiviteten till **MongoDbSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-frågesträng. Exempel: `select * from MyTable`. |Nej (om **samlings** - **dataset** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [artikeln om MongoDB Connector](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Länkad tjänst
För att definiera en Amazon S3-länkad tjänst anger du **typen** av länkad tjänst till **en awsaccesskey** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomst nyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomst nyckeln. |Krypterad hemlig sträng |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Mer information finns i [artikeln om Amazon S3 Connector](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datamängd
Definiera en Amazon S3-data uppsättning genom att ange **typen** av data uppsättning till **AmazonS3** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| bucketName |S3-Bucket-namn. |Sträng |Ja |
| key |S3-objektets nyckel. |Sträng |Inga |
| protokollprefixet |Prefix för S3-objekt nyckeln. Objekt vars nycklar börjar med det här prefixet väljs. Gäller endast när nyckeln är tom. |Sträng |Inga |
| version |Versionen av S3-objektet om S3-versioner har Aktiver ATS. |Sträng |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga | |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga | |


> [!NOTE]
> bucketName + Key anger platsen för S3-objektet där Bucket är rot behållaren för S3-objekt och nyckeln är den fullständiga sökvägen till S3-objektet.

#### <a name="example-sample-dataset-with-prefix"></a>Exempel: exempel data uppsättning med prefix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exempel: exempel data uppsättning (med version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exempel: dynamiska sökvägar för S3
I exemplet använder vi fasta värden för nyckel-och bucketName-egenskaper i Amazon S3-datauppsättningen.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Du kan låta Data Factory beräkna nyckeln och bucketName dynamiskt vid körning med hjälp av systemvariabler som SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma sak för egenskapen prefix för en Amazon S3-data uppsättning. En lista över funktioner och variabler som stöds finns i [Data Factory funktioner och systemvariabler](data-factory-functions-variables.md) .

Mer information finns i [artikeln om Amazon S3 Connector](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fil system källa i kopierings aktivitet
Om du kopierar data från Amazon S3 anger du **käll typen** för kopierings aktiviteten till **FileSystemSource** och anger följande egenskaper i **käll** avsnittet:


| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om S3-objekt ska listas rekursivt under katalogen. |True/false |Inga |


#### <a name="example"></a>Exempel


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Mer information finns i [artikeln om Amazon S3 Connector](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Filsystem


### <a name="linked-service"></a>Länkad tjänst
Du kan länka ett lokalt fil system till en Azure-datafabrik med den **lokala fil serverns** länkade tjänst. Följande tabell innehåller beskrivningar av JSON-element som är speciella för den lokala fil Server länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Se till att egenskapen type har angetts till **OnPremisesFileServer**. |Ja |
| värd |Anger rot Sök vägen till den mapp som du vill kopiera. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner. |Ja |
| userid |Ange ID: t för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| password |Ange lösen ordet för användaren (UserID). |Nej (om du väljer encryptedCredential |
| encryptedCredential |Ange de krypterade autentiseringsuppgifter som du kan få genom att köra cmdleten New-AzDataFactoryEncryptValue. |Nej (om du väljer att ange användar-ID och lösen ord som oformaterad text) |
| gatewayName |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala fil servern. |Ja |

#### <a name="sample-folder-path-definitions"></a>Exempel på mappsökväg definitioner

| Scenario | Värd i länkad tjänst definition | folderPath i data uppsättnings definition |
| --- | --- | --- |
| Lokal mapp på Data Management Gateway dator: <br/><br/>Exempel: D: \\ \* eller D:\folder\subfolder\\* |D: \\ \\ (för data Management Gateway 2,0 och senare versioner) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2,0) |.\\\\ eller mapp \\ \\ -undermapp (för data Management Gateway 2,0 och senare versioner) <br/><br/>D: \\ \\ eller d: \\ \\ mapp \\ \\ -undermapp (för gateway-version under 2,0) |
| Fjärran sluten delad mapp: <br/><br/>Exempel: \\ \\ mappen Server \\ resurs \\ \* eller mapp för min \\ \\ Server- \\ delad \\ mapp \\\\* |\\\\\\\\Server \\ \\ resurs |.\\\\ eller mapp \\ -undermapp \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: använda användar namn och lösen ord i oformaterad text

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exempel: använda encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [artikeln om fil Systems koppling](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datamängd
För att definiera en fil system data uppsättning, ange **typ** av data uppsättning till **fileshare** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger under Sök vägen till mappen. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer. <br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: "fileFilter": "*. log"<br/>Exempel 2: "fileFilter": 2016-1-?. format<br/><br/>Observera att fileFilter är tillämpligt för en data uppsättning för en indata-FileShare. |Inga |
| partitionedBy |Du kan använda partitionedBy för att ange ett dynamiskt folderPath/fileName för Time Series-data. Ett exempel är folderPath-parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**; och de nivåer som stöds är: **optimalt** och **snabbast**. Se [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Du kan inte använda fileName och fileFilter samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [artikeln om fil Systems koppling](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Fil system källa i kopierings aktivitet
Om du kopierar data från fil systemet anger du **käll typen** för kopierings aktiviteten till **FileSystemSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i [artikeln om fil Systems koppling](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Fil Systems mottagare i kopierings aktivitet
Om du kopierar data till fil system ställer du in **typ av mottagare** i **FileSystemSink** och anger följande egenskaper i avsnittet **mottagare** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopierings beteendet när källan är BlobSource eller FileSystem. |**PreserveHierarchy:** Bevarar filens hierarki i målmappen. Det vill säga att käll filens relativa sökväg är samma som den relativa sökvägen till mål filen i målmappen.<br/><br/>**FlattenHierarchy:** Alla filer från källmappen skapas på den första nivån i målmappen. Målattribut skapas med ett automatiskt genererat namn.<br/><br/>**MergeFiles:** Sammanfogar alla filer från källmappen till en fil. Om fil namnet/blobb namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. |Inga |

disk

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [artikeln om fil Systems koppling](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad FTP-tjänst anger du **typen** av länkad tjänst till **ftpserver** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| värd |Namn eller IP-adress för FTP-servern |Ja |&nbsp; |
| authenticationType |Ange autentiseringstyp |Ja |Grundläggande, anonym |
| användarnamn |Användare som har åtkomst till FTP-servern |Inga |&nbsp; |
| password |Användarens lösen ord (användar namn) |Inga |&nbsp; |
| encryptedCredential |Krypterade autentiseringsuppgifter för åtkomst till FTP-servern |Inga |&nbsp; |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal FTP-Server |Inga |&nbsp; |
| port |Porten som FTP-servern lyssnar på |Inga |21 |
| enableSsl |Ange om FTP över SSL/TLS-kanal ska användas |Inga |true |
| enableServerCertificateValidation |Ange om du vill aktivera verifiering av Server-TLS/SSL-certifikat när du använder FTP över SSL/TLS-kanal |Inga |true |

#### <a name="example-using-anonymous-authentication"></a>Exempel: använda anonym autentisering

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exempel: använda användar namn och lösen ord som oformaterad text för grundläggande autentisering

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exempel: använda port, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exempel: använda encryptedCredential för autentisering och gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Mer information finns i artikeln om [FTP-anslutning](data-factory-ftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en FTP-datauppsättning genom att ange **typen** av data uppsättning till **fileshare** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Under Sök väg till mappen. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter är tillämpligt för en data uppsättning för en indata-FileShare. Den här egenskapen stöds inte med HDFS. |Inga |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath, fil namn för Time Series-data. Till exempel folderPath parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**; och de nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| useBinaryTransfer |Ange om du vill använda binärt överförings läge. Sant för binärt läge och falskt ASCII. Standardvärde: sant. Den här egenskapen kan endast användas när den associerade länkade tjänst typen är av typen: FtpServer. |Inga |

> [!NOTE]
> Det går inte att använda filename och fileFilter samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i artikeln om [FTP-anslutning](data-factory-ftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Fil system källa i kopierings aktivitet
Om du kopierar data från en FTP-server anger du **käll typen** för kopierings aktiviteten till **FileSystemSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Mer information finns i artikeln om [FTP-anslutning](data-factory-ftp-connector.md#copy-activity-properties) .


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Länkad tjänst
Definiera en HDFS-länkad tjänst genom att ange **typen** för den länkade tjänsten till **HDFS** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **HDFS** |Ja |
| URL |URL till HDFS |Ja |
| authenticationType |Anonym eller Windows. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS Connector läser du det här avsnittet för att konfigurera din lokala miljö. |Ja |
| userName |Användar namn för Windows-autentisering. |Ja (för Windows-autentisering) |
| password |Lösen ord för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till HDFS. |Ja |
| encryptedCredential |[New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) utdata från autentiseringsuppgifterna för åtkomst. |Inga |

#### <a name="example-using-anonymous-authentication"></a>Exempel: använda anonym autentisering

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exempel: använda Windows-autentisering

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i artikeln HDFS Connector.

### <a name="dataset"></a>Datamängd
Definiera en HDFS-datauppsättning genom att ange **typen** av data uppsättning till **fileshare** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet "\" för specialtecken i strängen. Exempel: för folder\subfolder, anger du \\ \\ undermappen mapp och för d:\samplefolder, anger du d: \\ \\ samplefolder.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: <br/><br/>`Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath, fil namn för Time Series-data. Exempel: folderPath-parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Det går inte att använda filename och fileFilter samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i artikeln HDFS Connector.

### <a name="file-system-source-in-copy-activity"></a>Fil system källa i kopierings aktivitet
Om du kopierar data från HDFS anger du **käll typen** för kopierings aktiviteten till **FileSystemSource** och anger följande egenskaper i **käll** avsnittet:

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i artikeln HDFS Connector.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad SFTP-tjänst anger du **typen** av länkad tjänst till **SFTP** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| värd | Namn eller IP-adress för SFTP-servern. |Ja |
| port |Porten som SFTP-servern lyssnar på. Standardvärdet är: 21 |Inga |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Basic**, **SshPublicKey**. <br><br> Se använda grundläggande autentisering och [autentisering med offentliga SSH-nycklar](#using-ssh-public-key-authentication) på fler egenskaper respektive JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över validering av värd nycklar. | Nej. Standardvärdet: false |
| hostKeyFingerprint | Ange finger utskriften för värd nyckeln. | Ja om `skipHostKeyValidation` är inställt på falskt.  |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (username + Password) eller SshPublicKey-autentisering (användar namn + privat nyckel Sök väg eller innehåll) i guiden Kopiera eller dialog rutan för ClickOnce. | Nej. Gäller endast när du kopierar data från en lokal SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Exempel: använda grundläggande autentisering

Om du vill använda grundläggande autentisering anger du `authenticationType` som `Basic` och anger sedan följande egenskaper, förutom SFTP-anslutningen som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Användarens lösen ord (användar namn). | Ja |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: **grundläggande autentisering med krypterade autentiseringsuppgifter**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Använda autentisering med offentlig SSH-nyckel:**

Om du vill använda grundläggande autentisering anger du `authenticationType` som `SshPublicKey` och anger sedan följande egenskaper, förutom SFTP-anslutningen som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-servern |Ja |
| privateKeyPath | Ange en absolut sökväg till den privata nyckel filen som gatewayen har åtkomst till. | Ange antingen `privateKeyPath` eller `privateKeyContent` . <br><br> Gäller endast när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserad sträng för innehållet i den privata nyckeln. Kopierings guiden kan läsa den privata nyckel filen och extrahera innehållet i den privata nyckeln automatiskt. Om du använder andra verktyg/SDK använder du egenskapen privateKeyPath i stället. | Ange antingen `privateKeyPath` eller `privateKeyContent` . |
| Fraser | Ange pass frasen/lösen ordet för att dekryptera den privata nyckeln om nyckel filen skyddas av en pass fras. | Ja om den privata nyckel filen skyddas av en pass fras. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: **SshPublicKey-autentisering med privat nyckel innehåll**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Mer information finns i artikeln om [SFTP-koppling](data-factory-sftp-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en SFTP-datauppsättning genom att ange **typen** av data uppsättning till **fileshare** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Under Sök väg till mappen. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter är tillämpligt för en data uppsättning för en indata-FileShare. Den här egenskapen stöds inte med HDFS. |Inga |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath, fil namn för Time Series-data. Till exempel folderPath parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| useBinaryTransfer |Ange om du vill använda binärt överförings läge. Sant för binärt läge och falskt ASCII. Standardvärde: sant. Den här egenskapen kan endast användas när den associerade länkade tjänst typen är av typen: FtpServer. |Inga |

> [!NOTE]
> Det går inte att använda filename och fileFilter samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i artikeln om [SFTP-koppling](data-factory-sftp-connector.md#dataset-properties) .

### <a name="file-system-source-in-copy-activity"></a>Fil system källa i kopierings aktivitet
Om du kopierar data från en SFTP-källa anger du **käll typen** för kopierings aktiviteten till **FileSystemSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |



#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Mer information finns i artikeln om [SFTP-koppling](data-factory-sftp-connector.md#copy-activity-properties) .


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad HTTP-tjänst anger du **typen** av länkad tjänst till **http** och anger följande egenskaper i **typeProperties** -avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| url | Bas-URL till webb servern | Ja |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är: **Anonym**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> I avsnitt under den här tabellen hittar du fler egenskaper och JSON-exempel för de typerna av autentisering. | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera verifiering av Server-TLS/SSL-certifikat om källan är HTTPS-webbserver | Nej, standardvärdet är sant |
| gatewayName | Namnet på Data Management Gateway att ansluta till en lokal HTTP-källa. | Ja om du kopierar data från en lokal HTTP-källa. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till HTTP-slutpunkten. Genereras automatiskt när du konfigurerar autentiseringsinformationen i guiden Kopiera eller dialog rutan för ClickOnce. | Nej. Gäller endast när du kopierar data från en lokal HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exempel: använda Basic-, Digest-eller Windows-autentisering
Ange `authenticationType` som `Basic` , `Digest` eller `Windows` , och ange följande egenskaper, förutom den allmänna HTTP-anslutningen som introducerades ovan:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användar namn för att få åtkomst till HTTP-slutpunkten. | Ja |
| password | Användarens lösen ord (användar namn). | Ja |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exempel: använda ClientCertificate-autentisering

Om du vill använda grundläggande autentisering anger du `authenticationType` som `ClientCertificate` och anger följande egenskaper förutom de allmänna HTTP-anslutningarna som introducerades ovan:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Det Base64-kodade innehållet i binära data från PFX-filen (personal information Exchange). | Ange antingen `embeddedCertData` eller `certThumbprint` . |
| certThumbprint | Tumavtrycket för det certifikat som har installerats på din gateway-dators certifikat arkiv. Gäller endast när du kopierar data från en lokal HTTP-källa. | Ange antingen `embeddedCertData` eller `certThumbprint` . |
| password | Lösen ordet som är associerat med certifikatet. | Inga |

Om du använder `certThumbprint` för autentisering och certifikatet är installerat i det personliga arkivet på den lokala datorn måste du ge Läs behörighet till Gateway-tjänsten:

1. Starta Microsoft Management Console (MMC). Lägg till snapin-modulen **certifikat** som är riktad mot den **lokala datorn**.
2. Expandera **certifikat**, **personligt** och klicka på **certifikat**.
3. Högerklicka på certifikatet i det personliga arkivet och välj **alla aktiviteter** -> **hantera privata nycklar...**
3. På fliken **säkerhet** lägger du till det användar konto som data Management Gateway värd tjänsten körs under med Läs behörighet till certifikatet.

**Exempel: använda klient certifikat:** Den här länkade tjänsten länkar din data fabrik till en lokal HTTP-webbserver. Det använder ett klient certifikat som är installerat på datorn med Data Management Gateway installerat.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exempel: använda klient certifikat i en fil
Den här länkade tjänsten länkar din data fabrik till en lokal HTTP-webbserver. Den använder en klient certifikat fil på datorn med Data Management Gateway installerad.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Mer information finns i artikeln om [http-anslutning](data-factory-http-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en HTTP-datauppsättning anger du **typen** av data uppsättning till **http** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| relativeUrl | En relativ URL till den resurs som innehåller data. Om ingen sökväg anges används endast den URL som angavs i den länkade tjänst definitionen. <br><br> Om du vill skapa en dynamisk URL kan du använda [Data Factory funktioner och systemvariabler](data-factory-functions-variables.md), till exempel: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` . | Inga |
| requestMethod | Http-metod. Tillåtna värden är **Get** eller **post**. | Nej. Standardvärdet är `GET`. |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Inga |
| requestBody | Brödtext för HTTP-begäran. | Inga |
| format | Om du bara vill **Hämta data från http-slutpunkten som de är** utan att parsa den, kan du hoppa över format inställningarna. <br><br> Om du vill parsa innehållet i HTTP-svar under kopieringen, stöds följande format **typer: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

#### <a name="example-using-the-get-default-method"></a>Exempel: använda metoden GET (standard)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exempel: använda POST-metoden

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Mer information finns i artikeln om [http-anslutning](data-factory-http-connector.md#dataset-properties) .

### <a name="http-source-in-copy-activity"></a>HTTP-källa i kopierings aktivitet
Om du kopierar data från en HTTP-källa anger du **käll typen** för kopierings aktiviteten till **HttpSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Tids gränsen (TimeSpan) för HTTP-begäran för att få ett svar. Det är tids gränsen för att få ett svar, inte tids gränsen för att läsa svars data. | Nej. Standardvärde: 00:01:40 |


#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [http-anslutning](data-factory-http-connector.md#copy-activity-properties) .

## <a name="odata"></a>OData

### <a name="linked-service"></a>Länkad tjänst
Definiera en OData-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OData** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| url |URL för OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För Cloud OData är möjliga värden Anonym, Basic och OAuth (Obs Azure Data Factory stöder för närvarande endast Azure Active Directory baserade OAuth). <br/><br/> För lokala OData-enheter är möjliga värden Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användar namn om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Ja (endast om du använder grundläggande autentisering) |
| authorizedCredential |Om du använder OAuth klickar du på knappen **auktorisera** i guiden Data Factory kopiering eller redigeraren och anger dina autentiseringsuppgifter. sedan genereras värdet för den här egenskapen automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till den lokala OData-tjänsten. Ange endast om du kopierar data från den lokala OData-källan. |Inga |

#### <a name="example---using-basic-authentication"></a>Exempel – med grundläggande autentisering
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exempel – använda anonym autentisering

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exempel – använda Windows-autentisering åtkomst till en lokal OData-källa

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exempel – använda OAuth-autentisering åtkomst till molnbaserad OData-källa
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Mer information finns i artikeln om [OData Connector](data-factory-odata-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Om du vill definiera en OData-datauppsättning anger du **typ** av data uppsättning till **ODataResource** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| path |Sökväg till OData-resursen |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Mer information finns i artikeln om [OData Connector](data-factory-odata-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från en OData-källa anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Exempel | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |"? $select = namn, beskrivning&$top = 5" |Inga |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Mer information finns i artikeln om [OData Connector](data-factory-odata-connector.md#copy-activity-properties) .


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Länkad tjänst
Definiera en ODBC-länkad tjänst genom att ange **typen** för den länkade tjänsten till **OnPremisesOdbc** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Begär |Delen av autentiseringsuppgifter som inte är till gång till i anslutnings strängen och en valfri krypterad autentiseringsuppgift. Se exemplen i följande avsnitt. |Ja |
| credential |Delen autentiseringsuppgifter för den anslutnings sträng som anges i drivrutinsspecifika egenskaps värde format. Exempel: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalagret. Möjliga värden är: anonyma och grundläggande. |Ja |
| användarnamn |Ange användar namn om du använder grundläggande autentisering. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till ODBC-datalagret. |Ja |

#### <a name="example---using-basic-authentication"></a>Exempel – med grundläggande autentisering

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exempel – använda grundläggande autentisering med krypterade autentiseringsuppgifter
Du kan kryptera autentiseringsuppgifterna med cmdleten [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) .

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exempel: använda anonym autentisering

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i artikeln om [ODBC-koppling](data-factory-odbc-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en ODBC-datauppsättning genom att ange **typen** av data uppsättning till **RelationalTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datalagret. |Ja |


#### <a name="example"></a>Exempel

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [ODBC-koppling](data-factory-odbc-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från ett ODBC-datalager anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i artikeln om [ODBC-koppling](data-factory-odbc-connector.md#copy-activity-properties) .

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Länkad tjänst
Definiera en Salesforce-länkad tjänst genom att ange **typen** för den länkade tjänsten till **Salesforce** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| environmentUrl | Ange URL: en för Salesforce-instansen. <br><br> -Standard är "https: \/ /login.Salesforce.com". <br> – Om du vill kopiera data från sandbox anger du " https://test.salesforce.com ". <br> – Om du vill kopiera data från en anpassad domän anger du exempelvis "https://[domän]. My. Salesforce. com". |Inga |
| användarnamn |Ange ett användar namn för användar kontot. |Ja |
| password |Ange ett lösen ord för användar kontot. |Ja |
| securityToken |Ange en säkerhetstoken för användar kontot. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer/hämtar en säkerhetstoken. Mer information om säkerhetstoken i allmänhet finns i [säkerhet och API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Mer information finns i artikeln om [Salesforce-koppling](data-factory-salesforce-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en Salesforce-datauppsättning genom att ange **typen** av data uppsättning till **RelationalTable** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga** för **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i artikeln om [Salesforce-koppling](data-factory-salesforce-connector.md#dataset-properties) .

### <a name="relational-source-in-copy-activity"></a>Relations källa i kopierings aktivitet
Om du kopierar data från Salesforce anger du **käll typen** för kopierings aktiviteten till **RelationalSource** och anger följande egenskaper i **käll** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |En SQL-92-fråga eller en [SOQL-fråga (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) . Till exempel: `select * from MyTable__c`. |Nej (om **data uppsättningens** **TableName** anges) |

#### <a name="example"></a>Exempel



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> En "__c"-del av API-namnet krävs för alla anpassade objekt.

Mer information finns i artikeln om [Salesforce-koppling](data-factory-salesforce-connector.md#copy-activity-properties) .

## <a name="web-data"></a>Webb data

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad webb tjänst anger du **typen** av länkad tjänst till **webben** och anger följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| URL |URL till webb adressen |Ja |
| authenticationType |Anonym. |Ja |


#### <a name="example"></a>Exempel


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Mer information finns i artikeln om [webb tabell koppling](data-factory-web-table-connector.md#linked-service-properties) .

### <a name="dataset"></a>Datamängd
Definiera en webb data uppsättning genom att ange **typen** av data uppsättning till webb **tabell** och ange följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |typ av data uppsättning. måste vara inställd på **Webtable** |Ja |
| path |En relativ URL till den resurs som innehåller tabellen. |Nej. Om ingen sökväg anges används endast den URL som angavs i den länkade tjänst definitionen. |
| index |Indexet för tabellen i resursen. Se avsnittet Hämta index för en tabell i en HTML-sida för steg för att hämta index för en tabell på en HTML-sida. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i artikeln om [webb tabell koppling](data-factory-web-table-connector.md#dataset-properties) .

### <a name="web-source-in-copy-activity"></a>Webb källa i kopierings aktivitet
Om du kopierar data från en webb tabell anger du **käll typen** för kopierings aktiviteten till webb **källan**. För närvarande stöds inte ytterligare egenskaper när källan i kopierings aktiviteten är av typen **Websource**.

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i artikeln om [webb tabell koppling](data-factory-web-table-connector.md#copy-activity-properties) .

## <a name="compute-environments"></a>BERÄKNINGS MILJÖER
I följande tabell visas de beräknings miljöer som stöds av Data Factory och de omvandlings aktiviteter som kan köras på dem. Klicka på länken för den beräkning som du är intresse rad av för att se JSON-scheman för den länkade tjänsten för att länka den till en data fabrik.

| Compute-miljö | Aktiviteter |
| --- | --- |
| [HDInsight-kluster på begäran](#on-demand-azure-hdinsight-cluster) eller [ditt eget HDInsight-kluster](#existing-azure-hdinsight-cluster) |[.Net anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [gris-aktivitet](#hdinsight-pig-activity), MapReduce- [aktivitet](#hdinsight-mapreduce-activity), Hadoop streaming-aktivitet, Spark- [aktivitet](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET-anpassad aktivitet](#net-custom-activity) |
| [Azure Machine Learning Studio (klassisk)](#azure-machine-learning-studio-classic) | [Azure Machine Learning Studio (klassisk) batch execution Activity](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (klassisk) uppdatera resurs aktivitet](#azure-machine-learning-studio-classic-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure Synapse Analytics](#azure-synapse-analytics) [SQL Server](#sql-server-stored-procedure) |[Lagrad procedur](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Azure HDInsight-kluster på begäran
Azure Data Factorys tjänsten kan automatiskt skapa ett Windows/Linux-baserat HDInsight-kluster på begäran för att bearbeta data. Klustret skapas i samma region som lagrings kontot (linkedServiceName-egenskapen i JSON) som är associerad med klustret. Du kan köra följande omvandlings aktiviteter på den här länkade tjänsten [: .net anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [gris](#hdinsight-pig-activity)-aktivitet, MapReduce- [aktivitet](#hdinsight-mapreduce-activity), Hadoop-direktuppspelning, Spark- [aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av egenskaperna som används i Azure JSON-definitionen för en länkad HDInsight-tjänst på begäran.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till **HDInsightOnDemand**. |Ja |
| clusterSize |Antalet arbets uppgifter/datanoder i klustret. HDInsight-klustret skapas med två head-noder och antalet arbetsnoder som du anger för den här egenskapen. Noderna har storlek Standard_D3 som har 4 kärnor, så ett kluster med en arbets nod tar 24 kärnor (4 \* 4 = 16 kärnor för arbetsnoder, plus 2 \* 4 = 8 kärnor för Head-noder). Se [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) för information om Standard_D3 nivån. |Ja |
| TimeToLive |Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir aktivt efter att en aktivitets körning slutförts om det inte finns några andra aktiva jobb i klustret.<br/><br/>Om en aktivitets körning till exempel tar 6 minuter och TimeToLive har angetts till 5 minuter förblir klustret aktiv i 5 minuter efter 6 minuters bearbetning av aktivitets körningen. Om en annan aktivitet körs med fönstret 6 minuter bearbetas den av samma kluster.<br/><br/>Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (kan ta en stund), så Använd den här inställningen vid behov för att förbättra prestandan hos en data fabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger TimeToLive-värdet till 0, tas klustret bort så snart aktiviteten körs i bearbetat. Å andra sidan kan klustret vara inaktivt om du anger ett högt värde, vilket leder till höga kostnader. Därför är det viktigt att du anger rätt värde utifrån dina behov.<br/><br/>Flera pipelines kan dela samma instans av HDInsight-klustret på begäran om egenskap svärdet TimeToLive har angetts korrekt |Ja |
| version |Version av HDInsight-klustret. Mer information finns [i stödda HDInsight-versioner i Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Inga |
| linkedServiceName |Azure Storage länkad tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. <p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder en Azure Data Lake Store som lagrings plats. Om du vill lagra resultat data från HDInsight-bearbetning i en Azure Data Lake Store använder du en kopierings aktivitet för att kopiera data från Azure-Blob Storage till Azure Data Lake Store.</p>  | Ja |
| additionalLinkedServiceNames |Anger ytterligare lagrings konton för den länkade HDInsight-tjänsten så att tjänsten Data Factory kan registrera dem åt dig. |Inga |
| osType |Typ av operativ system. Tillåtna värden är: Windows (standard) och Linux |Inga |
| hcatalogLinkedServiceName |Namnet på den länkade Azure SQL-tjänsten som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas genom att använda Azure SQL Database som metaarkiv. |Inga |

### <a name="json-example"></a>JSON-exempel
Följande JSON definierar en Linux-baserad länkad HDInsight-tjänst på begäran. Data Factory tjänsten skapar automatiskt ett **Linux-baserat HDInsight-** kluster när en data sektor bearbetas.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Mer information finns i artikeln om att [Beräkna länkade tjänster](data-factory-compute-linked-services.md) .

## <a name="existing-azure-hdinsight-cluster"></a>Befintligt Azure HDInsight-kluster
Du kan skapa en länkad Azure HDInsight-tjänst för att registrera ditt eget HDInsight-kluster med Data Factory. Du kan köra följande data omvandlings aktiviteter på den här länkade tjänsten [: .net anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [gris](#hdinsight-pig-activity)-aktivitet, MapReduce- [aktivitet](#hdinsight-mapreduce-activity), Hadoop-direktuppspelning, Spark- [aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av egenskaperna som används i Azure JSON-definitionen för en länkad Azure HDInsight-tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till **HDInsight**. |Ja |
| clusterUri |URI för HDInsight-klustret. |Ja |
| användarnamn |Ange namnet på den användare som ska användas för att ansluta till ett befintligt HDInsight-kluster. |Ja |
| password |Ange lösen ordet för användar kontot. |Ja |
| linkedServiceName | Namnet på den länkade tjänsten Azure Storage som refererar till Azure Blob Storage som används av HDInsight-klustret. <p>För närvarande kan du inte ange en Azure Data Lake Store länkad tjänst för den här egenskapen. Du kan komma åt data i Azure Data Lake Store från Hive/gris-skript om HDInsight-klustret har åtkomst till Data Lake Store. </p>  |Ja |

För versioner av HDInsight-kluster som stöds, se [HDInsight-versioner som stöds](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Du kan skapa en Azure Batch länkad tjänst för att registrera en batch-pool med virtuella datorer (VM) med en data fabrik. Du kan köra anpassade .NET-aktiviteter med antingen Azure Batch eller Azure HDInsight. Du kan köra en [Anpassad .net-aktivitet](#net-custom-activity) på den här länkade tjänsten.

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av egenskaperna som används i Azure JSON-definitionen för en Azure Batch länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till **AzureBatch**. |Ja |
| accountName |Namnet på Azure Batch kontot. |Ja |
| accessKey |Åtkomst nyckel för Azure Batch kontot. |Ja |
| poolName |Namn på poolen med virtuella datorer. |Ja |
| linkedServiceName |Namnet på den länkade tjänsten Azure Storage som är kopplad till den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för uppsamlingsfiler som krävs för att köra aktiviteten och för att lagra aktivitets körnings loggarna. |Ja |


#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klassisk)
Du skapar en Azure Machine Learning Studio (klassisk) länkad tjänst för att registrera en Studio (klassisk) slut punkt för batch-poäng med en data fabrik. Två data omvandlings aktiviteter som kan köras på den här länkade tjänsten: [Azure Machine Learning Studio (klassisk) batch execution Activity](#azure-machine-learning-studio-classic-batch-execution-activity), [Azure Machine Learning Studio (klassisk) uppdatera resurs aktivitet](#azure-machine-learning-studio-classic-update-resource-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av egenskaperna som används i Azure JSON-definitionen för en länkad Studio-tjänst (klassisk).

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Egenskapen Type ska anges till: **azureml**. |Ja |
| mlEndpoint |URL för batch-poäng. |Ja |
| apiKey |Den publicerade arbets ytans modells API. |Ja |

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka en Azure Data Lake Analytics Compute-tjänst till en Azure-datafabrik innan du använder den [data Lake Analytics U-SQL-aktiviteten](data-factory-usql-activity.md) i en pipeline.

### <a name="linked-service"></a>Länkad tjänst

Följande tabell innehåller beskrivningar av egenskaperna som används i JSON-definitionen för en Azure Data Lake Analytics länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Egenskapen Type ska anges till: **AzureDataLakeAnalytics**. |Ja |
| accountName |Azure Data Lake Analytics konto namn. |Ja |
| dataLakeAnalyticsUri |Azure Data Lake Analytics-URI. |Inga |
| auktorisering |Auktoriseringskod hämtas automatiskt när du klickar på knappen **auktorisera** i Data Factory redigeraren och slutför OAuth-inloggningen. |Ja |
| subscriptionId |ID för Azure-prenumeration |Nej (om inget anges används prenumerationen på data fabriken). |
| resourceGroupName |Azure-resursgruppsnamn |Nej (om inget värde anges används resurs gruppen för data fabriken). |
| sessionId |sessions-ID från OAuth-auktoriseringsbegäran. Varje sessions-ID är unikt och kan bara användas en gång. När du använder Data Factory redigeraren genereras detta ID automatiskt. |Ja |


#### <a name="json-example"></a>JSON-exempel
I följande exempel finns en JSON-definition för en Azure Data Lake Analytics länkad tjänst.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="sql-server-stored-procedure"></a>SQL Server lagrad procedur

Du skapar en SQL Server länkad tjänst och använder den med den [lagrade procedur aktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory pipeline.

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** för att länka en SQL Server databas till en data fabrik. Följande tabell innehåller en beskrivning av JSON-element som är speciella för SQL Server länkade tjänsten.

Följande tabell innehåller en beskrivning av JSON-element som är speciella för SQL Server länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till: **OnPremisesSqlServer**. |Ja |
| Begär |Ange connectionString-information som krävs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till SQL Server-databasen. |Ja |
| användarnamn |Ange användar namn om du använder Windows-autentisering. Exempel: **domän \\ namn användar namn**. |Inga |
| password |Ange lösen ordet för det användar konto som du har angett för användar namnet. |Inga |

Du kan kryptera autentiseringsuppgifter med cmdleten **New-AzDataFactoryEncryptValue** och använda dem i anslutnings strängen som visas i följande exempel (**EncryptedCredential** -egenskap):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exempel: JSON för att använda SQL-autentisering

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exempel: JSON för att använda Windows-autentisering

Om användar namn och lösen ord anges använder Gateway dem för att personifiera det angivna användar kontot för att ansluta till den SQL Server databasen. Annars ansluter gatewayen till SQL Server direkt med säkerhets kontexten för gatewayen (dess start konto).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i artikeln [SQL Server Connector](data-factory-sqlserver-connector.md#linked-service-properties) .

## <a name="data-transformation-activities"></a>DATA OMVANDLINGS AKTIVITETER

Aktivitet | Beskrivning
-------- | -----------
[HDInsight Hive-aktivitet](#hdinsight-hive-activity) | HDInsight Hive-aktiviteten i en Data Factory pipelinen kör Hive-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran.
[HDInsight gris-aktivitet](#hdinsight-pig-activity) | HDInsight gris-aktiviteten i en Data Factory pipeline kör gris-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran.
[HDInsight MapReduce-aktivitet](#hdinsight-mapreduce-activity) | HDInsight MapReduce-aktiviteten i en Data Factory pipeline kör MapReduce-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran.
[HDInsight-strömningsaktivitet](#hdinsight-streaming-activity) | HDInsight streaming-aktiviteten i en Data Factory pipeline kör Hadoop streaming-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran.
[HDInsight Apache Spark-aktivitet](#hdinsight-spark-activity) | HDInsight Spark-aktiviteten i en Data Factory pipeline kör Spark-program i ditt eget HDInsight-kluster.
[Batch-körningsaktivitet i Azure Machine Learning Studio (klassisk)](#azure-machine-learning-studio-classic-batch-execution-activity) | Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Studio (klassisk) webb tjänst för förutsägelse analys. Med hjälp av aktiviteten kör batch-körning i en Azure Data Factory pipeline kan du anropa en Studio (klassisk) webb tjänst för att göra förutsägelser för data i batch.
[Uppdateringsresursaktivitet i Azure Machine Learning Studio (klassisk)](#azure-machine-learning-studio-classic-update-resource-activity) | Med tiden måste förutsägande modeller i de Azure Machine Learning Studio (klassiska) bedömnings experimenten återskapas med hjälp av nya data uppsättningar för indata. När du är färdig med omträningen vill du uppdatera bedömnings-webbtjänsten med den omarbetade Machine Learning-modellen. Du kan använda aktiviteten uppdatera resurs för att uppdatera webb tjänsten med den nyligen utbildade modellen.
[Lagrad proceduraktivitet](#stored-procedure-activity) | Du kan använda den lagrade procedur aktiviteten i en Data Factory pipeline för att anropa en lagrad procedur i något av följande data lager: Azure SQL Database, Azure Synapse Analytics, SQL Server Database i ditt företag eller en virtuell Azure-dator.
[Data Lake Analytics U-SQL-aktivitet](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-aktivitet kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster.
[.NET-anpassad aktivitet](#net-custom-activity) | Om du behöver transformera data på ett sätt som inte stöds av Data Factory, kan du skapa en anpassad aktivitet med din egen data bearbetnings logik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten att köras med hjälp av antingen en Azure Batch tjänst eller ett Azure HDInsight-kluster.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
Du kan ange följande egenskaper i en JSON-definition för Hive-aktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightHive**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till HDInsightHive:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| skript |Ange Hive-skriptet infogat |Inga |
| skript Sök väg |Lagra Hive-skriptet i en Azure Blob-lagring och ange sökvägen till filen. Använd script-eller scriptPath-egenskapen. Båda kan inte användas tillsammans. Fil namnet är Skift läges känsligt. |Inga |
| definierar |Ange parametrar som nyckel/värde-par för referenser i Hive-skriptet med hjälp av "hiveconf" |Inga |

De här typ egenskaperna är speciella för Hive-aktiviteten. Andra egenskaper (utanför avsnittet typeProperties) stöds för alla aktiviteter.

### <a name="json-example"></a>JSON-exempel
Följande JSON definierar en HDInsight Hive-aktivitet i en pipeline.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Mer information finns i artikeln om [Hive-aktivitet](data-factory-hive-activity.md) .

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
Du kan ange följande egenskaper i en JSON-definition av gris-aktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightPig**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till HDInsightPig:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| skript |Ange det infogade gris-skriptet |Inga |
| skript Sök väg |Lagra ditt gris-skript i en Azure Blob-lagring och ange sökvägen till filen. Använd script-eller scriptPath-egenskapen. Båda kan inte användas tillsammans. Fil namnet är Skift läges känsligt. |Inga |
| definierar |Ange parametrar som nyckel/värde-par för referenser i gris-skriptet |Inga |

De här typ egenskaperna är speciella för aktiviteten gris. Andra egenskaper (utanför avsnittet typeProperties) stöds för alla aktiviteter.

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Mer information finns i artikeln om aktivitet i gris.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
Du kan ange följande egenskaper i en JSON-definition för MapReduce-aktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightMapReduce**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till HDInsightMapReduce:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| jarLinkedService | Namnet på den länkade tjänsten för Azure Storage som innehåller JAR-filen. | Ja |
| jarFilePath | Sökväg till JAR-filen i Azure Storage. | Ja |
| className | Namnet på huvud klassen i JAR-filen. | Ja |
| ogiltiga | En lista med kommaavgränsade argument för MapReduce-programmet. Vid körning ser du några extra argument (till exempel: MapReduce. job. Tags) från MapReduce-ramverket. Om du vill särskilja argumenten med MapReduce-argumenten kan du använda båda alternativen och värdet som argument, som du ser i följande exempel (-s,--output,--output osv., är alternativ omedelbart följt av deras värden) | Inga |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Mer information finns i artikeln om [MapReduce-aktiviteter](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
Du kan ange följande egenskaper i en JSON-definition för Hadoop streaming-aktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightStreaming**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till HDInsightStreaming:

| Egenskap | Beskrivning |
| --- | --- |
| mappning | Namnet på den körbara filen för mapper. I exemplet är cat.exe filen mapper.|
| minskning | Namn på den programbegränsande körbara filen. I exemplet är wc.exe den körbara filen för minskning. |
| indata | Indatafil (inklusive plats) för mapper. I exemplet: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"` : adfsample är BLOB-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är blobben. |
| utdata | Utdatafil (inklusive plats) för minskningen. Utdata från Hadoop streaming-jobbet skrivs till den plats som angetts för den här egenskapen. |
| filePaths | Sökvägar för mapparna mapper och reduce. I exemplet: "adfsample/example/Apps/wc.exe", är adfsample BLOB-behållaren, exempel/Apps är mappen och wc.exe är den körbara filen. |
| fileLinkedService | Azure Storage länkad tjänst som representerar Azure Storage som innehåller filerna som anges i avsnittet fil Sök vägar. |
| ogiltiga | En lista med kommaavgränsade argument för MapReduce-programmet. Vid körning ser du några extra argument (till exempel: MapReduce. job. Tags) från MapReduce-ramverket. Om du vill särskilja argumenten med MapReduce-argumenten kan du använda båda alternativen och värdet som argument, som du ser i följande exempel (-s,--output,--output osv., är alternativ omedelbart följt av deras värden) |
| getDebugInfo | Ett valfritt element. När det är inställt på att Miss lyckas, hämtas loggarna bara vid ett haveri. När det är inställt på alla hämtas alltid loggar oavsett körnings status. |

> [!NOTE]
> Du måste ange en data uppsättning för en data uppsättning för Hadoop streaming-aktiviteten för egenskapen **utdata** . Den här data uppsättningen kan vara en dummy-datauppsättning som krävs för att driva pipeline-schemat (varje timme, varje dag osv.). Om aktiviteten inte tar indata kan du hoppa över att ange en indata-datauppsättning för aktiviteten för egenskapen **Inputs** .

## <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Mer information finns i artikeln om [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
Du kan ange följande egenskaper i en JSON-definition för Spark-aktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightSpark**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till HDInsightSpark:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| rootPath | Azure Blob-behållaren och-mappen som innehåller Spark-filen. Fil namnet är Skift läges känsligt. | Ja |
| entryFilePath | Relativ sökväg till rotmappen för Spark-koden/-paketet. | Ja |
| className | Programmets Java/Spark-huvud klass | Inga |
| ogiltiga | En lista med kommando rads argument för Spark-programmet. | Inga |
| proxyUser | Användar kontot som ska personifieras för att köra Spark-programmet | Inga |
| sparkConfig | Konfigurations egenskaper för Spark. | Inga |
| getDebugInfo | Anger när Spark-loggfilerna ska kopieras till Azure-lagringen som används av HDInsight-kluster (eller) som anges av sparkJobLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga |
| sparkJobLinkedService | Den länkade tjänsten Azure Storage som innehåller Spark-jobbets fil, beroenden och loggar.  Om du inte anger något värde för den här egenskapen används det lagrings utrymme som är associerat med HDInsight-kluster. | Inga |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Observera följande punkter:

- Egenskapen **Type** har angetts till **HDInsightSpark**.
- **RootPath** är inställt på **adfspark \\ PyFiles** där adfspark är Azure Blob-behållaren och pyFiles är en bra mapp i den behållaren. I det här exemplet är Azure-Blob Storage den som är associerad med Spark-klustret. Du kan överföra filen till en annan Azure Storage. Om du gör det skapar du en Azure Storage länkad tjänst för att länka lagrings kontot till data fabriken. Ange sedan namnet på den länkade tjänsten som ett värde för egenskapen **sparkJobLinkedService** . Se Spark-aktivitets egenskaper för information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten.
- **EntryFilePath** har angetts till **test.py**, som är python-filen.
- **GetDebugInfo** -egenskapen är inställd på **Always**, vilket innebär att loggfilerna alltid genereras (lyckade eller misslyckade).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte anger den här egenskapen till alltid i en produktions miljö om du inte felsöker ett problem.
- Avsnittet **utdata** har en data uppsättning för utdata. Du måste ange en data uppsättning för utdata även om Spark-programmet inte producerar några utdata. Den utgående data uppsättningen driver schemat för pipelinen (varje timme, varje dag osv.).

Mer information om aktiviteten finns i artikeln om [Spark-aktivitet](data-factory-spark.md) .

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Batch-körningsaktivitet i Azure Machine Learning Studio (klassisk)
Du kan ange följande egenskaper i en Azure Machine Learning Studio (klassisk) batch execution Activity JSON-definition. Egenskapen Type för aktiviteten måste vara: **AzureMLBatchExecution**. Du måste först skapa en länkad Studio-tjänst (klassisk) och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till AzureMLBatchExecution:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
WebServiceInputActivity | Data uppsättningen som ska skickas som indata för webb tjänsten Studio (klassisk). Den här data uppsättningen måste också ingå i indata för aktiviteten. |Använd antingen WebServiceInputActivity eller webServiceInputs. |
webServiceInputs | Ange data uppsättningar som ska skickas som indata för webb tjänsten Studio (klassisk). Om webb tjänsten tar flera indata använder du egenskapen webServiceInputs i stället för att använda egenskapen WebServiceInputActivity. Data uppsättningar som **webServiceInputs** refererar till måste också tas med i aktivitetens **indata**. | Använd antingen WebServiceInputActivity eller webServiceInputs. |
webServiceOutputs | De data uppsättningar som har tilldelats som utdata för webb tjänsten Studio (klassisk). Webb tjänsten returnerar utdata från den här data uppsättningen. | Ja |
Dublettparameternamnet | Ange värden för webb tjänst parametrarna i det här avsnittet. | Inga |

### <a name="json-example"></a>JSON-exempel
I det här exemplet har aktiviteten data uppsättningen **MLSqlInput** som indata och **MLSqlOutput** som utdata. **MLSqlInput** skickas som en inmatare till webb tjänsten med hjälp av JSON-egenskapen **WebServiceInputActivity** . **MLSqlOutput** skickas som utdata till webb tjänsten med hjälp av JSON-egenskapen **webServiceOutputs** .

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

I JSON-exemplet använder den distribuerade Studio-webbtjänsten en läsare och en Writer-modul för att läsa/skriva data från/till en Azure SQL Database. Den här webb tjänsten exponerar följande fyra parametrar: databas server namn, databas namn, konto namn för Server användare och lösen ord för användar konto för Server.

> [!NOTE]
> Endast indata och utdata för AzureMLBatchExecution-aktiviteten kan skickas som parametrar till webb tjänsten. I ovanstående JSON-kodfragment är MLSqlInput till exempel inpasset i AzureMLBatchExecution-aktiviteten, som skickas som en indatamängd till webb tjänsten via WebServiceInputActivity-parametern.

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Uppdateringsresursaktivitet i Azure Machine Learning Studio (klassisk)
Du kan ange följande egenskaper i en Azure Machine Learning Studio (klassisk) uppdatera JSON-definitionen för resurs aktiviteter. Egenskapen Type för aktiviteten måste vara: **AzureMLUpdateResource**. Du måste först skapa en länkad Studio-tjänst (klassisk) och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till AzureMLUpdateResource:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
trainedModelName | Namnet på den omtränade modellen. | Ja |
trainedModelDatasetName | Data uppsättningen pekar på den iLearner-fil som returnerades av omträningen. | Ja |

### <a name="json-example"></a>JSON-exempel
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Aktiviteten för batch-körning i Studio (klassisk) tar tränings data som indata och skapar en iLearner-fil som utdata. Aktiviteten anropar webb tjänsten utbildning (inlärnings experiment som visas som en webb tjänst) med indata och tar emot ilearner-filen från webb tjänsten. PlaceholderBlob är bara en data uppsättning för en dummy-datauppsättning som krävs av Azure Data Factorys tjänsten för att köra pipelinen.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Du kan ange följande egenskaper i en JSON-definition för U-SQL-aktivitet. Egenskapen Type för aktiviteten måste vara: **DataLakeAnalyticsU-SQL**. Du måste skapa en Azure Data Lake Analytics länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till DATALAKEANALYTICSU-SQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| scriptPath |Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är Skift läges känsligt. |Nej (om du använder skript) |
| scriptLinkedService |Länkad tjänst som länkar det lagrings utrymme som innehåller skriptet till data fabriken |Nej (om du använder skript) |
| skript |Ange infogat skript i stället för att ange scriptPath och scriptLinkedService. Exempel: "skript": "skapa databas test". |Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism |Det maximala antalet noder som används samtidigt för att köra jobbet. |Inga |
| prioritet |Bestämmer vilka jobb som inte är i kö som ska väljas för att köras först. Ju lägre siffra, desto högre prioritet. |Inga |
| parametrar |Parametrar för U-SQL-skriptet |Inga |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Mer information finns i [data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan ange följande egenskaper i en JSON-definition för en lagrad procedur aktivitet. Egenskapen Type för aktiviteten måste vara: **SqlServerStoredProcedure**. Du måste skapa en av följande länkade tjänster och ange namnet på den länkade tjänsten som ett värde för egenskapen **linkedServiceName** :

- SQL Server
- Azure SQL Database
- Azure Synapse Analytics

Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till SqlServerStoredProcedure:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL Database eller Azure Synapse Analytics som representeras av den länkade tjänsten som används i utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrar för lagrad procedur. Om du behöver skicka null för en parameter använder du syntaxen: "param1": null (alla gemener). I följande exempel hittar du information om hur du använder den här egenskapen. |Inga |

Om du anger en indata-datauppsättning måste den vara tillgänglig (i klar status) för att den lagrade procedur aktiviteten ska kunna köras. Det går inte att konsumera indata-dataset i den lagrade proceduren som en parameter. Den används endast för att kontrol lera beroendet innan den lagrade procedur aktiviteten startas. Du måste ange en data uppsättning för utdata för en lagrad procedur aktivitet.

Data uppsättningen för utdata anger **schemat** för aktiviteten för lagrad procedur (varje timme, varje vecka, varje månad osv.). Data uppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller en Azure Synapse-analys eller en SQL Server databas där du vill att den lagrade proceduren ska köras. Data uppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)) i pipelinen. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här data uppsättningen. Det är den lagrade proceduren som skriver till en SQL-tabell som den resulterande data uppsättningen pekar på. I vissa fall kan data uppsättningen för utdata vara en **dummy-datauppsättning**, som endast används för att ange schemat för körning av den lagrade procedur aktiviteten.

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Mer information finns i artikeln om [lagrade procedur aktiviteter](data-factory-stored-proc-activity.md) .

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Du kan ange följande egenskaper i en JSON-definition för anpassade aktiviteter i .NET. Egenskapen Type för aktiviteten måste vara: **DotNetActivity**. Du måste skapa en länkad Azure HDInsight-tjänst eller en Azure Batch länkad tjänst och ange namnet på den länkade tjänsten som ett värde för egenskapen **linkedServiceName** . Följande egenskaper stöds i avsnittet **typeProperties** när du ställer in typen av aktivitet till DotNetActivity:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| AssemblyName | Sammansättningens namn. I exemplet är det: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Namnet på den klass som implementerar IDotNetActivity-gränssnittet. I exemplet är det: **MyDotNetActivityNS. MyDotNetActivity** där MyDotNetActivityNS är namn området och MyDotNetActivity är klassen.  | Ja |
| PackageLinkedService | Namnet på den länkade tjänsten Azure Storage som pekar på blob-lagringen som innehåller zip-filen för den anpassade aktiviteten. I exemplet är det: **AzureStorageLinkedService**.| Ja |
| PackageFile | Namnet på zip-filen. I exemplet är det: **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| extendedProperties | Utökade egenskaper som du kan definiera och skicka till .NET-koden. I det här exemplet anges variabeln **SliceStart** till ett värde baserat på system variabeln SliceStart. | Inga |

### <a name="json-example"></a>JSON-exempel

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Detaljerad information finns [i använda anpassade aktiviteter i Data Factory](data-factory-use-custom-activities.md) artikel.

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser:

- [Självstudie: skapa en pipeline med en kopierings aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Självstudie: skapa en pipeline med en Hive-aktivitet](data-factory-build-your-first-pipeline.md)