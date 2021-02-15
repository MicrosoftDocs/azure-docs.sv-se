---
title: Hämta metadata-aktivitet i Azure Data Factory
description: Lär dig hur du använder aktiviteten hämta metadata i en Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: f860225862dcbfb79535acfbd6eeb89a217e7ae9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385497"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Hämta metadata-aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Du kan använda aktiviteten hämta metadata för att hämta metadata för alla data i Azure Data Factory. Du kan använda den här aktiviteten i följande scenarier:

- Verifiera metadata för alla data.
- Utlös en pipeline när data är klara/tillgängliga.

Följande funktioner är tillgängliga i kontroll flödet:

- Du kan använda utdata från aktiviteten hämta metadata i villkors uttryck för att utföra verifieringen.
- Du kan utlösa en pipeline när ett villkor är uppfyllt genom att göra tills loopar.

## <a name="capabilities"></a>Funktioner

Aktiviteten hämta metadata tar en data uppsättning som indata och returnerar metadatainformation som utdata. För närvarande stöds följande anslutningar och motsvarande hämtnings bara metadata. Den maximala storleken för returnerade metadata är cirka 4 MB.

>[!NOTE]
>Om du kör aktiviteten hämta metadata i en integration runtime med egen värd, stöds de senaste funktionerna på version 3,6 eller senare.

### <a name="supported-connectors"></a>Anslutningar som stöds

**File Storage**

| Koppling/metadata | itemName<br>(fil/mapp) | itemType<br>(fil/mapp) | ikoner<br>Arkiv | Create<br>(fil/mapp) | lastModified<br>(fil/mapp) |childItems<br>projektbevakningsmappen |contentMD5<br>Arkiv | hierarkistruktur<br/>Arkiv | Antal<br>Arkiv | finns<br>(fil/mapp) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Filsystem](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- När du använder hämta metadata-aktivitet mot en mapp kontrollerar du att du har behörigheten lista/kör till den aktuella mappen.
- För Amazon S3 och Google Cloud Storage `lastModified` gäller Bucket och nyckeln, men inte den virtuella mappen, och gäller även för `exists` Bucket och nyckeln, men inte till prefixet eller den virtuella mappen.
- För Azure Blob Storage `lastModified` gäller för behållaren och blobben, men inte i den virtuella mappen.
- `lastModified` filtret används för närvarande för att filtrera underordnade objekt, men inte den angivna mappen/filen.
- Wildcard-filter i mappar/filer stöds inte för aktiviteten hämta metadata.
- `structure` och `columnCount` stöds inte när metadata hämtas från binär-, JSON-eller XML-filer.

**Relationsdatabas**

| Koppling/metadata | hierarkistruktur | Antal | finns |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Hanterad Azure SQL-instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metadata-alternativ

Du kan ange följande typer av metadata i listan Hämta metadata aktivitet fält för att hämta motsvarande information:

| Typ av metadata | Beskrivning |
|:--- |:--- |
| itemName | Filens eller mappens namn. |
| itemType | Typ av fil eller mapp. Returnerat värde är `File` eller `Folder` . |
| ikoner | Filens storlek i byte. Gäller endast för filer. |
| Create | Datum/tid för filen eller mappen har skapats. |
| lastModified | Datum och tid då filen eller mappen senast ändrades. |
| childItems | Lista över undermappar och filer i den aktuella mappen. Gäller endast för mappar. Returnerat värde är en lista över namn och typ för varje underordnat objekt. |
| contentMD5 | MD5 av filen. Gäller endast för filer. |
| hierarkistruktur | Data strukturen för filen eller Relations databas tabellen. Returnerat värde är en lista med kolumn namn och kolumn typer. |
| Antal | Antalet kolumner i filen eller Relations tabellen. |
| finns| Om en fil, mapp eller tabell finns. Om `exists` anges i fält listan Hämta metadata, kommer aktiviteten inte att kunna köras även om filen, mappen eller tabellen inte finns. I stället `exists: false` returneras i utdata. |

>[!TIP]
>När du vill kontrol lera att det finns en fil, mapp eller tabell, anger `exists` du i fält listan Hämta metadata-aktivitet. Sedan kan du kontrol lera `exists: true/false` resultatet i aktivitetens utdata. Om `exists` inte anges i fält listan, kommer get metadata-aktiviteten inte att fungera om objektet inte hittas.

>[!NOTE]
>När du hämtar metadata från fil Arkiv och konfigurerar `modifiedDatetimeStart` eller `modifiedDatetimeEnd` , `childItems` kommer i-utdata bara att innehålla filer på den angivna sökvägen som har en senaste ändrings tid inom det angivna intervallet. I innehåller inte objekt i undermappar.

## <a name="syntax"></a>Syntax

**Hämta metadata-aktivitet**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Data uppsättning**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Typ egenskaper

För närvarande kan aktiviteten hämta metadata returnera följande typer av metadatainformation:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
Fält lista | De typer av metadatainformation som krävs. Mer information om metadata som stöds finns i avsnittet [metadata-alternativ](#metadata-options) i den här artikeln. | Ja 
data uppsättning | Referens data uppsättningen vars metadata ska hämtas av aktiviteten hämta metadata. I avsnittet [funktioner](#capabilities) finns information om anslutnings program som stöds. Information om syntax för data uppsättning finns i specifika anslutnings avsnitt. | Ja
formatSettings | Använd när du använder data uppsättning för format typ. | Inga
storeSettings | Använd när du använder data uppsättning för format typ. | Inga

## <a name="sample-output"></a>Exempelutdata

Resultatet från hämta metadata visas i aktivitetens utdata. Nedan visas två exempel som visar omfattande metadata-alternativ. Använd följande mönster om du vill använda resultatet i en efterföljande aktivitet: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Hämta metadata för en fil

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Hämta metadata för en mapp

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om andra kontroll flödes aktiviteter som stöds av Data Factory:

- [Kör pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [ForEach-aktivitet](control-flow-for-each-activity.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
