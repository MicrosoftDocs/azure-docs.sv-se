---
title: Flytta data till/från Azure Cosmos DB
description: Lär dig hur du flyttar data till/från Azure Cosmos DB samling med Azure Data Factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
robots: noindex
ms.openlocfilehash: a90b26c677c3d53794bce3c2ff48c618415e4cf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379377"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Flytta data till och från Azure Cosmos DB med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Azure Cosmos DB Connector i v2](../connector-azure-cosmos-db.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data till/från Azure Cosmos DB (SQL API). Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från alla käll data lager som stöds till Azure Cosmos DB eller från Azure Cosmos DB till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller handfat av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

> [!IMPORTANT]
> Azure Cosmos DB Connector har endast stöd för SQL-API: et.

Om du vill kopiera data som-är till/från JSON-filer eller en annan Cosmos DB samling, se [Importera/exportera JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från Azure Cosmos DB med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från Cosmos DB finns i avsnittet om [JSON-exempel](#json-examples) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Cosmos DB:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är speciella för Azure Cosmos DB länkade tjänsten.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **DocumentDb** |Ja |
| Begär |Ange information som krävs för att ansluta till Azure Cosmos DB databasen. |Ja |

Exempel:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar varandra för alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **DocumentDbCollection** har följande egenskaper.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| Samling |Namnet på den Cosmos DB dokument samlingen. |Ja |

Exempel:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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
### <a name="schema-by-data-factory"></a>Schema efter Data Factory
För schema fria data lager, till exempel Azure Cosmos DB, härleder Data Factory-tjänsten schemat på något av följande sätt:

1. Om du anger data strukturen med hjälp av **struktur** egenskapen i data uppsättnings definitionen, följer Data Factory-tjänsten den här strukturen som schema. I det här fallet, om en rad inte innehåller något värde för en kolumn, så anges ett null-värde för det.
2. Om du inte anger data strukturen med hjälp av **struktur** egenskapen i data uppsättnings definitionen data Factory, härleds schemat med hjälp av den första raden i data. I det här fallet, om den första raden inte innehåller det fullständiga schemat, kommer vissa kolumner saknas i resultatet av kopierings åtgärden.

Därför är det bästa sättet för schema fria data källor att ange data strukturen med hjälp av **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om att [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopierings aktiviteten tar bara en indata och producerar bara ett resultat.

Vilka egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten å andra sidan varierar med varje aktivitets typ och om kopierings aktiviteten varierar beroende på typerna av källor och mottagare.

Om kopierings aktiviteten är av typen **DocumentDbCollectionSource** är följande egenskaper tillgängliga i avsnittet **typeProperties** :

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om inget anges används SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslat |Valfritt Character. <br/><br/>Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användaren att ange hierarki via nestingSeparator, vilket är "." i exemplen ovan. Med avgränsaren genererar kopierings aktiviteten "name"-objektet med tre underordnade element först, i mitten och sist, enligt namnet. First "," Name. mitten "och" Name. last "i tabell definitionen. |Inga |

**DocumentDbCollectionSink** stöder följande egenskaper:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i käll kolumnens namn som anger att det kapslade dokumentet behövs. <br/><br/>Till exempel ovan: `Name.First` i tabellen utdata skapas följande JSON-struktur i Cosmos DB-dokumentet:<br/><br/>"Namn": {<br/>    "First": "John"<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (dot). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (dot). |
| writeBatchSize |Antal parallella förfrågningar till Azure Cosmos DB tjänst för att skapa dokument.<br/><br/>Du kan finjustera prestandan när du kopierar data till/från Cosmos DB med hjälp av den här egenskapen. Du kan vänta en bättre prestanda när du ökar writeBatchSize eftersom fler parallella förfrågningar till Cosmos DB skickas. Du måste dock undvika begränsning som kan resultera i fel meddelandet: "begär ande frekvens är stor".<br/><br/>Begränsningen bestäms av ett antal faktorer, inklusive dokument storlek, antal villkor i dokument, indexerings policy för mål samling osv. För kopierings åtgärder kan du använda en bättre samling (t. ex. S3) för att få flest data flöde tillgängligt (2 500 enheter för programbegäran/sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänte tid för att slutföra åtgärden innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

## <a name="importexport-json-documents"></a>Importera/exportera JSON-dokument
Med hjälp av den här Cosmos DB-anslutningen kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos DB, inklusive Azure Blob, Azure Data Lake, lokalt fil system eller andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB samling till olika filbaserade butiker.
* Migrera data mellan två Cosmos DB samlingar som de är.

För att uppnå sådan oberoende kopia,
* När du använder guiden Kopiera kontrollerar du alternativet **"Exportera som-är till JSON-filer eller Cosmos DB samling"** .
* När du använder JSON-redigering ska du inte ange "struktur"-avsnittet i Cosmos DB data uppsättning (er) eller egenskapen "nestingSeparator" på Cosmos DB källa/mottagare i kopierings aktiviteten. Om du vill importera från/exportera till JSON-filer anger du format typ som "JsonFormat", config "filePattern" i fil arkivets data uppsättning och hoppar över rest-format-inställningarna i avsnittet [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format) på information.

## <a name="json-examples"></a>JSON-exempel
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Cosmos DB och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exempel: kopiera data från Azure Cosmos DB till Azure-Blob
Exemplet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [DocumentDbCollection](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [DocumentDbCollectionSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data i Azure Cosmos DB till Azure-blobben. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Azure Cosmos DB länkad tjänst:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Länkad Azure Blob Storage-tjänst:**

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
**Data uppsättning för Azure Document DB:**

Exemplet förutsätter att du har en samling med namnet **person** i en Azure Cosmos DB databas.

Inställningen "extern": "true" och ange extern Aldata-princip information Azure Data Factory tjänsten som tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
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

**Data uppsättning för Azure Blob-utdata:**

Data kopieras till en ny BLOB varje timme med sökvägen för bloben som motsvarar angiven datetime med timkostnad.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Exempel-JSON-dokument i person samlingen i en Cosmos DB databas:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB har stöd för att skicka frågor till dokument med en SQL like-syntax över hierarkiska JSON-dokument.

Exempel:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Följande pipeline kopierar data från person samlingen i Azure Cosmos DB-databasen till en Azure-blob. Som en del av kopierings aktiviteten har data uppsättningarna indata och utdata angetts.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
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
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exempel: kopiera data från Azure blob till Azure Cosmos DB
Exemplet nedan visar:

1. En länkad tjänst av typen DocumentDb.
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen DocumentDbCollection.
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och DocumentDbCollectionSink.

Exemplet kopierar data från Azure blob till Azure Cosmos DB. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure Blob Storage-tjänst:**

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
**Azure Cosmos DB länkad tjänst:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Data uppsättning för Azure Blob-indata:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB data uppsättning för utdata:**

Exemplet kopierar data till en samling med namnet "person".

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Följande pipeline kopierar data från Azure blob till person samlingen i Cosmos DB. Som en del av kopierings aktiviteten har data uppsättningarna indata och utdata angetts.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
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
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Om insamlingen av BLOB-inmatare är som

```
1,John,,Doe
```
Sedan blir utdata-JSON i Cosmos DB:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användaren att beteckna en hierarki via **nestingSeparator**, vilket är "." i det här exemplet. Med avgränsaren genererar kopierings aktiviteten "name"-objektet med tre underordnade element först, i mitten och sist, enligt namnet. First "," Name. mitten "och" Name. last "i tabell definitionen.

## <a name="appendix"></a>Bilaga
1. **Fråga:** Stöder kopierings aktiviteten uppdatering av befintliga poster?

    **Svar:** Nej.
2. **Fråga:** Hur gör ett nytt försök för en kopia att Azure Cosmos DB hantera redan kopierade poster?

    **Svar:** Om poster har ett "ID"-fält och kopierings åtgärden försöker infoga en post med samma ID, genererar kopierings åtgärden ett fel.
3. **Fråga:** Har Data Factory stöd för [intervall eller hash-baserad data partitionering](../../cosmos-db/partitioning-overview.md)?

    **Svar:** Nej.
4. **Fråga:** Kan jag ange mer än en Azure Cosmos DB samling för en tabell?

    **Svar:** Nej. Det går bara att ange en samling för tillfället.

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.