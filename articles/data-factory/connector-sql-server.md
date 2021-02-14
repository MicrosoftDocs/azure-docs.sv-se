---
title: Kopiera data till och från SQL Server
description: Lär dig mer om hur du flyttar data till och från SQL Server databas som finns lokalt eller i en virtuell Azure-dator med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/18/2020
ms.openlocfilehash: 5d35d0434f65f28b58a6d81172ade12aceb02987
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385004"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Kopiera data till och från SQL Server med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-sqlserver-connector.md)
> * [Aktuell version](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till en SQL Server-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SQL Server anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

Du kan kopiera data från en SQL Server-databas till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till en SQL Server databas. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här SQL Server-anslutningen:

- SQL Server version 2005 och senare.
- Kopiera data med SQL eller Windows-autentisering.
- Som källa hämtar data med hjälp av en SQL-fråga eller en lagrad procedur. Du kan också välja att parallellt kopiera från SQL Server källa, se avsnittet [parallell kopia från SQL Database](#parallel-copy-from-sql-database) för mer information.
- Som mottagare skapar mål tabellen automatiskt om den inte finns, baserat på käll schemat. lägga till data i en tabell eller anropa en lagrad procedur med anpassad logik under kopieringen. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) stöds inte.

>[!NOTE]
>SQL Server [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) stöds inte av den här anslutningen nu. För att lösa problemet kan du använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin. Följ [den här vägledningen](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) för hämtning av ODBC-drivrutiner och konfiguration av anslutnings strängar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SQL Server Database Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten SQL Server:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **SQLServer**. | Ja |
| Begär |Ange **ConnectionString** -information som behövs för att ansluta till SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. Se följande exempel.<br/>Du kan också ange ett lösen ord i Azure Key Vault. Om det är SQL-autentisering, hämtar du `password` konfigurationen från anslutnings strängen. Mer information finns i JSON-exemplet som följer tabellen och [lagrar autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| userName |Ange ett användar namn om du använder Windows-autentisering. Ett exempel är **domän \\ namn användar namn**. |Inga |
| password |Ange ett lösen ord för det användar konto som du har angett som användar namn. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory. Eller så kan du [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). |Inga |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) används för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure integration Runtime. |Inga |

>[!TIP]
>Om du träffar ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "databasens sessionsgräns är XXX och har nåtts," Lägg till `Pooling=false` i anslutnings strängen och försök igen.

**Exempel 1: Använd SQL-autentisering**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Använd SQL-autentisering med ett lösen ord i Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 3: Använd Windows-autentisering**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server data uppsättningen.

Följande egenskaper stöds för att kopiera data från och till en SQL Server databas:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **SqlServerTable**. | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej för källa, Ja för mottagare |

**Exempel**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som kan användas för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SQL Server källa och mottagare.

### <a name="sql-server-as-a-source"></a>SQL Server som källa

>[!TIP]
>Om du vill läsa in data från SQL Server effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från SQL Database](#parallel-copy-from-sql-database).

Om du vill kopiera data från SQL Server anger du käll typen i kopierings aktiviteten till **SqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets Källa:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **SqlSource**. | Ja |
| sqlReaderQuery |Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Den här egenskapen är namnet på den lagrade procedur som läser data från käll tabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Inga |
| storedProcedureParameters |De här parametrarna är för den lagrade proceduren.<br/>Tillåtna värden är namn-eller värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. |Inga |
| isolationLevel | Anger transaktions låsnings beteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Om inget värde anges används databasens standard isolerings nivå. Mer information finns i [det här dokumentet](/dotnet/api/system.data.isolationlevel) . | Inga |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från SQL Server. <br>Tillåtna värden är: **ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att samtidigt läsa in data från SQL Server av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte är det `None` . | Inga |
| ***Under `partitionSettings` :*** | | |
| partitionColumnName | Ange namnet på käll kolumnen **i Integer-eller date/datetime-typ** (,,,,,, `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` eller `datetimeoffset` ) som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras indexet eller primär nyckeln för tabellen automatiskt och används som partition-kolumn.<br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook  `?AdfDynamicRangePartitionCondition ` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.  <br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |
| partitionLowerBound | Det minsta värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |

**Observera följande punkter:**

- Om **sqlReaderQuery** har angetts för **SqlSource** kör kopierings aktiviteten den här frågan mot SQL Server källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- När du använder lagrad procedur i källa för att hämta data, Tänk på om din lagrade procedur har utformats för att returnera ett annat schema när ett annat parameter värde skickas in, kan du stöta på fel eller Visa oväntat resultat när du importerar schema från användar gränssnittet eller när du kopierar data till SQL Database med automatisk tabell skapande.

**Exempel: Använd SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel: använda en lagrad procedur**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definitionen av den lagrade proceduren**

```sql
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

### <a name="sql-server-as-a-sink"></a>SQL Server som mottagare

> [!TIP]
> Lär dig mer om Skriv beteenden, konfigurationer och bästa metoder som stöds av [bästa praxis för att läsa in data i SQL Server](#best-practice-for-loading-data-into-sql-server).

Om du vill kopiera data till SQL Server anger du mottagar typen i kopierings aktiviteten till **SqlSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets mottagare:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **SqlSink**. | Ja |
| preCopyScript |Den här egenskapen anger en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till SQL Server. Den anropas bara en gång per kopierings körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. |Inga |
| tableOption | Anger om [mottagar tabellen ska skapas automatiskt om den](copy-activity-overview.md#auto-create-sink-tables) inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när mottagaren anger den lagrade proceduren. Tillåtna värden är: `none` (standard), `autoCreate` . |Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en mål tabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och som inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du `preCopyScript` egenskapen.<br>Se exempel från [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink). | Inga |
| storedProcedureTableTypeParameterName |Parameter namnet för den tabell typ som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Det tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga de data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-och värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Inga |
| writeBatchSize |Antal rader som ska infogas i SQL-tabellen *per batch*.<br/>Tillåtna värden är heltal för antalet rader. Som standard bestämmer Azure Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. |Inga |
| writeBatchTimeout |Den här egenskapen anger vänte tiden för åtgärden Infoga som ska slutföras innan tids gränsen uppnås.<br/>Tillåtna värden är för TimeSpan. Ett exempel är "00:30:00" i 30 minuter. Om inget värde anges används standardvärdet "02:00:00" för timeout. |Inga |

**Exempel 1: Lägg till data**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Exempel 2: anropa en lagrad procedur under kopiering**

Läs mer om att [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Parallell kopiering från SQL Database

SQL Server koppling i kopierings aktivitet tillhandahåller inbyggd data partitionering för att kopiera data parallellt. Du kan hitta alternativ för data partitionering på fliken **källa** i kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-sql-server/connector-sql-partition-options.png)

När du aktiverar partitionerad kopiering kör kopierings aktiviteten parallella frågor mot din SQL Server-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från SQL Server.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din SQL Server. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell med fysiska partitioner.        | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. <br><br/>Om du vill kontrol lera om din tabell har fysisk partition eller inte, kan du referera till [den här frågan](#sample-query-to-check-physical-partition). |
| Fullständig belastning från stor tabell, utan fysiska partitioner, med en heltals-eller datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Partitionstabell** (valfritt): Ange den kolumn som används för att partitionera data. Om den inte anges används index eller primär nyckel kolumn.<br/>**Partitionens övre gränser** och **partitionens nedre gränser** (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera raderna i tabellen, och alla rader i tabellen kommer att partitioneras och kopieras. Om detta inte anges identifierar kopierings aktiviteten automatiskt värdena.<br><br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. |
| Läs in en stor mängd data med hjälp av en anpassad fråga utan fysiska partitioner, medan en heltals-eller datum-/datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data.<br>**Partitionens övre gränser** och **partitionens nedre gränser** (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera rader i tabellen, partitioneras och kopieras alla rader i frågeresultatet. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till SQL Server. <br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. <br><br>Här är fler exempel frågor för olika scenarier:<br> 1. fråga hela tabellen: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. fråga från en tabell med kolumn val och ytterligare WHERE-sats filter: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. fråga med under frågor: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. fråga med partition i under fråga: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Metod tips för att läsa in data med partitions alternativ:

1. Välj en distinkt kolumn som partitionstabell (t. ex. primär nyckel eller unik nyckel) för att undvika data skevning. 
2. Om tabellen har en inbyggd partition använder du partition alternativ "fysiska partitioner i tabellen" för att få bättre prestanda.    
3. Om du använder Azure Integration Runtime för att kopiera data, kan du ställa in större "[data integrerings enheter (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) för att utnyttja mer data bearbetnings resurser. Kontrol lera tillämpliga scenarier där.
4. "[Graden av kopierings parallellitet](copy-activity-performance-features.md#parallel-copy)" styr partitionsnumret, vilket anger att det här antalet är för stort för att ont prestanda, rekommenderar att du anger det här antalet som (DIU eller antalet IR-noder med egen värd) * (2 till 4).

**Exempel: fullständig belastning från stor tabell med fysiska partitioner**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Exempel fråga för att kontrol lera den fysiska partitionen

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Om tabellen har fysisk partition visas "HasPartition" som "Ja" som följande.

![SQL-frågeresultat](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Bästa praxis för att läsa in data i SQL Server

När du kopierar data till SQL Server kan du behöva olika Skriv beteenden:

- [Lägg till](#append-data): mina källdata har endast nya poster.
- [Upsert](#upsert-data): mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill läsa in hela dimensions tabellen på nytt varje tillfälle.
- [Skriv med anpassad logik](#write-data-with-custom-logic): Jag behöver extra bearbetning före den slutliga infogningen i mål tabellen.

Se respektive avsnitt för hur du konfigurerar i Azure Data Factory och metod tips.

### <a name="append-data"></a>Lägg till data

Att lägga till data är standard beteendet för den här SQL Server Sink-anslutningen. Azure Data Factory skapar en Mass infogning för att skriva till tabellen effektivt. Du kan konfigurera källan och mottagaren efter detta i kopierings aktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data som ska kopieras kan du samla in alla poster i en mellanlagringsplats med hjälp av kopierings aktiviteten och sedan köra en lagrad procedur aktivitet för att tillämpa en [merge](/sql/t-sql/statements/merge-transact-sql) -eller INSERT-Update-instruktion i en bild. 

Kopierings aktiviteten stöder för närvarande inte inläsning av data i en tillfällig databas tabell. Det finns ett avancerat sätt att konfigurera den med en kombination av flera aktiviteter, se [optimera SQL Database upsert-scenarier](https://github.com/scoriani/azuresqlbulkupsert). Nedan visas ett exempel på hur du använder en permanent tabell som mellanlagring.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopierings aktivitet** som är länkad till en **lagrad procedur aktivitet**. Den tidigare kopierar data från käll arkivet till en SQL Server-mellanlagringsdatabas, till exempel **UpsertStagingTable**, som tabell namnet i data uppsättningen. Sedan anropar den senare en lagrad procedur för att slå samman käll data från mellanlagringsplatsen till mål tabellen och rensa mellanlagrings tabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med SAMMANSLAGNINGs logik, som i följande exempel, som pekas från föregående lagrade procedur aktivitet. Anta att målet är **marknadsförings** tabellen med tre kolumner: **profil**, **State** och **Category**. Gör upsert baserat på kolumnen **profil** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Alternativ 2:** Du kan välja att [anropa en lagrad procedur i kopierings aktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden kör varje batch (som styrs av `writeBatchSize` egenskapen) i käll tabellen i stället för att använda Mass infogning som standard metod i kopierings aktiviteten.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i en kopierings aktivitets mottagare. I det här fallet kör Azure Data Factory skriptet först för varje kopierings aktivitet som körs. Sedan kör den kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen anger du ett skript för att först ta bort alla poster innan du läser in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [upsert data](#upsert-data) . När du behöver tillämpa extra bearbetning före den slutliga infogningen av källdata i mål tabellen, kan du läsa in till en mellanlagringsplats och sedan anropa aktiviteten för lagrad procedur, eller anropa en lagrad procedur i kopierings aktivitets Sink för att tillämpa data.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till SQL Server Database kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar i varje batch i käll tabellen. Funktionen för lagrade procedurer utnyttjar [tabell värdes parametrar](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Du kan använda en lagrad procedur när inbyggda kopierings metoder inte fungerar. Ett exempel är när du vill använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen. Några extra bearbetnings exempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i den SQL Server databasen. Anta att indata och **marknadsförings** tabellen för mottagare har tre kolumner: **profil**, **State** och **Category**. Gör upsert baserat på kolumnen **profil** och Använd den bara för en viss kategori som kallas "ProduktA".

1. I databasen definierar du tabell typen med samma namn som **sqlWriterTableType**. Schemat för tabell typen är detsamma som det schema som returnerades av indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från din angivna källa och sammanfogar dem i utdatatabellen. Parameter namnet för tabell typen i den lagrade proceduren är samma som **TableName** som definierats i data uppsättningen.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. I Azure Data Factory definierar du avsnittet **SQL-mottagare** i kopierings aktiviteten enligt följande:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>Data typs mappning för SQL Server

När du kopierar data från och till SQL Server, används följande mappningar från SQL Server data typer för att Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| SQL Server data typ | Azure Data Factory data typen Interim |
|:--- |:--- |
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
| sql_variant |Objekt |
| text |Sträng, char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Sträng |

>[!NOTE]
> För data typer som mappar till typen decimal, stöder för tillfället kopierings aktiviteten precisionen upp till 28. Om du har data som kräver precision som är större än 28, bör du överväga att konvertera till en sträng i en SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Använda Always Encrypted

När du kopierar data från/till SQL Server med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)använder du [allmän ODBC-anslutning](connector-odbc.md) och SQL Server ODBC-drivrutin via egen värd integration Runtime. Den här SQL Server-anslutningen har inte stöd för Always Encrypted nu. 

Mer specifikt:

1. Konfigurera en egen värd Integration Runtime om du inte har någon. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .

2. Hämta 64-bitars ODBC-drivrutinen för [SQL Server härifrån och](/sql/connect/odbc/download-odbc-driver-for-sql-server)installera på den integration runtime datorn. Läs mer om hur driv rutinen fungerar när [du använder Always Encrypted med ODBC-drivrutinen för SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Skapa länkad tjänst med ODBC-typ för att ansluta till din SQL-databas. Om du vill använda SQL-autentisering anger du ODBC-anslutningssträngen enligt nedan och väljer **grundläggande** autentisering för att ange användar namn och lösen ord.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Skapa data uppsättning och kopiera aktivitet med ODBC-typ enligt detta. Läs mer från artikeln om [ODBC-koppling](connector-odbc.md) .

## <a name="troubleshoot-connection-issues"></a>Felsöka anslutningsproblem

1. Konfigurera SQL Server-instansen så att den accepterar fjärr anslutningar. Starta **SQL Server Management Studio**, högerklicka på **Server** och välj **Egenskaper**. Välj **anslutningar** i listan och markera kryss rutan **Tillåt fjärr anslutningar till den här servern** .

    ![Aktivera fjärranslutningar](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Detaljerade anvisningar finns i [Konfigurera konfigurations alternativet för fjärråtkomstservern](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. Starta **Konfigurationshanteraren för SQL Server**. Expandera **SQL Server nätverks konfiguration** för den instans du vill ha och välj **protokoll för MSSQLSERVER**. Protokollen visas i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och välja **Aktivera**.

    ![Aktivera TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Mer information och alternativa sätt att aktivera TCP/IP-protokollet finns i [Aktivera eller inaktivera ett Server nätverks protokoll](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. I samma fönster dubbelklickar du på **TCP/IP** för att starta fönstret **Egenskaper för TCP/IP** .
4. Växla till fliken **IP-adresser** . Rulla nedåt för att se avsnittet **IPAll** . Skriv ner **TCP-porten**. Standardvärdet är **1433**.
5. Skapa en **regel för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten. 
6. **Verifiera anslutning**: om du vill ansluta till SQL Server med ett fullständigt kvalificerat namn använder du SQL Server Management Studio från en annan dator. Ett exempel är `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).