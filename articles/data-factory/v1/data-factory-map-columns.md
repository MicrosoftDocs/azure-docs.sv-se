---
title: Mappa data mängds kolumner i Azure Data Factory
description: Lär dig hur du mappar käll kolumner till mål kolumner.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371132"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mappa käll data uppsättnings kolumner till kolumner med mål data uppsättningar
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. 

Kolumn mappning kan användas för att ange hur kolumner som anges i "struktur" i käll tabellens karta till kolumner som anges i tabellen "struktur" i mottagar tabellen. Egenskapen **columnMapping** är tillgänglig i avsnittet **typeProperties** i kopierings aktiviteten.

Kolumn mappningen stöder följande scenarier:

* Alla kolumner i käll data uppsättningens struktur mappas till alla kolumner i data uppsättnings strukturen för mottagaren.
* En delmängd av kolumnerna i käll data uppsättningens struktur mappas till alla kolumner i data uppsättnings strukturen för mottagaren.

Följande är fel villkor som resulterar i ett undantag:

* Antingen färre kolumner eller fler kolumner i tabellen "struktur" i mottagar tabellen än vad som anges i mappningen.
* Duplicera mappning.
* SQL-frågeresultaten har inget kolumn namn som anges i mappningen.

> [!NOTE]
> Följande exempel gäller för Azure SQL och Azure Blob, men de gäller för alla data lager som stöder rektangulära data uppsättningar. Justera data uppsättningen och de länkade tjänst definitionerna i exempel för att peka på data i den relevanta data källan.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exempel 1 – kolumn mappning från Azure SQL till Azure-Blob
I det här exemplet har indatacellen en struktur och den pekar på en SQL-tabell i Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
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

I det här exemplet har utdatatabellen en struktur och den pekar på en BLOB i Azure Blob Storage.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
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
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Följande JSON definierar en kopierings aktivitet i en pipeline. Kolumnerna från källan mappas till kolumner i handfat (**columnMappings**) med hjälp av egenskapen **Translator** .

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Flöde för kolumn mappning:**

![Flöde för kolumn mappning](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exempel 2 – kolumn mappning med SQL-fråga från Azure SQL till Azure-Blob
I det här exemplet används en SQL-fråga för att extrahera data från Azure SQL i stället för att helt enkelt ange tabell namnet och kolumn namnen i avsnittet "struktur". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
I det här fallet mappas frågeresultaten först till kolumner som anges i strukturen för källan. Sedan mappas kolumnerna från källans struktur till kolumner i handfatet "struktur" med regler som anges i columnMappings.  Anta att frågan returnerar 5 kolumner, två fler kolumner än de som anges i strukturen för källan.

**Flöde för kolumn mappning**

![Flöde för kolumn mappning – 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Nästa steg
I artikeln finns en själv studie kurs om hur du använder kopierings aktivitet: 

- [Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
