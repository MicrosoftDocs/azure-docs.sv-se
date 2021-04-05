---
title: Flytta data till/från Azure-tabellen
description: Lär dig hur du flyttar data till/från Azure Table Storage med Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc8e0272dc690ed541883ae943c118cbbe2f1854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392127"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Flytta data till och från Azure-tabellen med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-table-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-table-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory kan du läsa mer i [Azure Table Storage Connector i v2](../connector-azure-table-storage.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data till och från Azure Table Storage. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten. 

Du kan kopiera data från alla käll data lager som stöds till Azure Table Storage eller från Azure Table Storage till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller handfat av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en Azure-Table Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager: 

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. 
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från ett Azure-Table Storage finns i avsnittet [JSON-exempel](#json-examples) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Table Storage: 

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Det finns två typer av länkade tjänster som du kan använda för att länka en Azure Blob-lagring till en Azure-datafabrik. De är: **AzureStorage** länkade tjänst-och **AzureStorageSas** -länkade tjänster. Den länkade tjänsten Azure Storage tillhandahåller data fabriken med global åtkomst till Azure Storage. Den länkade tjänsten Azure Storage SAS (Shared Access Signature) tillhandahåller data fabriken med begränsad/tidsbunden åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. I följande avsnitt finns mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för data uppsättningen av typen **AzureTable** har följande egenskaper.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell i Azure Table Database-instansen som den länkade tjänsten refererar till. |Ja. När ett tableName anges utan azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från den tabell som uppfyller frågan till målet. |

### <a name="schema-by-data-factory"></a>Schema efter Data Factory
För schema fria data lager, till exempel Azure Table, härleds schemat på något av följande sätt i Data Factory-tjänsten:

1. Om du anger data strukturen med hjälp av **struktur** egenskapen i data uppsättnings definitionen, följer Data Factory-tjänsten den här strukturen som schema. I det här fallet anges ett null-värde för det om en rad inte innehåller något värde för en kolumn.
2. Om du inte anger data strukturen med hjälp av egenskapen **struktur** i definitions definitionen för data uppsättning, Data Factory härleder schemat genom att använda den första raden i data. I detta fall, om den första raden inte innehåller det fullständiga schemat, saknas vissa kolumner i resultatet av kopierings åtgärden.

Därför är det bästa sättet för schema fria data källor att ange data strukturen med hjälp av **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata och principer är tillgängliga för alla typer av aktiviteter.

Vilka egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten å andra sidan varierar med varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

**AzureTableSource** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd den anpassade frågan för att läsa data. |Sträng för Azure Table-fråga. Se exemplen i nästa avsnitt. |Nej. När ett tableName anges utan azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från den tabell som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om förtäring av undantag för tabell saknas. |TRUE<br/>FALSE |Inga |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-exempel
Om Azure Table-kolumnen är av sträng typ:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Om Azure Table-kolumnen är av typen datetime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** stöder följande egenskaper i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standardvärdet för partitionerings nyckel som kan användas av mottagaren. |Ett sträng värde. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnyckel. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumn namn. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumn värden används som rad nyckel. Om inget anges ska du använda ett GUID för varje rad. |Ett kolumn namn. |Inga |
| azureTableInsertType |Det läge där data ska infogas i Azure-tabellen.<br/><br/>Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och rad nycklar har ersatts eller slås samman. <br/><br/>Information om hur de här inställningarna (sammanfoga och ersätt) fungerar finns i avsnittet [Infoga eller sammanfoga entiteter](/rest/api/storageservices/Insert-Or-Merge-Entity) och [Infoga eller ersätta entiteter](/rest/api/storageservices/Insert-Or-Replace-Entity) . <br/><br> Den här inställningen gäller på radnivå, inte på tabell nivå, och inget av alternativen tar bort rader i den utgående tabellen som inte finns i indata. |Sammanfoga (standard)<br/>bytt |Inga |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts. |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout har nåtts |tidsintervall<br/><br/>Exempel: "00:20:00" (20 minuter) |Nej (standard-timeout-värdet för Storage-klienten är 90 SEK) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mappa en käll kolumn till en mål kolumn med hjälp av JSON-egenskapen Translator innan du kan använda mål kolumnen som azureTablePartitionKeyName.

I följande exempel mappas käll kolumnens DivisionID till mål kolumnen: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID har angetts som partitionsnyckel.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-exempel
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Table Storage och Azure Blob Database. Data kan dock kopieras **direkt** från någon av källorna till någon av de mottagare som stöds. Mer information finns i avsnittet "data lager och format som stöds" i [Flytta data med hjälp av kopierings aktivitet](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Exempel: kopiera data från Azure-tabellen till Azure-blobben
Följande exempel visar:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för båda tabellerna & BLOB).
2. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
3. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Pipelinen](data-factory-create-pipelines.md) med kopierings aktivitet som använder AzureTableSource och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data som tillhör standardpartitionen i en Azure-tabell till en BLOB varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure Storage-tjänst:**

```JSON
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
Azure Data Factory stöder två typer av Azure Storage länkade tjänster: **AzureStorage** och **AzureStorageSas**. För det första anger du anslutnings strängen som innehåller konto nyckeln och för den senare, anger du URL: en för signatur för delad åtkomst (SAS). Mer information finns i avsnittet [länkade tjänster](#linked-service-properties) .  

**Data uppsättning för Azure Table-indata:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i Azure-tabellen.

Inställningen "extern": "true" informerar den Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
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

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopiera aktivitet i en pipeline med AzureTableSource och BlobSink:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **AzureTableSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges med egenskapen **AzureTableSourceQuery** väljer data från standardpartitionen varje timme som ska kopieras.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
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
            }
        ]
    }
}
```

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Exempel: kopiera data från Azure blob till Azure-tabell
Följande exempel visar:

1. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (används för båda tabellerna & BLOB)
2. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureTable](#dataset-properties).
4. [Pipelinen](data-factory-create-pipelines.md) med kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureTableSink](#copy-activity-properties).

Exemplet kopierar Time Series-data från en Azure-blob till en Azure-tabell varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Azure Storage (för den länkade tjänsten Azure Table & BLOB):**

```JSON
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

Azure Data Factory stöder två typer av Azure Storage länkade tjänster: **AzureStorage** och **AzureStorageSas**. För det första anger du anslutnings strängen som innehåller konto nyckeln och för den senare, anger du URL: en för signatur för delad åtkomst (SAS). Mer information finns i avsnittet [länkade tjänster](#linked-service-properties) .

**Data uppsättning för Azure Blob-indata:**

Data hämtas från en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden och fil namnet använder Tim delen av start tiden. inställningen "extern": "true" informerar Data Factory tjänsten om att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
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

**Data uppsättning för Azure Table-utdata:**

Exemplet kopierar data till en tabell med namnet "min tabell" i Azure-tabellen. Skapa en Azure-tabell med samma antal kolumner som du förväntar dig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Kopiera aktivitet i en pipeline med BlobSource och AzureTableSink:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
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
      }
    ]
  }
}
```
## <a name="type-mapping-for-azure-table"></a>Typ mappning för Azure-tabell
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande två stegs metod.

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till & från Azure-tabellen används följande [mappningar som definieras av Azure-Table service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) från Azure Table OData-typer till .net-typ och vice versa.

| OData-datatyp | .NET-typ | Information |
| --- | --- | --- |
| EDM. Binary |byte[] |En matris med byte upp till 64 KB. |
| Edm.Boolean |boolesk |Ett booleskt värde. |
| EDM. DateTime |DateTime |Ett 64-bitars värde uttryckt som UTC (Coordinated Universal Time). Det DateTime-intervall som stöds börjar från 12:00 midnatt, 1 januari 1601 A.D. (C.E.), UTC. Intervallet slutar den 31 december 9999. |
| Edm.Double |double |Ett 64-bitars flytt ALS värde. |
| EDM. GUID |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |Ett 32-bitars heltal. |
| Edm.Int64 |Int64 |Ett 64-bitars heltal. |
| Edm.String |Sträng |Ett UTF-16-kodat värde. Sträng värden kan vara upp till 64 KB. |

### <a name="type-conversion-sample"></a>Exempel på typ konvertering
Följande exempel är för att kopiera data från en Azure-blob till Azure-tabell med typ konverteringar.

Anta att BLOB-datauppsättningen är i CSV-format och innehåller tre kolumner. En av dem är en datetime-kolumn med ett anpassat datetime-format med förkortade franska namn för veckodag.

Definiera BLOB-källans data uppsättning enligt följande tillsammans med typ definitioner för kolumnerna.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Med typ mappningen från en Azure Table OData-typ till .NET-typ definierar du tabellen i Azure-tabellen med följande schema.

**Azure Table-schema:**

| Kolumnnamn | Typ |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |EDM. DateTime |

Definiera sedan Azure Table-datauppsättningen på följande sätt. Du behöver inte ange "struktur"-avsnittet med typ informationen eftersom typ informationen redan har angetts i det underliggande data lagret.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

I det här fallet är Data Factory automatiskt typ konverteringar inklusive datetime-fältet med det anpassade datetime-formatet med hjälp av kulturen "fr-fr" när data flyttas från blob till Azure-tabell.

> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Mer information om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md).