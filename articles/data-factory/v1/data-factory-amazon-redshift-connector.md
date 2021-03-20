---
title: Flytta data från Amazon RedShift med hjälp av Azure Data Factory
description: Lär dig hur du flyttar data från Amazon-RedShift med hjälp av Azure Data Factory kopierings aktivitet.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 025250f47bf0630be5ae988140a5feeecfd0eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377558"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Flytta data från Amazon RedShift med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-amazon-redshift-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-redshift.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Amazon RedShift Connector i v2](../connector-amazon-redshift.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från Amazon RedShift. Artikeln bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en allmän översikt över data förflyttning med kopierings aktiviteten.

Data Factory stöder för närvarande endast flytt av data från Amazon-RedShift till ett [mottagar data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Det finns inte stöd för att flytta data från andra data lager till Amazon-RedShift.

> [!TIP]
> För att uppnå bästa möjliga prestanda vid kopiering av stora mängder data från Amazon RedShift bör du överväga att använda det inbyggda RedShift **Unload** -kommandot via Amazon Simple Storage Service (Amazon S3). Mer information finns i [använda Unload för att kopiera data från Amazon RedShift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Förutsättningar
* Om du flyttar data till ett lokalt data lager installerar du [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal dator. Bevilja åtkomst för en gateway till Amazon RedShift-klustret med hjälp av den lokala datorns IP-adress. Instruktioner finns i [bevilja åtkomst till klustret](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Information om hur du flyttar data till ett Azure-datalager finns i [Compute IP-adress och SQL-intervall som används av Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet för att flytta data från en Amazon RedShift-källa med hjälp av olika verktyg och API: er.

Det enklaste sättet att skapa en pipeline är att använda guiden Azure Data Factory kopiering. En snabb genom gång av hur du skapar en pipeline med hjälp av kopierings guiden finns i [självstudien: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också skapa en pipeline med hjälp av Visual Studio, Azure PowerShell eller andra verktyg. Azure Resource Manager mallar, .NET-API: et eller REST API kan också användas för att skapa pipelinen. Stegvisa instruktioner för att skapa en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa länkade tjänster för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data uppsättningar som representerar indata och utdata för kopierings åtgärden.
3. Skapa en pipeline med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden Kopiera skapas JSON-definitioner för dessa Data Factory entiteter automatiskt. När du använder verktyg eller API: er (förutom .NET-API: et) definierar du Data Factory entiteter med hjälp av JSON-formatet. JSON-exemplet: kopiera data från Amazon RedShift till Azure Blob Storage visar JSON-definitionerna för de Data Factory entiteter som används för att kopiera data från ett Amazon RedShift-data lager.

I följande avsnitt beskrivs de JSON-egenskaper som används för att definiera Data Factory entiteter för Amazon-RedShift.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande tabell innehåller beskrivningar av de JSON-element som är speciella för en Amazon RedShift-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **bastyp** |Den här egenskapen måste anges till **AmazonRedshift**. |Ja |
| **servernamn** |IP-adressen eller värd namnet för Amazon RedShift-servern. |Ja |
| **lastning** |Numret på den TCP-port som Amazon RedShift-servern använder för att lyssna efter klient anslutningar. |Nej (standard är 5439) |
| **databas** |Namnet på Amazon RedShift-databasen. |Ja |
| **användar** |Namnet på den användare som har åtkomst till databasen. |Ja |
| **lösenord** |Lösen ordet för användar kontot. |Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En lista över de avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitten **struktur**, **tillgänglighet** och **princip** liknar varandra för alla typer av data uppsättningar. Exempel på data uppsättnings typer är Azure SQL, Azure Blob Storage och Azure Table Storage.

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i arkivet. Avsnittet **typeProperties** för en data uppsättning av typen **RelationalTable**, som innehåller Amazon RedShift-datauppsättningen, har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **tableName** |Namnet på tabellen i Amazon RedShift-databasen som den länkade tjänsten refererar till. |Nej (om egenskapen **fråga** för en kopierings aktivitet av typen **RelationalSource** har angetts) |

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

En lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaperna **namn**, **Beskrivning**, **indata** , tabell, **utdata** och **princip** är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i avsnittet **typeProperties** varierar för varje aktivitets typ. För kopierings aktiviteten varierar egenskaperna beroende på typerna av data källor och mottagare.

För kopierings aktiviteten, när källan är av typen **AmazonRedshiftSource**, finns följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **frågeterm** | Använd den anpassade frågan för att läsa data. |Nej (om egenskapen **TableName** för en data uppsättning anges) |
| **redshiftUnloadSettings** | Innehåller egenskaps gruppen när du använder kommandot RedShift **Unload** . | Inga |
| **s3LinkedServiceName** | Amazon S3 som används som ett interimistiskt lager. Den länkade tjänsten anges med ett Azure Data Factory namn av typen **en awsaccesskey**. | Krävs när du använder egenskapen **redshiftUnloadSettings** |
| **bucketName** | Anger den Amazon S3-Bucket som ska användas för att lagra interims data. Om den här egenskapen inte anges genererar kopiera aktivitet automatiskt en Bucket. | Krävs när du använder egenskapen **redshiftUnloadSettings** |

Du kan också använda **RelationalSource** -typen, som innehåller Amazon Redshift, med följande egenskap i avsnittet **typeProperties** . OBS! den här käll typen har inte stöd för kommandot RedShift **Unload** .

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **frågeterm** |Använd den anpassade frågan för att läsa data. | Nej (om egenskapen **TableName** för en data uppsättning anges) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd inläsning för att kopiera data från Amazon RedShift

Kommandot Amazon RedShift [**Unload**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) tar bort resultatet från en fråga till en eller flera filer på Amazon S3. Det här kommandot rekommenderas av Amazon för att kopiera stora data mängder från RedShift.

**Exempel: kopiera data från Amazon RedShift till Azure Synapse Analytics**

Det här exemplet kopierar data från Amazon RedShift till Azure Synapse Analytics. Exemplet använder kommandot RedShift **Unload** , mellanlagrade kopierings data och Microsoft PolyBase.

För det här exemplet använder kopierings aktiviteten först bort data från Amazon RedShift till Amazon S3 enligt konfigurationen i alternativet  **redshiftUnloadSettings** . Därefter kopieras data från Amazon S3 till Azure Blob Storage enligt vad som anges i alternativet **stagingSettings** . Slutligen laddar PolyBase data i Azure Synapse Analytics. Alla interimistiska format hanteras av kopierings aktiviteten.

![Kopiera arbets flöde från Amazon RedShift till Azure Synapse Analytics](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-exempel: kopiera data från Amazon RedShift till Azure Blob Storage
Det här exemplet visar hur du kopierar data från en Amazon RedShift-databas till Azure Blob Storage. Data kan kopieras direkt till alla [mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten.

Exemplet har följande data Factory-entiteter:

* En länkad tjänst av typen [AmazonRedshift](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties)
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder egenskaperna [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från ett frågeresultat i Amazon-RedShift till en Azure-Blob per timme. De JSON-egenskaper som används i exemplet beskrivs i avsnitten som följer enhets definitionerna.

**Amazon RedShift-länkad tjänst**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Länkad Azure Blob Storage-tjänst**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Amazon RedShift-indata-datauppsättning**

Den **externa** egenskapen anges till "true" för att informera Data Factory-tjänsten om att data uppsättningen är extern för data fabriken. Den här egenskaps inställningen anger att data uppsättningen inte produceras av en aktivitet i data fabriken. Ange egenskapen till true för en indata-datauppsättning som inte produceras av en aktivitet i pipelinen.

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

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny BLOB varje timme genom att ange **frekvens** egenskapen till "timme" och egenskapen **Interval** till 1. **FolderPath** -egenskapen för blobben utvärderas dynamiskt. Egenskap svärdet baseras på Start tiden för den sektor som bearbetas. Mappsökvägen använder de delar av start tiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med en Azure RedShift-källa (av typen RelationalSource) och en Azure Blob-mottagare**

Pipelinen innehåller en kopierings aktivitet som är konfigurerad att använda data uppsättningar för indata och utdata. Pipelinen är schemalagd att köras varje timma. I JSON-definitionen för pipelinen anges **käll** typen till **RelationalSource** och **mottagar** typen är inställd på **BlobSink**. Den SQL-fråga som angetts för egenskapen **fråga** väljer vilka data som ska kopieras från den senaste timmen.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Typ mappning för Amazon RedShift
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typen till mottagar typ. Typerna konverteras med hjälp av en två stegs metod:

1. Konvertera från en ursprunglig källtyp till en .NET-typ
2. Konvertera från en .NET-typ till en typ av intern mottagare

Följande mappningar används när kopierings aktiviteten konverterar data från en Amazon RedShift-typ till en .NET-typ:

| Amazon RedShift-typ | .NET-typ |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |Decimal |
| REAL |Enkel |
| DUBBEL PRECISION |Double |
| BOOLESKT |Sträng |
| CHAR |Sträng |
| VARCHAR |Sträng |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |Sträng |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i mottagar data uppsättningen finns i avsnittet [mappa data uppsättnings kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Upprepnings bara läsningar från Relations källor
När du kopierar data från ett Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera **principen** för återförsök för en data uppsättning för att köra en sektor igen när ett fel uppstår. Se till att samma data är lästa, oavsett hur många gånger som sektorn körs igen. Se också till att samma data är lästa oavsett hur du kör om sektorn. Mer information finns i [repeterbara läsningar från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Lär dig mer om viktiga faktorer som påverkar prestandan för kopierings aktiviteten och hur du optimerar prestanda i [guiden Kopiera aktivitet prestanda och justering](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för hur du skapar en pipeline med kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
