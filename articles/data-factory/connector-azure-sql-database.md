---
title: Kopiera och transformera data i Azure SQL Database
description: Lär dig hur du kopierar data till och från Azure SQL Database och hur du omvandlar data i Azure SQL Database genom att använda Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/11/2021
ms.openlocfilehash: 07fbc7b1137d7eaf8a73a806c6a3714fab274df0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393113"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure SQL Database med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
>
> - [Version 1](v1/data-factory-azure-sql-connector.md)
> - [Aktuell version](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure SQL Database och använda data flödet för att transformera data i Azure SQL Database. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure SQL Database anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För kopierings aktiviteten stöder den här Azure SQL Database-anslutningen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token-autentisering med tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar data med hjälp av en SQL-fråga eller en lagrad procedur. Du kan också välja att parallellt kopiera från en Azure SQL Database källa, se avsnittet [parallell kopia från SQL Database](#parallel-copy-from-sql-database) för mer information.
- Som mottagare skapar mål tabellen automatiskt om den inte finns, baserat på käll schemat. lägga till data i en tabell eller anropa en lagrad procedur med anpassad logik under kopieringen.

Om du använder Azure SQL Database [nivån utan server](../azure-sql/database/serverless-tier-overview.md), Observera att när servern har pausats, Miss lyckas aktivitets körningen i stället för att vänta tills den automatiska återställningen är klar. Du kan lägga till ett nytt aktivitets försök eller kedja ytterligare aktiviteter för att se till att servern är aktiv vid den faktiska körningen.

>[!NOTE]
> Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) stöds inte av den här anslutningen nu. För att lösa problemet kan du använda en [allmän ODBC-anslutning](connector-odbc.md) och en SQL Server ODBC-drivrutin via en lokal integration Runtime. Lär dig mer från att [använda Always Encrypted](#using-always-encrypted) avsnittet. 

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure integration runtime konfigurerar du en [brand Väggs regel på server nivå](../azure-sql/database/firewall-configure.md) så att Azure-tjänsterna kan komma åt servern.
> Om du kopierar data med hjälp av en lokal integration runtime konfigurerar du brand väggen för att tillåta lämpligt IP-intervall. Detta intervall inkluderar datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Azure Data Factory entiteter som är speciella för en Azure SQL Database anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Dessa egenskaper stöds för en Azure SQL Database länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AzureSqlDatabase**. | Ja |
| Begär | Ange information som krävs för att ansluta till Azure SQL Database-instansen för egenskapen **ConnectionString** . <br/>Du kan också ange ett lösen ord eller en tjänst huvud nyckel i Azure Key Vault. Om det är SQL-autentisering, hämtar du `password` konfigurationen från anslutnings strängen. Mer information finns i JSON-exemplet som följer tabellen och [lagrar autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| tenant | Ange klient information, t. ex. domän namnet eller klient-ID: t, som ditt program finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten |
| azureCloudType | För autentisering av tjänstens huvud namn anger du vilken typ av Azure-moln miljö som Azure AD-programmet är registrerat för. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller** och **AzureGermany**. Som standard används data fabrikens moln miljö. | Inga |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) används för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. | Inga |

För olika typer av autentiseringar, se följande avsnitt om krav respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- [Azure AD Application token-autentisering: tjänstens huvud namn](#service-principal-authentication)
- [Azure AD Application token-autentisering: hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du träffar ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "databasens sessionsgräns är XXX och har nåtts," Lägg till `Pooling=false` i anslutnings strängen och försök igen.

### <a name="sql-authentication"></a>SQL-autentisering

**Exempel: använda SQL-autentisering**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lösen ord i Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda en tjänst objekts Azure AD-baserad autentisering av Azure AD-programtoken:

1. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) från Azure Portal. Anteckna program namnet och följande värden som definierar den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klient-ID:t

2. [Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) för servern på Azure Portal om du inte redan har gjort det. Azure AD-administratören måste vara en Azure AD-användare eller Azure AD-grupp, men den kan inte vara ett huvud namn för tjänsten. Det här steget görs så att du i nästa steg kan använda en Azure AD-identitet för att skapa en innesluten databas användare för tjänstens huvud namn.

3. [Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för tjänstens huvud namn. Anslut till databasen från eller till den databas som du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Ge tjänstens huvud namn nödvändiga behörigheter som du vanligt vis använder för SQL-användare eller andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurera en Azure SQL Database länkad tjänst i Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering av tjänstens huvud namn

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten för Azure SQL Database autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering.

1. [Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) för servern på Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören har fullständig åtkomst till databasen.

2. [Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till databasen från eller till den databas som du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Ge den Data Factory hanterade identiteten nödvändiga behörigheter som du vanligt vis använder för SQL-användare och andra. Kör följande kod. Fler alternativ finns i [det här dokumentet](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurera en Azure SQL Database länkad tjänst i Azure Data Factory.

**Exempel**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](./concepts-datasets-linked-services.md).

Följande egenskaper stöds för Azure SQL Database data uppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **AzureSqlTable**. | Ja |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej för källa, Ja för mottagare |

### <a name="dataset-properties-example"></a>Exempel på data uppsättnings egenskaper

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database källa och mottagare.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database som källa

>[!TIP]
>Om du vill läsa in data från Azure SQL Database effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från SQL Database](#parallel-copy-from-sql-database).

För att kopiera data från Azure SQL Database, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **AzureSqlSource**. "SqlSource"-typen stöds fortfarande för bakåtkompatibilitet. | Ja |
| sqlReaderQuery | Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. | Inga |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från käll tabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Inga |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-eller värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Inga |
| isolationLevel | Anger transaktions låsnings beteendet för SQL-källan. De tillåtna värdena är: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Om inget värde anges används databasens standard isolerings nivå. Mer information finns i [det här dokumentet](/dotnet/api/system.data.isolationlevel) . | Inga |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Azure SQL Database. <br>Tillåtna värden är: **ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att data ska läsas in från en Azure SQL Database av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när alternativet partition inte är det `None` . | Inga |
| ***Under `partitionSettings` :*** | | |
| partitionColumnName | Ange namnet på käll kolumnen **i Integer-eller date/datetime-typ** (,,,,,, `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` eller `datetimeoffset` ) som ska användas av intervall partitionering för parallell kopiering. Om detta inte anges identifieras indexet eller primär nyckeln för tabellen automatiskt och används som partition-kolumn.<br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook  `?AdfDynamicRangePartitionCondition ` i WHERE-satsen. Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.  <br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |
| partitionLowerBound | Det minsta värdet för partition-kolumnen för delning av partition intervall. Det här värdet används för att bestämma partitionens kliv, inte för att filtrera raderna i tabellen. Alla rader i tabellen eller frågeresultatet kommer att partitioneras och kopieras. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br>Använd när alternativet partition är `DynamicRange` . Ett exempel finns i avsnittet [parallell kopiering från SQL-databas](#parallel-copy-from-sql-database) . | Inga |

**Observera följande punkter:**

- Om **sqlReaderQuery** har angetts för **AzureSqlSource** kör kopierings aktiviteten den här frågan mot Azure SQL Database källan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** om den lagrade proceduren tar parametrar.
- När du använder lagrad procedur i källa för att hämta data, Tänk på om din lagrade procedur har utformats för att returnera ett annat schema när ett annat parameter värde skickas in, kan du stöta på fel eller Visa oväntat resultat när du importerar schema från användar gränssnittet eller när du kopierar data till SQL Database med automatisk tabell skapande.

#### <a name="sql-query-example"></a>Exempel på SQL-fråga

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Exempel på lagrad procedur

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>Definition av lagrad procedur

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database som mottagare

> [!TIP]
> Lär dig mer om Skriv beteenden, konfigurationer och bästa metoder som stöds av [bästa praxis för att läsa in data i Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

För att kopiera data till Azure SQL Database, stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **AzureSqlSink**. "SqlSink"-typen stöds fortfarande för bakåtkompatibilitet. | Ja |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till Azure SQL Database. Den anropas bara en gång per kopierings körning. Använd den här egenskapen för att rensa de förinstallerade data. | Inga |
| tableOption | Anger om [mottagar tabellen ska skapas automatiskt om den](copy-activity-overview.md#auto-create-sink-tables) inte finns, baserat på käll schemat. <br>Det går inte att skapa en automatisk tabell när mottagaren anger den lagrade proceduren. <br>Tillåtna värden är: `none` (standard), `autoCreate` . | Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska användas i en mål tabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och som inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du `preCopyScript` egenskapen.<br>Se exempel från [anropa en lagrad procedur från en SQL-mottagare](#invoke-a-stored-procedure-from-a-sql-sink). | Inga |
| storedProcedureTableTypeParameterName |Parameter namnet för den tabell typ som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Det tabell typs namn som ska användas i den lagrade proceduren. Kopierings aktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabell typen. Den lagrade procedur koden kan sedan sammanfoga de data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-och värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Inga |
| writeBatchSize | Antal rader som ska infogas i SQL-tabellen *per batch*.<br/> Det tillåtna värdet är **Integer** (antal rader). Som standard bestämmer Azure Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. | Inga |
| writeBatchTimeout | Vänte tiden för att infoga batch-operationen ska avslutas innan den nådde tids gränsen.<br/> Det tillåtna värdet är **TimeSpan**. Ett exempel är "00:30:00" (30 minuter). | Inga |
| disableMetricsCollection | Data Factory samlar in mått som Azure SQL Database DTU: er för att kopiera prestanda optimering och rekommendationer, vilket ger ytterligare åtkomst till Master DB. Om du är orolig för det här beteendet anger `true` du för att inaktivera det. | Nej (standard är `false` ) |

**Exempel 1: Lägg till data**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

Azure SQL Database koppling i kopierings aktivitet tillhandahåller inbyggd data partitionering för att kopiera data parallellt. Du kan hitta alternativ för data partitionering på fliken **källa** i kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-sql-server/connector-sql-partition-options.png)

När du aktiverar partitionerad kopiering kör kopierings aktiviteten parallella frågor mot din Azure SQL Database-källa för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från Azure SQL Database.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din Azure SQL Database. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell med fysiska partitioner.        | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. <br><br/>Om du vill kontrol lera om din tabell har fysisk partition eller inte, kan du referera till [den här frågan](#sample-query-to-check-physical-partition). |
| Fullständig belastning från stor tabell, utan fysiska partitioner, med en heltals-eller datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Partitionstabell** (valfritt): Ange den kolumn som används för att partitionera data. Om den inte anges används index eller primär nyckel kolumn.<br/>**Partitionens övre gränser** och **partitionens nedre gränser** (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera raderna i tabellen, och alla rader i tabellen kommer att partitioneras och kopieras. Om detta inte anges identifierar kopierings aktiviteten automatiskt värdena.<br><br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. |
| Läs in en stor mängd data med hjälp av en anpassad fråga utan fysiska partitioner, medan en heltals-eller datum-/datetime-kolumn för data partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data.<br>**Partitionens övre gränser** och **partitionens nedre gränser** (valfritt): Ange om du vill fastställa partitionens kliv. Detta är inte för att filtrera rader i tabellen, partitioneras och kopieras alla rader i frågeresultatet. Om inget värde anges identifierar kopierings aktiviteten automatiskt värdet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Azure SQL Database. <br>Om t. ex. partitionens kolumn "ID" har värden mellan 1 och 100 och du anger den nedre gränser som 20 och den övre gränser som 80, med parallell kopiering som 4, Data Factory hämtar data med 4 partitions-ID: n i intervallet <= 20, [21, 50], [51, 80] och >= 81. <br><br>Här är fler exempel frågor för olika scenarier:<br> 1. fråga hela tabellen: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. fråga från en tabell med kolumn val och ytterligare WHERE-sats filter: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. fråga med under frågor: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. fråga med partition i under fråga: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Metod tips för att läsa in data med partitions alternativ:

1. Välj en distinkt kolumn som partitionstabell (t. ex. primär nyckel eller unik nyckel) för att undvika data skevning. 
2. Om tabellen har en inbyggd partition använder du partition alternativ "fysiska partitioner i tabellen" för att få bättre prestanda.    
3. Om du använder Azure Integration Runtime för att kopiera data, kan du ställa in större "[data integrerings enheter (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) för att utnyttja mer data bearbetnings resurser. Kontrol lera tillämpliga scenarier där.
4. "[Graden av kopierings parallellitet](copy-activity-performance-features.md#parallel-copy)" styr partitionsnumret, vilket anger att det här antalet är för stort för att ont prestanda, rekommenderar att du anger det här antalet som (DIU eller antalet IR-noder med egen värd) * (2 till 4).

**Exempel: fullständig belastning från stor tabell med fysiska partitioner**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "AzureSqlSource",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Bästa praxis för att läsa in data i Azure SQL Database

När du kopierar data till Azure SQL Database kan du behöva olika Skriv beteenden:

- [Lägg till](#append-data): mina källdata har endast nya poster.
- [Upsert](#upsert-data): mina källdata har både infogningar och uppdateringar.
- [Skriv över](#overwrite-the-entire-table): Jag vill läsa in en hel dimensions tabell på nytt varje tillfälle.
- [Skriv med anpassad logik](#write-data-with-custom-logic): Jag behöver extra bearbetning före den slutliga infogningen i mål tabellen.

Se respektive avsnitt om hur du konfigurerar i Azure Data Factory och metod tips.

### <a name="append-data"></a>Lägg till data

Att lägga till data är standard beteendet för den här Azure SQL Database Sink-anslutningen. Azure Data Factory skapar en Mass infogning för att skriva till tabellen effektivt. Du kan konfigurera källan och mottagaren efter detta i kopierings aktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data som ska kopieras kan du samla in alla poster i en mellanlagringsplats med hjälp av kopierings aktiviteten och sedan köra en lagrad procedur aktivitet för att tillämpa en [merge](/sql/t-sql/statements/merge-transact-sql) -eller INSERT-Update-instruktion i en bild. 

Kopierings aktiviteten stöder för närvarande inte inläsning av data i en tillfällig databas tabell. Det finns ett avancerat sätt att konfigurera den med en kombination av flera aktiviteter, se [optimera Azure SQL Database upsert-scenarier](https://github.com/scoriani/azuresqlbulkupsert). Nedan visas ett exempel på hur du använder en permanent tabell som mellanlagring.

I Azure Data Factory kan du till exempel skapa en pipeline med en **kopierings aktivitet** som är länkad till en **lagrad procedur aktivitet**. Den tidigare kopierar data från käll arkivet till en Azure SQL Database-mellanlagringsdatabas, till exempel **UpsertStagingTable**, som tabell namnet i data uppsättningen. Sedan anropar den senare en lagrad procedur för att slå samman käll data från mellanlagringsplatsen till mål tabellen och rensa mellanlagrings tabellen.

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

**Alternativ 3:** Du kan använda [kart data flöde](#sink-transformation) som erbjuder inbyggda metoder för att infoga/upsert/uppdatera.

### <a name="overwrite-the-entire-table"></a>Skriv över hela tabellen

Du kan konfigurera **preCopyScript** -egenskapen i kopierings aktiviteten Sink. I det här fallet kör Azure Data Factory skriptet först för varje kopierings aktivitet som körs. Sedan kör den kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med den senaste informationen anger du ett skript för att först ta bort alla poster innan du läser in nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [upsert data](#upsert-data) . När du behöver tillämpa extra bearbetning före den slutliga infogningen av käll data i mål tabellen, kan du läsa in till en mellanlagringsplats och sedan anropa en lagrad procedur aktivitet, eller anropa en lagrad procedur i kopierings aktivitetens Sink för att tillämpa data eller använda data flödet för mappning.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till Azure SQL Database kan du också konfigurera och anropa en användardefinierad lagrad procedur med ytterligare parametrar i varje batch i käll tabellen. Funktionen för lagrade procedurer utnyttjar [tabell värdes parametrar](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Du kan använda en lagrad procedur när inbyggda kopierings metoder inte fungerar. Ett exempel är när du vill använda extra bearbetning före den slutliga infogningen av käll data i mål tabellen. Några extra bearbetnings exempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert till en tabell i Azure SQL Database. Anta att indata och **marknadsförings** tabellen för mottagare har tre kolumner: **profil**, **State** och **Category**. Gör upsert baserat på kolumnen **profil** och Använd den bara för en viss kategori som kallas "ProduktA".

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
        "type": "AzureSqlSink",
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

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till tabeller från Azure SQL Database. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden.

### <a name="source-transformation"></a>Käll omvandling

Inställningar som är aktuella för Azure SQL Database finns tillgängliga på fliken **käll alternativ** i käll omvandlingen.

**Inmatade:** Välj om du vill peka källan på en tabell (motsvarande ```Select * from <table-name>``` ) eller ange en anpassad SQL-fråga.

**Fråga**: om du väljer fråga i fältet inmatat anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen. **Order by** -satser stöds inte här, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell. Med den här frågan skapas en käll tabell som du kan använda i ditt data flöde. Att använda frågor är också ett bra sätt att minska antalet rader för testning eller sökning.

- SQL-exempel: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek**: Ange en batchstorlek för att segmentera stora data till läsningar.

**Isolerings nivå**: standardvärdet för SQL-källor i mappnings data flödet är Read uncommitted. Du kan ändra isolerings nivån här till något av följande värden:

- Läs bekräftad
- Läs ej allokerad
- Upprepnings bar läsning
- Serialiserbar
- Ingen (ignorera isolerings nivå)

![Isoleringsnivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är tillgängliga för Azure SQL Database finns på fliken **Inställningar** i omvandlingen för mottagare.

**Uppdaterings metod:** Avgör vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar. Om du vill uppdatera, upsert eller ta bort rader krävs en Alter-Row-omvandling för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

![Nyckel kolumner](media/data-flow/keycolumn.png "Nyckel kolumner")

Kolumn namnet som du väljer som nyckel här kommer att användas av ADF som en del av den efterföljande uppdateringen, upsert, Delete. Därför måste du välja en kolumn som finns i Sink-mappningen. Om du inte vill skriva värdet till den här nyckel kolumnen klickar du på "hoppa över skrivning av nyckel kolumner".

Du kan Parameterisera nyckel kolumnen som används här för att uppdatera mål Azure SQL Databases tabellen. Om du har flera kolumner för en sammansatt nyckel klickar du på "anpassat uttryck" och du kan lägga till dynamiskt innehåll med uttrycks språket ADF-dataflöde, som kan innehålla en sträng mat ris med kolumn namn för en sammansatt nyckel.

**Tabell åtgärd:** Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.

- Ingen: ingen åtgärd utförs i tabellen.
- Återskapa: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.
- Trunkera: alla rader från mål tabellen tas bort.

**Batchstorlek**: styr hur många rader som skrivs i varje Bucket. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras.

**Använd tempdb:** Som standard använder Data Factory en global temporär tabell för att lagra data som en del av inläsnings processen. Du kan också avmarkera alternativet "Använd TempDB" och be i stället Data Factory att lagra den tillfälliga tabellen i en användar databas som finns i den databas som används för den här mottagaren.

![Använd Temp DB](media/data-flow/tempdb.png "Använd Temp DB")

**För-och post-SQL-skript**: Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din mottagar databas

![skript för SQL-bearbetning före och efter bearbetning](media/data-flow/prepost1.png "Skript för SQL-bearbetning")

### <a name="error-row-handling"></a>Felhantering av poster

Vid skrivning till Azure SQL DB kan vissa rader med data Miss lyckas på grund av begränsningar som anges av målet. Några vanliga fel är:

*    Sträng data eller binära data skulle trunkeras i tabellen
*    Det går inte att infoga värdet NULL i kolumnen
*    INSERT-instruktionen var i konflikt med kontroll begränsningen

En data flödes körning fungerar som standard för det första fel som det får. Du kan välja att **fortsätta med ett fel** som gör att ditt data flöde kan slutföras även om enskilda rader har fel. Azure Data Factory innehåller olika alternativ för att hantera dessa fel rader.

**Transaktions genomförande:** Välj om dina data ska skrivas i en enskild transaktion eller i batchar. En enskild transaktion ger sämre prestanda men inga data som skrivs visas för andra förrän transaktionen har slutförts.  

**Utdata avvisade data:** Om aktive rad kan du mata ut fel rader till en CSV-fil i Azure Blob Storage eller ett Azure Data Lake Storage Gen2 konto som du väljer. Detta skriver fel raderna med tre ytterligare kolumner: SQL-åtgärden som INSERT eller UPDATE, data flödets felkod och fel meddelandet på raden.

**Rapporten lyckades vid fel:** Om det här alternativet aktive ras markeras data flödet som slutfört även om fel rader hittas. 

![Felhantering av poster](media/data-flow/sql-error-row-handling.png "Felhantering av poster")


## <a name="data-type-mapping-for-azure-sql-database"></a>Data typs mappning för Azure SQL Database

När data kopieras från eller till Azure SQL Database, används följande mappningar från Azure SQL Database data typer för att Azure Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database data typ | Azure Data Factory data typen Interim |
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
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte [] |
| varchar |Sträng, char [] |
| xml |Sträng |

>[!NOTE]
> För data typer som mappar till typen decimal, stöder för tillfället kopierings aktiviteten precisionen upp till 28. Om du har data med en precision som är större än 28 bör du överväga att konvertera till en sträng i SQL-frågan.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Använda Always Encrypted

När du kopierar data från/till Azure SQL Database med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)använder du [allmän ODBC-anslutning](connector-odbc.md) och SQL Server ODBC-drivrutin via egen värd integration Runtime. Den här Azure SQL Database-anslutningen har inte stöd för Always Encrypted nu. 

Mer specifikt:

1. Konfigurera en egen värd Integration Runtime om du inte har någon. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .

2. Hämta 64-bitars ODBC-drivrutinen för [SQL Server härifrån och](/sql/connect/odbc/download-odbc-driver-for-sql-server)installera på den integration runtime datorn. Läs mer om hur driv rutinen fungerar när [du använder Always Encrypted med ODBC-drivrutinen för SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Skapa länkad tjänst med ODBC-typ för att ansluta till din SQL-databas, se följande exempel:

    - Använda **SQL-autentisering**: Ange ODBC-anslutningssträngen enligt nedan och välj **grundläggande** autentisering för att ange användar namn och lösen ord.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Använda **Data Factory hanterad identitets autentisering**: 

        1. Följ samma [krav](#managed-identity) för att skapa databas användare för den hanterade identiteten och ge rätt roll i databasen.
        2. I länkad tjänst anger du ODBC-anslutningssträngen enligt nedan och väljer **Anonym** autentisering som själva anslutnings strängen anger `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Skapa data uppsättning och kopiera aktivitet med ODBC-typ enligt detta. Läs mer från artikeln om [ODBC-koppling](connector-odbc.md) .

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).