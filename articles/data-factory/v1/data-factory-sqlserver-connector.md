---
title: Flytta data till och från SQL Server
description: Lär dig mer om hur du flyttar data till/från SQL Server databas som finns lokalt eller i en virtuell Azure-dator med hjälp av Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368820"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Flytta data till och från SQL Server med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sqlserver-connector.md)
> * [Version 2 (aktuell version)](../connector-sql-server.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [SQL Server Connector i v2](../connector-sql-server.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data till/från en SQL Server-databas. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en SQL Server-databas** till följande data lager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till en SQL Server-databas**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
Den här SQL Server Connector stöder kopiering av data från/till följande versioner av instansen lokalt eller i Azure-IaaS med hjälp av både SQL-autentisering och Windows-autentisering: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Aktivera anslutning
De begrepp och steg som krävs för att ansluta till SQL Server som finns lokalt eller i Azure IaaS-datorer (infrastruktur som en tjänst) är desamma. I båda fallen måste du använda Data Management Gateway för anslutning.

Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar om du vill lära dig mer om data Management Gateway och stegvisa anvisningar för hur du konfigurerar gatewayen. Att konfigurera en gateway-instans är ett krav för att ansluta till SQL Server.

Även om du kan installera gateway på samma lokala dator eller en virtuell dator i molnet som SQL Server för bättre prestanda rekommenderar vi att du installerar dem på separata datorer. Om du har en gateway och SQL Server på separata datorer minskar du resurs konkurrens.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en SQL Server-databas med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du t. ex. kopierar data från en SQL Server-databas till en Azure Blob Storage, skapar du två länkade tjänster för att länka din SQL Server-databas och Azure Storage-konto till data fabriken. För länkade tjänst egenskaper som är speciella för SQL Server Database, se avsnittet [länkade tjänst egenskaper](#linked-service-properties) .
3. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange SQL-tabellen i SQL Server databasen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange BLOB-behållaren och mappen som innehåller data som kopieras från SQL Server databasen. För data uppsättnings egenskaper som är speciella för SQL Server Database, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
4. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du SqlSource som källa och BlobSink som mottagare för kopierings aktiviteten. På samma sätt kan du använda BlobSource och SqlSink i kopierings aktiviteten om du kopierar från Azure Blob Storage till SQL Server databas. Information om kopierings aktiviteter som är speciell för SQL Server Database finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från en SQL Server databas finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-from-and-to-sql-server) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för SQL Server:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
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

### <a name="samples"></a>Exempel
**JSON för att använda SQL-autentisering**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON för att använda Windows-autentisering**

Data Management Gateway kommer att personifiera det angivna användar kontot för att ansluta till SQL Server-databasen.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
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

## <a name="dataset-properties"></a>Egenskaper för datamängd
I exemplen har du använt en data uppsättning av typen **SqlServerTable** för att representera en tabell i en SQL Server databas.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (SQL Server, Azure Blob, Azure Table osv.).

Avsnittet typeProperties är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet **typeProperties** för data uppsättningen av typen **SqlServerTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server databas instansen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
Om du flyttar data från en SQL Server databas anger du käll typen i kopierings aktiviteten till **SqlSource**. På samma sätt, om du flyttar data till en SQL Server databas, ställer du in mottagar typen i kopierings aktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, in-och utdata-tabeller och principer är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopierings aktiviteten tar bara en indata och producerar bara ett resultat.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

### <a name="sqlsource"></a>SqlSource
När källan i en kopierings aktivitet är av typen **SqlSource** finns följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: Välj * från tabellen tabell. Kan referera till flera tabeller från databasen som refereras av data uppsättningen. Om det inte anges används SQL-instruktionen som körs: Välj från tabellen. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från käll tabellen. |Namnet på den lagrade proceduren. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource kör kopierings aktiviteten den här frågan mot SQL Server databas källan för att hämta data.

Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger någon av sqlReaderQuery eller sqlReaderStoredProcedureName, används kolumnerna som definieras i avsnittet struktur för att skapa en urvals fråga som ska köras mot SQL Server databasen. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName** måste du fortfarande ange ett värde för egenskapen **TableName** i data uppsättnings-JSON. Det finns inga verifieringar som utförts i den här tabellen.

### <a name="sqlsink"></a>SqlSink
**SqlSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes. |tidsintervall<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för kopierings aktivitet som ska köras så att data i en angiven sektor rensas. Mer information finns i avsnittet [repeterbar kopiering](#repeatable-copy) . |Ett frågeuttryck. |Inga |
| sliceIdentifierColumnName |Ange kolumn namn för kopierings aktivitet som ska fyllas med automatiskt genererad sektor identifierare, som används för att rensa data i en speciell sektor när den körs igen. Mer information finns i avsnittet [repeterbar kopiering](#repeatable-copy) . |Kolumn namnet för en kolumn med data typen Binary (32). |Inga |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur käll data ska användas i mål tabellen, t. ex. för att göra upsertar eller transformera med din egen affärs logik. <br/><br/>Observera att den lagrade proceduren **anropas per batch**. Om du vill utföra en åtgärd som bara körs en gång och inte har något att göra med källdata, t. ex. ta bort/trunkera, använder du `sqlWriterCleanupScript` Property. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |
| sqlWriterTableType |Ange tabell typ namn som ska användas i den lagrade proceduren. Kopierings aktivitet gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga data som kopieras med befintliga data. |Ett namn på en tabell typ. |Inga |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-exempel för att kopiera data från och till SQL Server
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Följande exempel visar hur du kopierar data till och från SQL Server och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exempel: kopiera data från SQL Server till Azure-Blob
Följande exempel visar:

1. En länkad tjänst av typen [OnPremisesSqlServer](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopierings aktivitet som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar Time Series-data från en SQL Server tabell till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Det första steget är att konfigurera data Management Gateway. Anvisningarna finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

**SQL Server länkad tjänst**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Länkad Azure Blob Storage-tjänst**

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
**SQL Server indata-datauppsättning**

Exemplet förutsätter att du har skapat en tabell "Tabell" i SQL Server och den innehåller en kolumn med namnet "timestampcolumn" för Time Series-data. Du kan fråga över flera tabeller i samma databas med en enda data uppsättning, men en enda tabell måste användas för data uppsättningens tableName-typeProperty.

Inställningen "External": "true" informerar Data Factory tjänsten om att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

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
**Utdatauppsättning för Azure-blob**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
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
**Pipeline med kopierings aktivitet**

Pipelinen innehåller en kopierings aktivitet som är konfigurerad för att använda dessa data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som angetts för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
I det här exemplet har **sqlReaderQuery** angetts för SqlSource. Kopierings aktiviteten kör den här frågan mot SQL Server databas källan för att hämta data. Alternativt kan du ange en lagrad procedur genom att ange parametrarna **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av data uppsättningen. Den är inte begränsad till endast den tabell som angetts som data uppsättningens tableName-typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i avsnittet struktur för att skapa en urvals fråga som ska köras mot SQL Server databasen. Om data uppsättnings definitionen inte har strukturen, väljs alla kolumner från tabellen.

Se avsnittet [SQL-källa](#sqlsource) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) för listan över egenskaper som stöds av SqlSource och BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exempel: kopiera data från Azure blob till SQL Server
Följande exempel visar:

1. Den länkade tjänsten av typen [OnPremisesSqlServer](#linked-service-properties).
2. Den länkade tjänsten av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. [Pipelinen](data-factory-create-pipelines.md) med kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och SqlSink.

Exemplet kopierar Time Series-data från en Azure-blob till en SQL Server tabell varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**SQL Server länkad tjänst**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Länkad Azure Blob Storage-tjänst**

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
**Indatauppsättning för Azure-blob**

Data hämtas från en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden och fil namnet använder Tim delen av start tiden. inställningen "extern": "true" informerar Data Factory tjänsten om att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```json
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
**SQL Server data uppsättning för utdata**

Exemplet kopierar data till en tabell med namnet "min tabell" i SQL Server. Skapa tabellen i SQL Server med samma antal kolumner som du förväntar dig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Pipeline med kopierings aktivitet**

Pipelinen innehåller en kopierings aktivitet som är konfigurerad för att använda dessa data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Felsöka anslutningsproblem
1. Konfigurera SQL Server att acceptera fjärr anslutningar. Starta **SQL Server Management Studio**, högerklicka på **Server** och klicka på **Egenskaper**. Markera **anslutningar** i listan och markera **Tillåt fjärr anslutningar till servern**.

    ![Aktivera fjärranslutningar](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Detaljerade anvisningar finns i [Konfigurera konfigurations alternativet för fjärråtkomstservern](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) .
2. Starta **Konfigurationshanteraren för SQL Server**. Expandera **SQL Server nätverks konfiguration** för den instans du vill ha och välj **protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **Aktivera**.

    ![Aktivera TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Se [Aktivera eller inaktivera ett Server nätverks protokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) för information och alternativa sätt att aktivera TCP/IP-protokoll.
3. I samma fönster dubbelklickar du på **TCP/IP** för att starta fönstret **Egenskaper för TCP/IP** .
4. Växla till fliken **IP-adresser** . Rulla ned för att se avsnittet **IPAll** . Anteckna TCP- **porten**(standard är **1433**).
5. Skapa en **regel för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.
6. **Verifiera anslutning**: om du vill ansluta till SQL Server med ett fullständigt kvalificerat namn använder du SQL Server Management Studio från en annan dator. Exempel: " \<machine\> . \<domain\> . Corp. \<company\> . com, 1433. "

   > [!IMPORTANT]
   > 
   > Se [Flytta data mellan lokala källor och molnet med data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) för detaljerad information.
   > 
   > Mer information om fel sökning av problem med anslutning/Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="identity-columns-in-the-target-database"></a>Identitets kolumner i mål databasen
Det här avsnittet innehåller ett exempel som kopierar data från en käll tabell utan identitets kolumn till en mål tabell med en identitets kolumn.

**Käll tabell:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Mål tabell:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Observera att mål tabellen innehåller en identitets kolumn.

**JSON-definition för käll data uppsättning**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Se artikeln [anropa lagrad procedur för SQL-mottagare i kopierings aktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md) för ett exempel på att anropa en lagrad procedur från SQL-Sink i en kopierings aktivitet i en pipeline.

## <a name="type-mapping-for-sql-server"></a>Typ mappning för SQL Server
Som anges i artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) utför kopierings aktiviteten automatiska typ konverteringar från käll typer till mottagar typer med följande 2-steg-metod:

1. Konvertera från interna käll typer till .NET-typ
2. Konvertera från .NET-typ till typ av intern mottagare

När du flyttar data till & från SQL Server används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som SQL Server data typs mappning för ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Mappa källa till mottagar kolumner
Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbar kopia
När du kopierar data till SQL Server databas lägger kopierings aktiviteten till data i tabellen mottagare som standard. Om du vill utföra en UPSERT i stället, se [upprepad skrivning till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) -artikeln.

När du kopierar data från Relations data lager bör du ha repeterbarhet i åtanke för att undvika oönskade resultat. I Azure Data Factory kan du köra om ett segment manuellt. Du kan också konfigurera principer för återförsök för en data uppsättning så att en sektor körs igen när ett fel uppstår. När en sektor körs på annat sätt måste du se till att samma data är lästa oavsett hur många gånger en sektor körs. Se [repeterbar läsning från Relations källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.