---
title: Skapa data uppsättningar i Azure Data Factory
description: Lär dig hur du skapar data uppsättningar i Azure Data Factory, med exempel som använder egenskaper som offset och anchorDateTime.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4b222b387dad1c078cfe2a063ed310ef463b192e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376844"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Data uppsättningar i Azure Data Factory (version 1)
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-create-datasets.md)
> * [Version 2 (aktuell version)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [data uppsättningar i v2](../concepts-datasets-linked-services.md).

I den här artikeln beskrivs vilka data uppsättningar som är, hur de definieras i JSON-format och hur de används i Azure Data Factory pipelines. Den innehåller information om varje avsnitt (till exempel struktur, tillgänglighet och princip) i JSON-definitionen för data uppsättningen. Artikeln innehåller också exempel för att använda egenskaperna **offset**, **anchorDateTime** och **Style** i en data uppsättnings JSON-definition.

> [!NOTE]
> Om du är nybörjare på Data Factory, se [Introduktion till Azure Data Factory](data-factory-introduction.md) för en översikt. Om du inte har praktisk erfarenhet av att skapa data fabriker kan du få en bättre förståelse genom att läsa självstudien om [data omvandling](data-factory-build-your-first-pipeline.md) och [själv studie kursen för data förflyttning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en aktivitet. Aktiviteterna i en pipeline definierar åtgärder som ska utföras på dina data. Du kan till exempel använda en kopierings aktivitet för att kopiera data från en SQL Server-databas till Azure Blob Storage. Sedan kan du använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta data från Blob Storage för att skapa utdata. Slutligen kan du använda en andra kopierings aktivitet för att kopiera utmatnings data till Azure Synapse Analytics, som bygger på de rapporterande lösningarna för Business Intelligence (BI). Mer information om pipelines och aktiviteter finns [i pipeline och aktiviteter i Azure Data Factory](data-factory-create-pipelines.md).

En aktivitet kan ta noll eller fler data **uppsättningar** och skapa en eller flera data uppsättningar. En indata-datauppsättning representerar indata för en aktivitet i pipelinen och en data uppsättning för utdata representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel BLOB-behållaren och mappen i Blob Storage som pipelinen ska läsa data från.

Innan du skapar en data uppsättning skapar du en **länkad tjänst** för att länka ditt data lager till data fabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Data uppsättningar identifierar data i länkade data lager, till exempel SQL-tabeller, filer, mappar och dokument. Till exempel länkar en Azure Storage länkad tjänst ett lagrings konto till data fabriken. En Azure Blob-datauppsättning representerar BLOB-behållaren och mappen som innehåller de blobar för indata som ska bearbetas.

Här är ett exempel scenario. Om du vill kopiera data från Blob Storage till SQL Database skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två data uppsättningar: Azure Blob-datauppsättningen (som refererar till den Azure Storage länkade tjänsten) och data uppsättningen för Azure SQL-tabellen (som refererar till den Azure SQL Database länkade tjänsten). De länkade tjänsterna Azure Storage och Azure SQL Database innehåller anslutnings strängar som Data Factory använder vid körning för att ansluta till dina Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger BLOB-behållaren och blob-mappen som innehåller blobar för indata i blob-lagringen. Data uppsättningen för Azure SQL-tabellen anger den SQL-tabell i SQL-databasen som data ska kopieras till.

Följande diagram visar relationerna mellan pipeline, aktivitet, data uppsättning och länkad tjänst i Data Factory:

![Relation mellan pipeline, aktivitet, data uppsättning, länkade tjänster](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Data mängds-JSON
En data uppsättning i Data Factory definieras i JSON-format enligt följande:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
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
| name |Data uppsättningens namn. Se [Azure Data Factory namngivnings regler](data-factory-naming-rules.md) för namngivnings regler. |Ja |NA |
| typ |Typ av data uppsättning. Ange en av de typer som stöds av Data Factory (till exempel: AzureBlob, AzureSqlTable). <br/><br/>Mer information finns i [data uppsättnings typ](#Type). |Ja |NA |
| hierarkistruktur |Schema för data uppsättningen.<br/><br/>Mer information finns i [data uppsättnings struktur](#Structure). |Inga |NA |
| typeProperties | Typ egenskaperna är olika för varje typ (till exempel: Azure Blob, Azure SQL-tabell). Mer information om de typer som stöds och deras egenskaper finns i [data uppsättnings typ](#Type). |Ja |NA |
| extern | Boolesk flagga för att ange om en data uppsättning uttryckligen skapas av en Data Factory-pipeline eller inte. Om indata-datauppsättningen för en aktivitet inte produceras av den aktuella pipelinen anger du den här flaggan till true. Ange den här flaggan till true för indata-datauppsättningen för den första aktiviteten i pipelinen.  |Inga |falskt |
| availability | Definierar bearbetnings fönstret (till exempel varje timme eller varje dag) eller segmenterings modellen för data uppsättnings produktionen. Varje enhet med data som konsumeras och skapas av en aktivitets körning kallas för en data sektor. Om tillgänglighet för en data uppsättning för utdata anges till daglig (frekvens-dag, intervall-1) skapas en sektor varje dag. <br/><br/>Mer information finns i tillgänglighet för data uppsättningar. <br/><br/>Mer information om den data uppsättnings segment modellen finns i artikeln om [schemaläggning och körning](data-factory-scheduling-and-execution.md) . |Ja |NA |
| policy |Definierar villkoret eller villkoret som data uppsättnings sektorerna måste uppfylla. <br/><br/>Mer information finns i avsnittet [princip för data uppsättning](#Policy) . |Inga |NA |

## <a name="dataset-example"></a>Exempel på data uppsättning
I följande exempel representerar data uppsättningen en tabell med namnet min **tabell** i en SQL-databas.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Observera följande punkter:

* **typ** är inställd på AzureSqlTable.
* Egenskapen **TableName** Type (som är speciell för AzureSqlTable-typ) har angetts till tabellen Table.
* **linkedServiceName** refererar till en länkad tjänst av typen AzureSqlDatabase, som definieras i nästa JSON-kodfragment.
* **tillgänglighets frekvensen** har angetts till Day och **Interval** är inställt på 1. Det innebär att data uppsättnings sektorn produceras dagligen.

**AzureSqlLinkedService** definieras enligt följande:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

I föregående JSON-kodfragment:

* **typ** är inställd på AzureSqlDatabase.
* Egenskapen **ConnectionString** Type anger information för att ansluta till en SQL-databas.

Som du kan se definierar den länkade tjänsten hur du ansluter till en SQL-databas. Data uppsättningen definierar vilken tabell som används som indata och utdata för aktiviteten i en pipeline.

> [!IMPORTANT]
> Om inte en data uppsättning skapas av pipelinen ska den markeras som **extern**. Den här inställningen gäller normalt indata för första aktiviteten i en pipeline.

## <a name="dataset-type"></a><a name="Type"></a> Data uppsättnings typ
Typ av data uppsättning beror på vilket data lager du använder. I följande tabell finns en lista över data lager som stöds av Data Factory. Klicka på ett data lager om du vill veta hur du skapar en länkad tjänst och en data uppsättning för det data lagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Data lager med * kan vara lokala eller i Azure Infrastructure as a Service (IaaS). Dessa data lager kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md).

I exemplet i föregående avsnitt är typ av data uppsättning inställd på **AzureSqlTable**. På samma sätt anges typ av data uppsättning till **AzureBlob** för en Azure Blob-datamängd, som du ser i följande JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Data uppsättnings struktur
**Struktur** avsnittet är valfritt. Den definierar data uppsättningens schema genom att innehålla en samling med namn och data typer för kolumner. Du kan använda avsnittet struktur för att tillhandahålla typ information som används för att konvertera typer och mappa kolumner från källan till målet. I följande exempel har data uppsättningen tre kolumner: `slicetimestamp` , `projectname` och `pageviews` . De är av typen sträng, sträng och decimal respektive decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Varje kolumn i strukturen innehåller följande egenskaper:

| Egenskap | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Kolumnens namn. |Ja |
| typ |Kolumnens datatyp.  |Inga |
| substrat |. NET-baserad kultur som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset` . Standardvärdet är `en-us`. |Inga |
| format |Format sträng som ska användas när typen är en .NET-typ: `Datetime` eller `Datetimeoffset` . |Inga |

Följande rikt linjer hjälper dig att avgöra när du ska inkludera struktur information och vad som ska ingå i **struktur** avsnittet.

* **För strukturerade data källor** anger du avsnittet struktur endast om du vill att kart käll kolumner ska innehålla kolumner, och deras namn inte är desamma. Den här typen av strukturerad data källa lagrar data schema och skriver information tillsammans med själva data. Exempel på strukturerade data källor är SQL Server, Oracle och Azure Table.
  
    Eftersom typ information redan är tillgänglig för strukturerade data källor bör du inte ta med typ information när du inkluderar avsnittet struktur.
* **För schema vid läsning av data källor (särskilt Blob Storage)** kan du välja att lagra data utan att lagra scheman eller ange information med data. För dessa typer av data källor, inkludera struktur när du vill mappa käll kolumner till mottagar kolumner. Ta även med struktur när data uppsättningen är indata för en kopierings aktivitet och data typerna för käll data uppsättningen ska konverteras till interna typer för mottagaren.
    
    Data Factory stöder följande värden för att tillhandahålla typ information i strukturen: **Int16, Int32, Int64, Single, Double, decimal, byte [], Boolean, String, GUID, DateTime, DateTimeOffset och TimeSpan**. Dessa värden är Common Language Specification (CLS)-kompatibla. NET-baserade typ värden.

Data Factory utför automatiskt typ konverteringar när data flyttas från ett käll data lager till ett data lager för mottagare.

## <a name="dataset-availability"></a>Tillgänglighet för data uppsättning
**Tillgänglighets** avsnittet i en data uppsättning definierar bearbetnings fönstret (till exempel varje timme, varje dag eller varje vecka) för data uppsättningen. Mer information om aktivitets fönster finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md).

Följande tillgänglighets avsnitt anger att den utgående data uppsättningen antingen skapas varje timme eller om data uppsättningen för indata är tillgänglig varje timme:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Om pipelinen har följande start-och slut tider:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Data uppsättningen för utdata skapas per timme inom pipelinens start-och slut tider. Därför finns det fem data uppsättnings segment som produceras av denna pipeline, en för varje aktivitets fönster (12: a, 1 AM-2 AM, 2 fm-3 AM, 3 am, 4 FM-5 AM).

I följande tabell beskrivs de egenskaper som du kan använda i avsnittet tillgänglighet:

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för data uppsättnings sektorns produktion.<br/><br/><b>Frekvens som stöds</b>: minut, timme, dag, vecka, månad |Ja |NA |
| interval |Anger en multiplikator för frekvens.<br/><br/>"Frekvens x-intervall" anger hur ofta sektorn produceras. Om du till exempel vill att data uppsättningen ska segmenteras per timme anger du <b>frekvens</b> till <b>timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/>Observera att om du anger **frekvensen** som **minut** bör du ange ett intervall som inte är mindre än 15. |Ja |NA |
| stil |Anger om sektorn ska skapas i början eller slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Om **frekvensen** är inställd på **månad** och **formatet** är inställt på **EndOfInterval**, produceras sektorn den sista dagen i månaden. Om **format** är inställt på **StartOfInterval** skapas sektorn den första dagen i månaden.<br/><br/>Om **frekvensen** är inställd på **dag** och **formatet** är inställt på **EndOfInterval**, produceras sektorn under den senaste timmen på dagen.<br/><br/>Om **frekvensen** är inställd på **timme** och **formatet** är inställt på **EndOfInterval**, skapas sektorn i slutet av timmen. För en sektor för en PM-2-timmarsperiod skapas till exempel sektorn på 2 PM. |Inga |EndOfInterval |
| anchorDateTime |Definierar den absoluta position i tid som används av Scheduler för att beräkna data uppsättningens sektor gränser. <br/><br/>Observera att om den här egenskapen har datum delar som är mer detaljerade än den angivna frekvensen, ignoreras fler detaljerade delar. Om **intervallet** exempelvis är **per timme** (frekvens: timme och intervall: 1) och **anchorDateTime** innehåller **minuter och sekunder**, ignoreras sedan minuter och sekunder delar av **anchorDateTime** . |Inga |01/01/0001 |
| offset |TimeSpan som startar och slutar på alla mängd uppsättnings segment flyttas. <br/><br/>Observera att om både **anchorDateTime** och **offset** anges, är resultatet det kombinerade skiftet. |Inga |NA |

### <a name="offset-example"></a>förskjutnings exempel
Som standard börjar sektorerna () med 12 () med UTC- `"frequency": "Day", "interval": 1` tid (Coordinated Universal Time). Om du vill att start tiden ska vara 6 UTC-tid i stället anger du förskjutningen enligt följande kodfragment:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime-exempel
I följande exempel skapas data uppsättningen en gång var 23: e timme. Den första sektorn börjar vid den tidpunkt som anges av **anchorDateTime**, som är inställd på `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>exempel på offset/format
Följande data uppsättning är månatlig och produceras den tredje i varje månad med 8:00 AM ( `3.08:00:00` ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Princip för data uppsättning
**Princip** avsnittet i data uppsättnings definitionen definierar villkoret eller villkoret som data uppsättnings sektorerna måste uppfylla.

### <a name="validation-policies"></a>Validerings principer
| Principnamn | Beskrivning | Tillämpas på | Obligatorisk | Standardvärde |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Verifierar att data i **Azure Blob Storage** uppfyller minimi kraven för storlek (i megabyte). |Azure Blob Storage |Inga |NA |
| minimumRows |Verifierar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det lägsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Inga |NA |

#### <a name="examples"></a>Exempel
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Externa data uppsättningar
Externa data uppsättningar är de som inte produceras av en pågående pipeline i data fabriken. Om data uppsättningen har marker ATS som **extern** kan **extern Aldata** -principen definieras för att påverka beteendet för data uppsättnings sektorns tillgänglighet.

Om inte en data uppsättning skapas av Data Factory ska den markeras som **extern**. Den här inställningen gäller normalt indata för första aktiviteten i en pipeline, om inte aktivitet eller pipeline-länkning används.

| Name | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tiden för att försena kontrollen av tillgängligheten för externa data för den aktuella sektorn. Du kan till exempel försena en varje timme-kontroll genom att använda den här inställningen.<br/><br/>Inställningen gäller bara för aktuell tid. Om det till exempel är 1:00 PM nu och det här värdet är 10 minuter, börjar verifieringen med 1:10 PM.<br/><br/>Observera att den här inställningen inte påverkar segment tidigare. Segment med **slut tid i sektor**  +  **dataDelay**  <  **nu** bearbetas utan fördröjning.<br/><br/>Tiden som är större än 23:59 timmar ska anges med `day.hours:minutes:seconds` formatet. Om du till exempel vill ange 24 timmar ska du inte använda 24:00:00. Använd i stället 1,00:00:00. Om du använder 24:00:00 behandlas det som 24 dagar (24.00:00:00). I 1 dag och 4 timmar anger du 1:04:00:00. |Inga |0 |
| retryInterval |Vänte tiden mellan ett haveri och nästa försök. Den här inställningen gäller för aktuell tid. Om föregående försök misslyckades är nästa försök efter **retryInterval** -perioden. <br/><br/>Om det är 1:00 PM börjar vi med det första försöket. Om varaktigheten för att slutföra den första verifierings kontrollen är 1 minut och åtgärden misslyckades, är nästa nytt försök på 1:00 + 1MIN (varaktighet) + 1MIN (återförsöksintervall) = 1:02 PM. <br/><br/>För sektorer i det förflutna sker ingen fördröjning. Återförsöket sker omedelbart. |Inga |00:01:00 (1 minut) |
| retryTimeout |Tids gränsen för varje nytt försök.<br/><br/>Om den här egenskapen har angetts till 10 minuter ska verifieringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra verifieringen, tids gränsen för återförsök.<br/><br/>Om alla försök för validerings tiden är slut, markeras sektorn som **stängningsåtgärd**. |Inga |00:10:00 (10 minuter) |
| maximumRetry |Antalet gånger för att kontrol lera tillgängligheten för externa data. Det högsta tillåtna värdet är 10. |Inga |3 |


## <a name="create-datasets"></a>Skapa datauppsättningar
Du kan skapa data uppsättningar genom att använda något av dessa verktyg eller SDK: er:

- Guiden Kopiera
- Visual Studio
- PowerShell
- Azure Resource Manager-mall
- REST-API
- .NET-API

I följande själv studie kurser finns stegvisa anvisningar för hur du skapar pipelines och data uppsättningar med hjälp av något av dessa verktyg eller SDK: er:

- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Bygg en pipeline med en data förflyttnings aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline har skapats och distribuerats kan du hantera och övervaka dina pipelines med hjälp av Azure Portal blad eller appen övervakning och hantering. I följande avsnitt finns stegvisa anvisningar:

- [Övervaka och hantera pipelines med hjälp av Azure Portal blad](data-factory-monitor-manage-pipelines.md)
- [Övervaka och hantera pipelines med hjälp av appen övervakning och hantering](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Data uppsättningar med omfång
Du kan skapa data uppsättningar som är begränsade till en pipeline med hjälp av egenskapen **data uppsättningar** . Dessa data uppsättningar kan endast användas av aktiviteter i denna pipeline, inte av aktiviteter i andra pipeliner. I följande exempel definieras en pipeline med två data uppsättningar (InputDataset-RDC och OutputDataset-RDC) som ska användas i pipelinen.

> [!IMPORTANT]
> Data uppsättningar med begränsat läge stöds bara med engångs pipelines (där **pipelineMode** är inställt på **Databasmigrering**). Mer information finns i [pipeline för databasmigrering](data-factory-create-pipelines.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Nästa steg
- Mer information om pipelines finns i [skapa pipeliner](data-factory-create-pipelines.md).
- Mer information om hur pipelines schemaläggs och körs finns i [schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md).
