---
title: Kopiera data till/från Azure SQL Database
description: Lär dig hur du kopierar data till/från Azure SQL Database med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370520"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Database med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-sql-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-database.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Azure SQL Database Connector i v2](../connector-azure-sql-database.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data till och från Azure SQL Database. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Database** till följande data lager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure SQL Database Connector stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en Azure SQL Database med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du till exempel kopierar data från en Azure Blob Storage till Azure SQL Database, skapar du två länkade tjänster för att länka ditt Azure Storage-konto och Azure SQL Database till din data fabrik. För länkade tjänst egenskaper som är speciella för Azure SQL Database, se avsnittet [länkade tjänst egenskaper](#linked-service-properties) .
3. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange BLOB-behållaren och mappen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange SQL-tabellen i Azure SQL Database som innehåller data som kopieras från blob-lagringen. För data uppsättnings egenskaper som är speciella för Azure Data Lake Store, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
4. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du BlobSource som källa och SqlSink som mottagare för kopierings aktiviteten. På samma sätt kan du använda SqlSource och BlobSink i kopierings aktiviteten om du kopierar från Azure SQL Database till Azure Blob Storage. Information om kopierings aktiviteter som är speciell för Azure SQL Database finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från en Azure SQL Database finns i avsnittet om [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-database) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure SQL Database:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
En länkad Azure SQL-tjänst länkar Azure SQL Database till din data fabrik. Följande tabell innehåller en beskrivning av JSON-element som är speciella för den länkade Azure SQL-tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **AzureSqlDatabase** |Ja |
| Begär |Ange information som krävs för att ansluta till Azure SQL Database-instansen för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandväggen](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) som databas servern ska [tillåta Azure-tjänster att få åtkomst till servern](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Om du kopierar data till Azure SQL Database utanför Azure, inklusive från lokala data källor med Data Factory Gateway, konfigurerar du dessutom lämpligt IP-adressintervall för datorn som skickar data till Azure SQL Database.

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en data uppsättning som representerar indata eller utdata i Azure SQL Database, ställer du in data uppsättningens typ-egenskap på: **AzureSqlTable**. Ange egenskapen **linkedServiceName** för data uppsättningen till namnet på den länkade Azure SQL-tjänsten.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för data uppsättningen av typen **AzureSqlTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instansen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopierings aktiviteten tar bara en indata och producerar bara ett resultat.

De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

Om du flyttar data från Azure SQL Database anger du käll typen i kopierings aktiviteten till **SqlSource**. På samma sätt, om du flyttar data till Azure SQL Database, ställer du in mottagar typen i kopierings aktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

### <a name="sqlsource"></a>SqlSource
När källan är av typen **SqlSource** i kopierings aktivitet är följande egenskaper tillgängliga i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource kör kopierings aktiviteten den här frågan mot Azure SQL Database källan för att hämta data. Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definierats i avsnittet struktur i JSON-datauppsättnings-JSON för att skapa en fråga ( `select column1, column2 from mytable` ) för att köra mot Azure SQL Database. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName** måste du fortfarande ange ett värde för egenskapen **TableName** i data uppsättnings-JSON. Det finns inga verifieringar som utförts i den här tabellen.
>
>

### <a name="sqlsource-example"></a>SqlSource-exempel

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Definitionen för den lagrade proceduren:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. Mer information finns i [repeterbar kopia](#repeatable-copy). |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Ange ett kolumn namn för kopierings aktiviteten som ska fyllas med automatiskt genererad sektor identifierare, som används för att rensa data i en speciell sektor när den körs igen. Mer information finns i [repeterbar kopia](#repeatable-copy). |Kolumn namnet för en kolumn med data typen Binary (32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur käll data ska användas i mål tabellen, t. ex. för att göra upsertar eller transformera med din egen affärs logik. <br/><br/>Observera att den lagrade proceduren **anropas per batch**. Om du vill utföra en åtgärd som bara körs en gång och inte har något att göra med källdata, t. ex. ta bort/trunkera, använder du `sqlWriterCleanupScript` Property. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |
| sqlWriterTableType |Ange ett tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktivitet gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga data som kopieras med befintliga data. |Ett namn på en tabell typ. |Inga |

#### <a name="sqlsink-example"></a>SqlSink-exempel

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-exempel för att kopiera data till och från SQL Database
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Database och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Exempel: kopiera data från Azure SQL Database till Azure-Blob
Samma definierar följande Data Factory entiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar Time Series-data (varje timme, varje dag osv.) från en tabell i Azure SQL Database till en BLOB varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Azure SQL Database länkad tjänst:**

```JSON
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
I avsnittet om länkade Azure SQL-tjänster finns en lista över egenskaper som stöds av den här länkade tjänsten.

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
I artikeln om [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) finns en lista över egenskaper som stöds av den här länkade tjänsten.


**Data uppsättning för Azure SQL-indata:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i Azure SQL och innehåller en kolumn med namnet "timestampcolumn" för Time Series-data.

Inställningen "extern": "true" informerar den Azure Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
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

Se avsnittet typ egenskaper för Azure SQL-datamängd för listan över egenskaper som stöds av den här data uppsättnings typen.

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Se avsnittet [Egenskaper för Azure Blob-datamängd](data-factory-azure-blob-connector.md#dataset-properties) för listan över egenskaper som stöds av den här data uppsättnings typen.

**En kopierings aktivitet i en pipeline med SQL-källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som angetts för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
      }
    ]
  }
}
```
I exemplet anges **sqlReaderQuery** för SqlSource. Kopierings aktiviteten kör den här frågan mot Azure SQL Database källan för att hämta data. Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definierats i avsnittet struktur i JSON-datauppsättnings-JSON för att skapa en fråga som ska köras mot Azure SQL Database. Exempel: `select column1, column2 from mytable`. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

Se avsnittet [SQL-källa](#sqlsource) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSource och BlobSink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Exempel: kopiera data från Azure blob till Azure SQL Database
Exemplet definierar följande Data Factory entiteter:

1. En länkad tjänst av typen [AzureSqlDatabase](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#copy-activity-properties).

Exemplet kopierar Time Series-data (varje timme, varje dag osv.) från Azure blob till en tabell i Azure SQL Database varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure SQL-tjänst:**

```JSON
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
I avsnittet om länkade Azure SQL-tjänster finns en lista över egenskaper som stöds av den här länkade tjänsten.

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
I artikeln om [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) finns en lista över egenskaper som stöds av den här länkade tjänsten.


**Data uppsättning för Azure Blob-indata:**

Data hämtas från en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden och fil namnet använder Tim delen av start tiden. inställningen "extern": "true" informerar Data Factory tjänsten som den här tabellen är extern i data fabriken och produceras inte av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Se avsnittet [Egenskaper för Azure Blob-datamängd](data-factory-azure-blob-connector.md#dataset-properties) för listan över egenskaper som stöds av den här data uppsättnings typen.

**Azure SQL Database data uppsättning för utdata:**

Exemplet kopierar data till en tabell med namnet "min tabell" i Azure SQL. Skapa tabellen i Azure SQL med samma antal kolumner som du förväntar dig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Se avsnittet typ egenskaper för Azure SQL-datamängd för listan över egenskaper som stöds av den här data uppsättnings typen.

**En kopierings aktivitet i en pipeline med BLOB-källa och SQL-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
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
      }
    ]
  }
}
```
Se avsnittet [SQL Sink](#sqlsink) och [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSink och BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Identitets kolumner i mål databasen
Det här avsnittet innehåller ett exempel på hur du kopierar data från en käll tabell utan en identitets kolumn till en mål tabell med en identitets kolumn.

**Käll tabell:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Mål tabell:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Observera att mål tabellen innehåller en identitets kolumn.

**JSON-definition för käll data uppsättning**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**JSON-definition för mål data uppsättning**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Observera att eftersom käll-och mål tabellen har ett annat schema (målet har en ytterligare kolumn med identitet). I det här scenariot måste du ange en **struktur** egenskap i definitionen av mål data uppsättningen, som inte innehåller identitets kolumnen.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrad procedur från SQL-Sink
Ett exempel på att anropa en lagrad procedur från SQL-Sink i en kopierings aktivitet i en pipeline finns i artikeln [anropa lagrad procedur för SQL-mottagare i Kopiera aktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-azure-sql-database"></a>Typ mappning för Azure SQL Database
Som vi nämnt i artikel kopierings [aktiviteterna för data förflyttning](data-factory-data-movement-activities.md) utförs automatiska typ konverteringar från käll typer till mottagar typer med följande 2-stegs metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till och från Azure SQL Database används följande mappningar från SQL-typ till .NET-typ och vice versa. Mappningen är samma som SQL Server data typs mappning för ADO.NET.

| SQL Server typ av databas motor | .NET Framework typ |
| --- | --- |
| bigint |Int64 |
| binary |Byte [] |
| bit |Boolesk |
| char |Sträng, char [] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary (max)) |Byte [] |
| Float |Double |
| image |Byte [] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, char [] |
| ntext |Sträng, char [] |
| numeric |Decimal |
| nvarchar |Sträng, char [] |
| real |Enkel |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Jobbobjektet |
| text |Sträng, char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner i käll data uppsättningen till kolumner i data uppsättning för mottagare finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbar kopia
När du kopierar data till SQL Server databas lägger kopierings aktiviteten till data i tabellen mottagare som standard. Om du vill utföra en UPSERT i stället, se [upprepad skrivning till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) -artikeln.

När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.