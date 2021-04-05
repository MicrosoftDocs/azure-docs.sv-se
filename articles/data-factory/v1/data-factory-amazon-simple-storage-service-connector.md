---
title: Flytta data från Amazon Simple Storage-tjänsten med hjälp av Data Factory
description: Lär dig mer om hur du flyttar data från Amazon Simple Storage Service (S3) med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6d254a535b1db53478772b481bd029a8c4db6f3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361354"
---
# <a name="move-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Flytta data från Amazon Simple Storage-tjänsten med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-amazon-simple-storage-service-connector.md)
> * [Version 2 (aktuell version)](../connector-amazon-simple-storage-service.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Amazon S3 Connector i v2](../connector-amazon-simple-storage-service.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från Amazon Simple Storage Service (S3). Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från Amazon S3 till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från Amazon S3 till andra data lager, men inte flytta data från andra data lager till Amazon S3.

## <a name="required-permissions"></a>Behörigheter som krävs
Om du vill kopiera data från Amazon S3 ser du till att du har beviljats följande behörigheter:

* `s3:GetObject` och `s3:GetObjectVersion` för Amazon S3-objekt åtgärder.
* `s3:ListBucket` för Amazon S3-Bucket-åtgärder. Om du använder Data Factory kopierings guiden `s3:ListAllMyBuckets` krävs även.

Mer information om den fullständiga listan över Amazon S3-behörigheter finns i [Ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från en Amazon S3-källa med hjälp av olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. En snabb genom gång finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Stegvisa instruktioner för att skapa en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Oavsett om du använder verktyg eller API: er utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg eller API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett Amazon S3-data lager finns i [JSON-exemplet: kopiera data från Amazon S3 till Azure Blob](#json-example-copy-data-from-amazon-s3-to-azure-blob-storage) i den här artikeln.

> [!NOTE]
> Mer information om fil-och komprimerings format som stöds för en kopierings aktivitet finns [i fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Amazon S3.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
En länkad tjänst länkar ett data lager till en data fabrik. Du skapar en länkad tjänst av typen **en awsaccesskey** för att länka ditt Amazon S3-data lager till din data fabrik. Följande tabell innehåller en beskrivning av JSON-element som är speciella för den länkade tjänsten Amazon S3 (en awsaccesskey).

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomst nyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomst nyckeln. |Krypterad hemlig sträng |Ja |

>[!NOTE]
>Den här anslutningen kräver åtkomst nycklar för IAM-kontot för att kopiera data från Amazon S3. [Tillfälliga säkerhets referenser](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) stöds inte.
>

Här är ett exempel:

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

## <a name="dataset-properties"></a>Egenskaper för datamängd
Ange en data uppsättning som representerar indata i Azure Blob Storage genom att ange egenskapen type för data uppsättningen till **AmazonS3**. Ange egenskapen **linkedServiceName** för data uppsättningen till namnet på den länkade tjänsten Amazon S3. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [skapa data uppsättningar](data-factory-create-datasets.md). 

Avsnitt som struktur, tillgänglighet och princip liknar varandra för alla typer av data uppsättningar (till exempel SQL Database, Azure blob och Azure Table). Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för en data uppsättning av typen **AmazonS3** (som innehåller Amazon S3-datauppsättningen) har följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| bucketName |S3-Bucket-namn. |Sträng |Ja |
| key |S3-objektets nyckel. |Sträng |Inga |
| protokollprefixet |Prefix för S3-objekt nyckeln. Objekt vars nycklar börjar med det här prefixet väljs. Gäller endast när nyckeln är tom. |Sträng |Inga |
| version |Versionen av S3-objektet, om S3-versioner är aktive rad. |Sträng |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i avsnitten [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Om du vill kopiera filer som är mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. | |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). | |Inga |


> [!NOTE]
> **bucketName + Key** anger platsen för S3-objektet, där Bucket är rot behållaren för S3-objekt och nyckeln är den fullständiga sökvägen till S3-objektet.

### <a name="sample-dataset-with-prefix"></a>Exempel data uppsättning med prefix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "testbucket",
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
### <a name="sample-dataset-with-version"></a>Exempel data uppsättning (med version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "testbucket",
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

### <a name="dynamic-paths-for-s3"></a>Dynamiska sökvägar för S3
I föregående exempel används fasta värden för egenskaperna **Key** och **BucketName** i Amazon S3-datauppsättningen.

```json
"key": "testFolder/test.orc",
"bucketName": "testbucket",
```

Du kan låta Data Factory beräkna dessa egenskaper dynamiskt vid körning, genom att använda systemvariabler som SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma sak för egenskapen **prefix** för en Amazon S3-data uppsättning. En lista över funktioner som stöds och variabler finns i [Data Factory Functions and system Variables](data-factory-functions-variables.md).

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipeliner](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, in-och utdata-tabeller och principer är tillgängliga för alla typer av aktiviteter. Vilka egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktiviteten varierar egenskaperna beroende på typerna av källor och mottagare. När en källa i kopierings aktiviteten är av typen **FileSystemSource** (som innehåller Amazon S3) är följande egenskap tillgängligt i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om S3-objekt ska listas rekursivt under katalogen. |True/false |Inga |

## <a name="json-example-copy-data-from-amazon-s3-to-azure-blob-storage"></a>JSON-exempel: kopiera data från Amazon S3 till Azure Blob Storage
Det här exemplet visar hur du kopierar data från Amazon S3 till en Azure Blob Storage. Data kan dock kopieras direkt till [någon av de handfat som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten i Data Factory.

Exemplet innehåller JSON-definitioner för följande Data Factory entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från Amazon S3 till Blob Storage med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).   

* En länkad tjänst av typen [en awsaccesskey](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AmazonS3](#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från Amazon S3 till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

### <a name="amazon-s3-linked-service"></a>Amazon S3-länkad tjänst

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

### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

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

### <a name="amazon-s3-input-dataset"></a>Data uppsättning för Amazon S3-indata

Inställningen **"extern": true** informerar den Data Factory tjänsten som data uppsättningen är extern för data fabriken. Ange den här egenskapen till true för en indata-datauppsättning som inte produceras av en aktivitet i pipelinen.

```json
    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
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


### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder de delar av start tiden för år, månad, dag och timmar.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="copy-activity-in-a-pipeline-with-an-amazon-s3-source-and-a-blob-sink"></a>Kopiera aktivitet i en pipeline med en Amazon S3-källa och en BLOB-mottagare

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **FileSystemSource** och **mottagar** typen är inställd på **BlobSink**.

```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
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
                "inputs": [
                    {
                        "name": "AmazonS3InputDataset"
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
                "name": "AmazonS3ToBlob"
            }
        ],
        "start": "2014-08-08T18:00:00Z",
        "end": "2014-08-08T19:00:00Z"
    }
}
```
> [!NOTE]
> Information om hur du mappar kolumner från en käll data uppsättning till kolumner från en Sink-datauppsättning finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Information om viktiga faktorer som påverkar prestanda för data flytt (kopierings aktivitet) i Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitets prestanda och justering](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
