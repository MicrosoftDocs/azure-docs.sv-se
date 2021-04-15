---
title: Kopiera och transformera data i Azure SQL Database
description: Lär dig hur du kopierar data till och från Azure SQL Database och transformerar data i Azure SQL Database med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365237"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure SQL Database med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version Azure Data Factory som du använder:"]
>
> - [Version 1](v1/data-factory-azure-sql-connector.md)
> - [Aktuell version](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Azure SQL Database och använder Dataflöde för att transformera data i Azure SQL Database. Mer information Azure Data Factory finns i [introduktionsartikeln](introduction.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den Azure SQL Database anslutningsappen stöds för följande aktiviteter:

- [aktiviteten Kopiera med](copy-activity-overview.md) [matristabell för källa/mottagare som](copy-activity-overview.md) stöds
- [Mappa dataflöde](concepts-data-flow-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För aktiviteten Kopiera stöder den Azure SQL Database anslutningsappen dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory-autentisering (Azure AD) programtoken med ett huvudnamn för tjänsten eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller en lagrad procedur. Du kan också välja att parallellkopia från en Azure SQL Database datakälla. Mer information finns i avsnittet [Parallellkopiering](#parallel-copy-from-sql-database) från SQL Database.
- Som mottagare skapar du automatiskt måltabellen om den inte finns baserat på källschemat. lägga till data i en tabell eller anrop av en lagrad procedur med anpassad logik under kopieringen.

Om du använder Azure SQL Database [serverlös nivå](../azure-sql/database/serverless-tier-overview.md)bör du observera att när servern är pausad misslyckas aktivitetskörningen i stället för att vänta på att det automatiska återupptar-programmet är klart. Du kan lägga till återförsök för aktivitet eller länka ytterligare aktiviteter för att se till att servern är aktiv vid den faktiska körningen.

>[!NOTE]
> Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) stöds inte av den här anslutningsappen nu. Du kan komma runt detta genom att använda en allmän [ODBC-anslutningsapp](connector-odbc.md) och en odbc-SQL Server ODBC-drivrutin via en integrationskörning med egen värd. Läs mer i [avsnittet Använda Always Encrypted.](#using-always-encrypted) 

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Integration Runtime konfigurerar du en brandväggsregel på servernivå så att [Azure-tjänster](../azure-sql/database/firewall-configure.md) kan komma åt servern.
> Om du kopierar data med hjälp av en integrationskörning med egen värd konfigurerar du brandväggen så att den tillåter lämpligt IP-intervall. Det här intervallet omfattar datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Azure Data Factory entiteter som är specifika för Azure SQL Database anslutningsappen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Dessa egenskaper stöds för en Azure SQL Database länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureSqlDatabase**.  | Ja |
| Connectionstring | Ange information som behövs för att ansluta Azure SQL Database instansen för **egenskapen connectionString.** <br/>Du kan också ange ett lösenord eller en nyckel för tjänstens huvudnamn i Azure Key Vault. Om det är SQL-autentisering hämtar du `password` konfigurationen från anslutningssträngen. Mer information finns i JSON-exemplet efter tabellen och Lagra [autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som **SecureString för** att lagra det säkert i Azure Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten |
| tenant | Ange information om klientorganisationen, t.ex. domännamnet eller klientorganisations-ID:t, under vilket programmet finns. Hämta den genom att hovra med musen i det övre högra hörnet Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten |
| azureCloudType | För autentisering av tjänstens huvudnamn anger du vilken typ av Azure-molnmiljö som ditt Azure AD-program är registrerat i. <br/> Tillåtna värden **är AzurePublic,** **AzureChina,** **AzureUsGovernment** och **AzureGermany.** Som standard används datafabrikens molnmiljö. | Inga |
| connectVia | Den [här Integreringskörningen](concepts-integration-runtime.md) används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en integration runtime med egen värd om ditt datalager finns i ett privat nätverk. Om inget anges används standardinställningen för Azure Integration Runtime. | Inga |

Olika autentiseringstyper finns i följande avsnitt om krav och JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- [Autentisering av Azure AD-programtoken: Tjänstens huvudnamn](#service-principal-authentication)
- [Autentisering av Azure AD-programtoken: Hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du får ett fel med felkoden "UserErrorFailedToConnectToSqlServer" och ett meddelande som "Sessionsgränsen för databasen är XXX och har nåtts" lägger du till i anslutningssträngen och `Pooling=false` försöker igen. `Pooling=false` rekommenderas också för **SHIR(self hosted Integration Runtime) typ** av länkad tjänstkonfiguration. Poolning och andra anslutningsparametrar kan läggas till som nya parameternamn och värden i **avsnittet Ytterligare anslutningsegenskaper** i formuläret för skapande av länkad tjänst.

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

**Exempel: lösenord i Azure Key Vault**

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

Följ dessa steg om du vill använda en tjänsthuvudnamnsbaserad autentisering av Azure AD-programtoken:

1. [Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) från Azure Portal. Anteckna programnamnet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Programnyckel
    - Klient-ID:t

2. [Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) för servern på Azure Portal om du inte redan har gjort det. Azure AD-administratören måste vara en Azure AD-användare eller Azure AD-grupp, men det kan inte vara ett huvudnamn för tjänsten. Det här steget görs så att du i nästa steg kan använda en Azure AD-identitet för att skapa en innesluten databasanvändare för tjänstens huvudnamn.

3. [Skapa inneslutna databasanvändare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för tjänstens huvudnamn. Anslut till databasen från eller till vilken du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som har minst behörigheten ALTER ANY USER. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Ge tjänstens huvudnamn nödvändiga behörigheter som du normalt gör för SQL-användare eller andra. Kör följande kod. Fler alternativ finns i det [här dokumentet](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurera en Azure SQL Database länkad tjänst i Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering med tjänstens huvudnamn

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Hanterade identiteter för azure-resursautentisering

En datafabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den specifika datafabriken. Du kan använda den här hanterade identiteten för Azure SQL Database autentisering. Den avsedda fabriken kan komma åt och kopiera data från eller till din databas med hjälp av den här identiteten.

Följ dessa steg om du vill använda autentisering med hanterad identitet.

1. [Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) för servern på Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. Om du beviljar gruppen med en hanterad identitet en administratörsroll hoppar du över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

2. [Skapa inneslutna databasanvändare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) för den Azure Data Factory hanterade identiteten. Anslut till databasen från eller till vilken du vill kopiera data med hjälp av verktyg som SQL Server Management Studio, med en Azure AD-identitet som minst har behörigheten ALTER ANY USER. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Bevilja den Data Factory hanterade identiteten behörigheter som du normalt gör för SQL-användare och andra. Kör följande kod. Fler alternativ finns i det [här dokumentet.](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns [i Datauppsättningar](./concepts-datasets-linked-services.md).

Följande egenskaper stöds för Azure SQL Database datauppsättningen:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för datauppsättningen måste anges **till AzureSqlTable**.  | Ja |
| schema | Namnet på schemat. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. För ny arbetsbelastning använder du `schema` och `table` . | Nej för källa, Ja för mottagare |

### <a name="dataset-properties-example"></a>Exempel på datamängdsegenskaper

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Database källa och mottagare.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database som källa

>[!TIP]
>Om du vill läsa in data Azure SQL Database data effektivt med hjälp av datapartitionering kan du läsa mer från [Parallellkopiering från SQL Database](#parallel-copy-from-sql-database).

Följande egenskaper stöds i avsnittet kopieringsaktivitetskälla för att kopiera data från Azure SQL Database **datakällor:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopieringsaktivitetskällan måste anges till **AzureSqlSource**.  "SqlSource"-typen stöds fortfarande för bakåtkompatibilitet. | Ja |
| sqlReaderQuery | Den här egenskapen använder den anpassade SQL-frågan för att läsa data. Ett exempel är `select * from MyTable`. | Inga |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Inga |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- eller värdepar. Namn och hölje för parametrar måste matcha namnen och höljet för parametrarna för den lagrade proceduren. | Inga |
| isolationLevel | Anger beteendet för transaktionslåsning för SQL-källan. De tillåtna värdena är: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead,** **Serializable**, **Snapshot**. Om inget anges används databasens standardisoleringsnivå. Mer information [finns i](/dotnet/api/system.data.isolationlevel) det här dokumentet. | Inga |
| partitionOptions | Anger de alternativ för datapartitionering som används för att läsa in data från Azure SQL Database. <br>Tillåtna värden är: **Ingen** (standard), **PhysicalPartitionsOfTable** och **DynamicRange**.<br>När ett partitionsalternativ är aktiverat (det vill säga inte ), styrs graden av parallellitet för samtidig inläsning av data från en Azure SQL Database av inställningen för `None` [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopieringsaktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för datapartitionering. <br>Tillämpa när partitionsalternativet inte är `None` . | Inga |
| ***Under `partitionSettings` :*** | | |
| partitionColumnName | Ange namnet på källkolumnen i heltal eller **date/datetime-typ** ( , , , , , , , eller ) som ska användas av intervallpartitionering `int` `smallint` för `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` parallellkopiering. Om inget anges identifieras tabellens index eller primärnyckel automatiskt och används som partitionskolumn.<br>Tillämpa när partitionsalternativet är `DynamicRange` . Om du använder en fråga för att hämta källdata ansluter du till  `?AdfDynamicRangePartitionCondition ` WHERE-satsen. Ett exempel finns i avsnittet [Parallellkopiering från SQL Database.](#parallel-copy-from-sql-database) | Inga |
| partitionUpperBound | Det maximala värdet för partitionskolumnen för delning av partitionsintervall. Det här värdet används för att bestämma partitions stride, inte för filtrering av raderna i tabellen. Alla rader i tabellen eller frågeresultatet partitioneras och kopieras. Om inget värde anges identifierar kopieringsaktiviteten värdet automatiskt.  <br>Använd när partitionsalternativet är `DynamicRange` . Ett exempel finns i avsnittet [Parallellkopiering från SQL Database.](#parallel-copy-from-sql-database) | Inga |
| partitionLowerBound | Det minsta värdet för partitionskolumnen för delning av partitionsintervall. Det här värdet används för att bestämma partitions stride, inte för filtrering av raderna i tabellen. Alla rader i tabellen eller frågeresultatet partitioneras och kopieras. Om inget värde anges identifierar kopieringsaktiviteten värdet automatiskt.<br>Använd när partitionsalternativet är `DynamicRange` . Ett exempel finns i avsnittet [Parallellkopiering från SQL Database.](#parallel-copy-from-sql-database) | Inga |

**Observera följande punkter:**

- Om **sqlReaderQuery** har angetts för **AzureSqlSource** kör kopieringsaktiviteten den här frågan mot Azure SQL Database för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters om** den lagrade proceduren tar parametrar.
- När du använder lagrad procedur i källan för att hämta data bör du observera att om den lagrade proceduren har utformats för att returnera ett annat schema när ett annat parametervärde skickas, kan det uppstå fel eller oväntade resultat när du importerar schemat från användargränssnittet eller när du kopierar data till SQL-databasen med automatisk tabellgenerering.

#### <a name="sql-query-example"></a>EXEMPEL på SQL-fråga

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
> Läs mer om skrivbeteenden, konfigurationer och metodtips som stöds i [Metodtips](#best-practice-for-loading-data-into-azure-sql-database)för att läsa in data i Azure SQL Database .

För att kopiera data Azure SQL Database stöds följande egenskaper i avsnittet för **kopieringsaktivitetens mottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **type** för kopieringsaktivitetens mottagare måste anges till **AzureSqlSink**. "SqlSink"-typen stöds fortfarande för bakåtkompatibilitet. | Ja |
| preCopyScript | Ange en SQL-fråga för kopieringsaktiviteten som ska köras innan data skrivs Azure SQL Database. Den anropas bara en gång per kopieringskörning. Använd den här egenskapen för att rensa förinstallerade data. | Inga |
| tableOption | Anger om du vill [skapa tabellen för mottagare automatiskt](copy-activity-overview.md#auto-create-sink-tables) om den inte finns baserat på källschemat. <br>Automatisk tabellskapande stöds inte när mottagaren anger lagrad procedur. <br>Tillåtna värden är: `none` (standard), `autoCreate` . | Inga |
| sqlWriterStoredProcedureName | Namnet på den lagrade proceduren som definierar hur källdata ska tillämpas i en måltabell. <br/>Den här lagrade proceduren *anropas per batch*. För åtgärder som bara körs en gång och inte har något att göra med källdata, till exempel ta bort eller trunkera, använder du `preCopyScript` egenskapen .<br>Se exempel från [Invoke a stored procedure from a SQL sink (Anropa en lagrad procedur från en SQL-mottagare).](#invoke-a-stored-procedure-from-a-sql-sink) | Inga |
| storedProcedureTableTypeParameterName |Parameternamnet för tabelltypen som anges i den lagrade proceduren.  |Inga |
| sqlWriterTableType |Tabelltypens namn som ska användas i den lagrade proceduren. Kopieringsaktiviteten gör data som flyttas tillgängliga i en temporär tabell med den här tabelltypen. Kod för lagrad procedur kan sedan sammanfoga de data som kopieras med befintliga data. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn- och värdepar. Namn och hölje för parametrar måste matcha namnen och höljet för parametrarna för den lagrade proceduren. | Inga |
| writeBatchSize | Antal rader som ska infogas i SQL-tabellen *per batch*.<br/> Det tillåtna värdet **är heltal** (antal rader). Som standard Azure Data Factory dynamiskt lämplig batchstorlek baserat på radstorleken. | Inga |
| writeBatchTimeout | Väntetiden för att batchinfogningsåtgärden ska slutföras innan den får en time out.<br/> Det tillåtna värdet är **timespan**. Ett exempel är "00:30:00" (30 minuter). | Inga |
| disableMetricsCollection | Data Factory samlar in mått, till exempel Azure SQL Database DPU:er för optimering av kopieringsprestanda och rekommendationer, vilket introducerar ytterligare åtkomst till huvud-DB. Om du är bekymrad över det här beteendet anger du `true` för att inaktivera det. | Nej (standard är `false` ) |
| maxConcurrentConnections |Den övre gränsen för samtidiga anslutningar som upprättas till datalagret under aktivitetskörningen. Ange bara ett värde när du vill begränsa samtidiga anslutningar.| Inga |

**Exempel 1: Lägga till data**

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

**Exempel 2: Anropa en lagrad procedur under kopiering**

Läs mer i Anropa [en lagrad procedur från en SQL-mottagare.](#invoke-a-stored-procedure-from-a-sql-sink)

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

## <a name="parallel-copy-from-sql-database"></a>Parallellkopiering från SQL-databas

Anslutningsappen Azure SQL Database kopieringsaktivitet ger inbyggd datapartitionering för att kopiera data parallellt. Du hittar alternativ för datapartitionering på **fliken Källa** för kopieringsaktiviteten.

![Skärmbild av partitionsalternativ](./media/connector-sql-server/connector-sql-partition-options.png)

När du aktiverar partitionerad kopiering kör kopieringsaktivitet parallella frågor mot din Azure SQL Database för att läsa in data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen för kopieringsaktiviteten. Om du till exempel anger till fyra genererar och kör Data Factory fyra frågor samtidigt baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från `parallelCopies` Azure SQL Database.

Vi föreslår att du aktiverar parallell kopiering med datapartitionering, särskilt när du läser in stora mängder data från Azure SQL Database. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till ett filbaserat datalager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn). I så fall är prestanda bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig inläsning från stora tabeller med fysiska partitioner.        | **Partitionsalternativ:** Fysiska partitioner i tabellen. <br><br/>Under körningen Data Factory automatiskt de fysiska partitionerna och kopierar data efter partitioner. <br><br/>Om du vill kontrollera om tabellen har en fysisk partition eller inte kan du referera till [den här frågan](#sample-query-to-check-physical-partition). |
| Fullständig inläsning från stora tabeller, utan fysiska partitioner, med ett heltal eller en datetime-kolumn för datapartitionering. | **Partitionsalternativ:** Dynamisk intervallpartition.<br>**Partitionskolumn** (valfritt): Ange den kolumn som används för att partitionera data. Om inget anges används kolumnen index eller primärnyckel.<br/>**Övre partitionsbunden** **och partitionens nedre** gräns (valfritt): Ange om du vill fastställa partitionssteget. Detta är inte för filtrering av raderna i tabellen, utan alla rader i tabellen partitioneras och kopieras. Om inget värde anges identifierar kopieringsaktiviteten värdena automatiskt.<br><br>Om partitionskolumnens "ID" till exempel har värden från 1 till 100 och du anger den nedre gränsen som 20 och den övre gränsen till 80, med parallellkopiering som 4, hämtar Data Factory data med 4 partitioner – ID:n i intervallet <=20, [21, 50], [51, 80] och >=81. |
| Läs in en stor mängd data med hjälp av en anpassad fråga, utan fysiska partitioner, medan med ett heltal eller en date/datetime-kolumn för datapartitionering. | **Partitionsalternativ:** Dynamisk intervallpartition.<br>**Fråga:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitionskolumn:** Ange den kolumn som används för att partitionera data.<br>**Övre partitionsbunden** **och partitionens nedre** gräns (valfritt): Ange om du vill fastställa partitionssteget. Detta gäller inte för filtrering av raderna i tabellen. Alla rader i frågeresultatet partitioneras och kopieras. Om inget värde anges identifierar kopieringsaktiviteten värdet automatiskt.<br><br>Under körningen Data Factory med det faktiska kolumnnamnet och värdeintervallen för `?AdfRangePartitionColumnName` varje partition och skickar till Azure SQL Database. <br>Om partitionskolumnens "ID" till exempel har värden från 1 till 100 och du anger den nedre gränsen som 20 och den övre gränsen till 80, med parallellkopiering som 4, hämtar Data Factory data med 4 partitioner – ID:n i intervallet <=20, [21, 50], [51, 80] och >=81. <br><br>Här är fler exempelfrågor för olika scenarier:<br> 1. Fråga hela tabellen: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Fråga från en tabell med kolumnval och ytterligare filter för where-clause: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Fråga med underfrågor: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Fråga med partition i underfråga: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Metodtips för att läsa in data med partitionsalternativ:

1. Välj kolumn som partitionskolumn (t.ex. primärnyckel eller unik nyckel) för att undvika dataskev partition. 
2. Om tabellen har en inbyggd partition använder du partitionsalternativet "Fysiska partitioner i tabellen" för att få bättre prestanda.    
3. Om du använder Azure Integration Runtime för att kopiera data kan du ange större["dataintegreringsenheter (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) för att använda mer beräkningsresurser. Kontrollera de tillämpliga scenarierna där.
4. "[Grad av](copy-activity-performance-features.md#parallel-copy)kopieringsparallellitet " styr partitionsnumren, att ange det här talet för stort ibland skadar prestandan, rekommenderar att du anger det här talet som (DIU eller antalet IR-noder med egen värd) * (2 till 4).

**Exempel: fullständig inläsning från stora tabeller med fysiska partitioner**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med partition med dynamiskt intervall**

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

### <a name="sample-query-to-check-physical-partition"></a>Exempelfråga för att kontrollera fysisk partition

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

Om tabellen har en fysisk partition visas "HasPartition" som "ja" som följande.

![Sql-frågeresultat](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Bästa praxis för att läsa in data i Azure SQL Database

När du kopierar data till Azure SQL Database kan du behöva ett annat skrivbeteende:

- [Lägg till:](#append-data)Mina källdata har endast nya poster.
- [Upsert:](#upsert-data)Mina källdata har både infogningar och uppdateringar.
- [Överskrivning: Jag](#overwrite-the-entire-table)vill läsa in en hel dimensionstabell på nytt varje gång.
- [Skriva med anpassad logik:](#write-data-with-custom-logic)Jag behöver extra bearbetning innan den slutliga infogningen i måltabellen.

Se respektive avsnitt om hur du konfigurerar i Azure Data Factory och bästa praxis.

### <a name="append-data"></a>Lägga till data

Att appending data är standardbeteendet för den här Azure SQL Database-anslutningsappen för mottagare. Azure Data Factory massinfogning för att effektivt skriva till tabellen. Du kan konfigurera källan och mottagaren enligt detta i kopieringsaktiviteten.

### <a name="upsert-data"></a>Upserta data

**Alternativ 1:** När du har en stor mängd data att kopiera kan du massinläsning av alla poster i en mellanlagringstabell med hjälp av kopieringsaktiviteten och sedan köra en lagrad proceduraktivitet för att tillämpa en [MERGE-](/sql/t-sql/statements/merge-transact-sql) eller INSERT/UPDATE-instruktion i en enda bild. 

aktiviteten Kopiera stöder för närvarande inte inbyggt inläsning av data i en tillfällig databastabell. Det finns ett avancerat sätt att konfigurera det med en kombination av flera aktiviteter. Mer information finns i [Optimera Azure SQL Database Bulk Upsert-scenarier.](https://github.com/scoriani/azuresqlbulkupsert) Nedan visas ett exempel på hur du använder en permanent tabell som mellanlagring.

I den här Azure Data Factory du till exempel skapa en pipeline med en **aktiviteten Kopiera** länkad med en **lagrad proceduraktivitet**. Den tidigare kopierar data från källlagret till en Azure SQL Database mellanlagringstabell, till exempel **UpsertStagingTable**, som tabellnamn i datauppsättningen. Sedan anropar den senare en lagrad procedur för att sammanfoga källdata från mellanlagringstabellen till måltabellen och rensa mellanlagringstabellen.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

I databasen definierar du en lagrad procedur med MERGE-logik, som i följande exempel, som pekar på från den tidigare lagrade proceduraktiviteten. Anta att målet är tabellen **Marknadsföring med** tre kolumner: **ProfileID,** **State** och **Category**. Gör upsert baserat på **kolumnen ProfileID.**

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

**Alternativ 2:** Du kan välja att [anropa en lagrad procedur i kopieringsaktiviteten](#invoke-a-stored-procedure-from-a-sql-sink). Den här metoden kör varje batch (enligt egenskapen ) i källtabellen i stället för att använda massinfogning som standard `writeBatchSize` i kopieringsaktiviteten.

**Alternativ 3:** Du kan använda [Dataflöde](#sink-transformation) som erbjuder inbyggda insert-/upsert-/uppdateringsmetoder.

### <a name="overwrite-the-entire-table"></a>Skriva över hela tabellen

Du kan konfigurera egenskapen **preCopyScript** i kopieringsaktivitets mottagare. I det här fallet körs skriptet Azure Data Factory för varje kopieringsaktivitet som körs. Sedan körs kopian för att infoga data. Om du till exempel vill skriva över hela tabellen med senaste data anger du ett skript för att först ta bort alla poster innan du massinläsningar av nya data från källan.

### <a name="write-data-with-custom-logic"></a>Skriva data med anpassad logik

Stegen för att skriva data med anpassad logik liknar de som beskrivs i avsnittet [Upsert-data.](#upsert-data) När du behöver tillämpa extra bearbetning innan den slutliga infogningen av källdata i måltabellen kan du läsa in till en mellanlagringstabell och sedan anropa lagrad proceduraktivitet, anropa en lagrad procedur i kopieringsaktivitetens mottagare för att tillämpa data eller använda Mappnings-Dataflöde.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Anropa en lagrad procedur från en SQL-mottagare

När du kopierar data till Azure SQL Database kan du också konfigurera och anropa en användarangivet lagrad procedur med ytterligare parametrar för varje batch i källtabellen. Funktionen för lagrad procedur drar nytta [av tabellvärdesparametrar](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Du kan använda en lagrad procedur när inbyggda kopieringsmekanismer inte har syftet. Ett exempel är när du vill tillämpa extra bearbetning innan den slutliga infogningen av källdata i måltabellen. Några extra bearbetningsexempel är när du vill slå samman kolumner, leta upp ytterligare värden och infoga dem i mer än en tabell.

Följande exempel visar hur du använder en lagrad procedur för att göra en upsert i en tabell i Azure SQL Database. Anta att indata och **marknadsföringstabellen för** mottagaren har tre kolumner: **ProfileID,** **State** och **Category**. Gör upsert baserat på **kolumnen ProfileID** och tillämpa den endast för en specifik kategori som kallas "ProductA".

1. I databasen definierar du tabelltypen med samma namn som **sqlWriterTableType**. Schemat för tabelltypen är samma som schemat som returneras av dina indata.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. I databasen definierar du den lagrade proceduren med samma namn som **sqlWriterStoredProcedureName**. Den hanterar indata från den angivna källan och sammanfogas i utdatatabellen. Parameternamnet för tabelltypen i den lagrade proceduren är samma som **tableName som definierats** i datauppsättningen.

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

3. I Azure Data Factory definierar du **avsnittet SQL-mottagare** i kopieringsaktiviteten enligt följande:

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

## <a name="mapping-data-flow-properties"></a>Mappa dataflödesegenskaper

När du transformerar data i mappningsdataflödet kan du läsa och skriva till tabeller från Azure SQL Database. Mer information finns i källtransformering [och omvandling av](data-flow-source.md) mottagare [i](data-flow-sink.md) mappning av dataflöden.

### <a name="source-transformation"></a>Källtransformering

Inställningar som är Azure SQL Database är tillgängliga på **fliken Källalternativ** för källtransformering.

**Indata:** Välj om du vill peka källan på en tabell (motsvarande ```Select * from <table-name>``` ) eller ange en anpassad SQL-fråga.

**Fråga:** Om du väljer Fråga i indatafältet anger du en SQL-fråga för din källa. Den här inställningen åsidosätter alla tabeller som du har valt i datauppsättningen. **Order** By-satser stöds inte här, men du kan ange en fullständig SELECT FROM-instruktion. Du kan också använda användardefinierade tabellfunktioner. **select * från udfGetData() är** en UDF i SQL som returnerar en tabell. Den här frågan skapar en källtabell som du kan använda i ditt dataflöde. Att använda frågor är också ett bra sätt att minska antalet rader för testning eller sökningar.

**Lagrad** procedur: Välj det här alternativet om du vill generera en projektion och källdata från en lagrad procedur som körs från källdatabasen. Du kan ange schema, procedurnamn och parametrar eller klicka på Uppdatera för att be ADF att identifiera scheman och procedurnamn. Sedan kan du klicka på Importera för att importera alla procedurparametrar med hjälp av formuläret ``@paraName`` .

![Lagrad procedur](media/data-flow/stored-procedure-2.png "Lagrad procedur")

- SQL-exempel: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Parametriserat SQL-exempel: ``"select * from {$tablename} where orderyear > {$year}"``

**Batchstorlek:** Ange en batchstorlek för att dela upp stora data i läsningar.

**Isoleringsnivå:** Standardvärdet för SQL-källor i mappningsdataflödet är inläst. Du kan ändra isoleringsnivån här till något av följande värden:

- Läst in
- Läsa ogenomsagd
- Repeterbar läsning
- Serialiserbar
- Ingen (ignorera isoleringsnivå)

![Isoleringsnivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är Azure SQL Database är tillgängliga på fliken **Inställningar för** omvandlingen av mottagaren.

**Uppdateringsmetod:** Avgör vilka åtgärder som tillåts på ditt databasmål. Standardvärdet är att endast tillåta infogningar. För att uppdatera, upsert eller ta bort rader krävs en alter-row-transformering för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckelkolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

![Nyckelkolumner](media/data-flow/keycolumn.png "Nyckelkolumner")

Kolumnnamnet som du väljer som nyckel här används av ADF som en del av den efterföljande uppdateringen, upsert, ta bort. Därför måste du välja en kolumn som finns i sink-mappningen. Om du inte vill skriva värdet till den här nyckelkolumnen klickar du på "Hoppa över skrivning av nyckelkolumner".

Du kan parameterisera nyckelkolumnen som används här för att uppdatera Azure SQL Database måltabellen. Om du har flera kolumner för en sammansatt nyckel klickar du på "Anpassat uttryck" så kan du lägga till dynamiskt innehåll med hjälp av språket för ADF-dataflödesuttryck, som kan innehålla en matris med strängar med kolumnnamn för en sammansatt nyckel.

**Tabellåtgärd:** Anger om du vill återskapa eller ta bort alla rader från måltabellen innan du skriver.

- Ingen: Ingen åtgärd utförs i tabellen.
- Återskapa: Tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.
- Trunkera: Alla rader från måltabellen tas bort.

**Batchstorlek:** Styr hur många rader som skrivs i varje bucket. Större batchstorlekar förbättrar komprimering och minnesoptimering, men riskerar undantag från minnet vid cachelagring av data.

**Använd TempDB:** Som standard Data Factory en global temporär tabell för att lagra data som en del av inläsningsprocessen. Du kan också avmarkera alternativet "Använd TempDB" och istället be Data Factory att lagra den tillfälliga lagringstabellen i en användardatabas som finns i databasen som används för den här mottagaren.

![Använda Temp DB](media/data-flow/tempdb.png "Använda temp-databas")

**För- och efter-SQL-skript: Ange** flerrads-SQL-skript som ska köras före (förbearbetning) och efter att data (efterbearbetning) skrivs till sink-databasen

![skript för för- och efterbearbetning av SQL](media/data-flow/prepost1.png "SQL-bearbetningsskript")

### <a name="error-row-handling"></a>Felhantering av poster

När du skriver till Azure SQL DB kan vissa rader med data misslyckas på grund av begränsningar som anges av målet. Några vanliga fel är:

*    Strängdata eller binära data trunkeras i tabellen
*    Det går inte att infoga värdet NULL i kolumnen
*    INSERT-instruktionen står i konflikt med CHECK-begränsningen

Som standard misslyckas en dataflödeskörning vid det första felet som den får. Du kan välja att **Fortsätta vid fel** som gör att ditt dataflöde kan slutföras även om enskilda rader har fel. Azure Data Factory finns olika alternativ för att hantera de här felraderna.

**Genomför transaktion:** Välj om dina data ska skrivas i en enda transaktion eller i batchar. En enda transaktion ger sämre prestanda, men inga data som skrivs kommer att vara synliga för andra tills transaktionen har slutförts.  

**Avvisade utdata:** Om aktiverad kan du mata ut felraderna till en CSV-fil i Azure Blob Storage eller ett Azure Data Lake Storage Gen2 konto som du väljer. Detta skriver felraderna med ytterligare tre kolumner: SQL-åtgärden som INSERT eller UPDATE, felkoden för dataflödet och felmeddelandet på raden.

**Rapportera om felet lyckades:** Om det här alternativet är aktiverat markeras dataflödet som lyckat även om felrader hittas. 

![Felhantering av poster](media/data-flow/sql-error-row-handling.png "Felhantering av poster")


## <a name="data-type-mapping-for-azure-sql-database"></a>Datatypsmappning för Azure SQL Database

När data kopieras från eller till Azure SQL Database används följande mappningar från Azure SQL Database för att Azure Data Factory mellanliggande datatyper. Information om hur kopieringsaktiviteten mappar källschemat och datatypen till mottagaren finns i [Schema- och datatypmappningar.](copy-activity-schema-and-type-mapping.md)

| Azure SQL Database datatyp | Azure Data Factory tillfällig datatyp |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolesk |
| char |String, Char[] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Enkel |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| Sql_variant |Objekt |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Sträng |

>[!NOTE]
> För datatyper som mappar till interimtypen Decimal stöder för närvarande aktiviteten Kopiera precision upp till 28. Om du har data med större precision än 28 kan du konvertera till en sträng i SQL-fråga.

## <a name="lookup-activity-properties"></a>Egenskaper för sökningsaktivitet

Mer information om egenskaperna finns i [Lookup activity (Sökningsaktivitet).](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Mer information om egenskaperna finns i [GetMetadata-aktiviteten](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Använda Always Encrypted

När du kopierar data från/till Azure SQL Database med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)använder du allmän [ODBC-anslutning](connector-odbc.md) och SQL Server ODBC-drivrutin via Integration Runtime. Den Azure SQL Database anslutningsappen stöder inte Always Encrypted nu. 

Mer specifikt:

1. Konfigurera en Integration Runtime om du inte har en. Mer [information finns i artikeln Integration Runtime](create-self-hosted-integration-runtime.md) med egen värd.

2. Ladda ned 64-bitars ODBC-drivrutinen för SQL Server [här](/sql/connect/odbc/download-odbc-driver-for-sql-server)och installera på Integration Runtime datorn. Läs mer om hur den här drivrutinen fungerar [från Använda Always Encrypted med ODBC-drivrutinen för SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Skapa en länkad tjänst med ODBC-typ för att ansluta till DIN SQL-databas. Se följande exempel:

    - Så här **använder du SQL-autentisering:** Ange ODBC-anslutningssträngen enligt nedan och välj **Grundläggande** autentisering för att ange användarnamn och lösenord.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Om du kör en Integration Runtime azure-dator kan du använda autentisering med **hanterad identitet med** den virtuella Azure-datorns identitet:

        1. Följ samma krav [för att](#managed-identity) skapa databasanvändare för den hanterade identiteten och ge rätt roll i databasen.
        2. I länkad tjänst anger du ODBC-anslutningssträngen enligt nedan och väljer **Anonym** autentisering eftersom själva anslutningssträngen anger `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Skapa datauppsättnings- och kopieringsaktivitet med ODBC-typ. Läs mer i artikeln [om ODBC-anslutningsappen.](connector-odbc.md)

## <a name="next-steps"></a>Nästa steg

En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [Datalager och format som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)
