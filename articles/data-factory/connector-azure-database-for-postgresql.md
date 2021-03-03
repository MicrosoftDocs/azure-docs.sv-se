---
title: Kopiera och transformera data i Azure Database for PostgreSQL
description: Lär dig hur du kopierar och transformerar data i Azure Database for PostgreSQL med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738063"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Database for PostgreSQL med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Database for PostgreSQL och använda data flödet för att transformera data i Azure Database for PostgreSQL. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

Den här anslutningen är specialiserad för den [Azure Database for PostgreSQL tjänsten](../postgresql/overview.md). Om du vill kopiera data från en allmän PostgreSQL-databas som finns lokalt eller i molnet använder du [postgresql-anslutningen](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Database for PostgreSQL anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med en [matris för käll/mottagare som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

För närvarande stöder data flödet Azure Database för PostgreSQL-Server, men inte flexibel Server eller storskalig (citus).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Database for PostgreSQL koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Azure Database for PostgreSQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzurePostgreSql**. | Ja |
| Begär | En ODBC-anslutningssträng för att ansluta till Azure Database for PostgreSQL.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) för mer information. | Ja |
| connectVia | Den här egenskapen representerar [integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

En typisk anslutnings sträng är `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Här är fler egenskaper som du kan ställa in per ärende:

| Egenskap | Beskrivning | Alternativ | Obligatorisk |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Den metod som driv rutinen använder för att kryptera data som skickas mellan driv rutinen och databas servern. Till exempel  `EncryptionMethod=<0/1/6>;`| 0 (ingen kryptering) **(standard)** /1 (SSL)/6 (RequestSSL) | Inga |
| ValidateServerCertificate (VSC) | Avgör om driv rutinen verifierar certifikatet som skickas av databas servern när SSL-kryptering är aktiverat (krypterings metod = 1). Till exempel  `ValidateServerCertificate=<0/1>;`| 0 (inaktive rad) **(standard)** /1 (aktive rad) | Inga |

**Exempel**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exempel**:

***Lagra lösen ord i Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns [i data uppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). Det här avsnittet innehåller en lista över egenskaper som Azure Database for PostgreSQL stöder i data uppsättningar.

Om du vill kopiera data från Azure Database for PostgreSQL anger du egenskapen type för data uppsättningen till **AzurePostgreSqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzurePostgreSqlTable** | Ja |
| tableName | Tabellens namn | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av en Azure Database for PostgreSQL källa.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSql som källa

Om du vill kopiera data från Azure Database for PostgreSQL anger du käll typen i kopierings aktiviteten till **AzurePostgreSqlSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzurePostgreSqlSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `SELECT * FROM mytable` eller `SELECT * FROM "MyTable"` . Observera i PostgreSQL behandlas enhets namnet som Skift läges okänsligt om det inte anges i citat tecken. | Nej (om egenskapen tableName i data uppsättningen anges) |

**Exempel**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL som mottagare

För att kopiera data till Azure Database for PostgreSQL, stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzurePostgreSQLSink**. | Ja |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan du skriver data till Azure Database for PostgreSQL i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Inga |
| writeMethod | Den metod som används för att skriva data till Azure Database for PostgreSQL.<br>Tillåtna värden är: **CopyCommand** (för hands version, som är mer presterande), **BulkInsert** (standard). | Inga |
| writeBatchSize | Antalet rader som lästs in i Azure Database for PostgreSQL per batch.<br>Tillåtet värde är ett heltal som representerar antalet rader. | Nej (standard är 1 000 000) |
| writeBatchTimeout | Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br>Tillåtna värden är TimeSpan-strängar. Ett exempel är 00:30:00 (30 minuter). | Nej (standard är 00:30:00) |

**Exempel**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till tabeller från Azure Database for PostgreSQL. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden. Du kan välja att använda en Azure Database for PostgreSQL data uppsättning eller en [infogad data uppsättning](data-flow-source.md#inline-datasets) som källa och mottagar typ.

### <a name="source-transformation"></a>Käll omvandling

I tabellen nedan visas de egenskaper som stöds av Azure Database for PostgreSQL källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabell | Om du väljer tabell som indata hämtar data flödet alla data från tabellen som anges i data uppsättningen. | Inga | - |*(endast för infogad data uppsättning)*<br>tableName |
| Söka i data | Om du väljer fråga som indata anger du en SQL-fråga för att hämta data från källan, vilket åsidosätter alla tabeller som du anger i data uppsättningen. Att använda frågor är ett bra sätt att minska rader för testning eller sökning.<br><br>**Order by** -satsen stöds inte, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell som du kan använda i data flödet.<br>Exempel på frågor: `select * from mytable where customerId > 1000 and customerId < 2000` eller `select * from "MyTable"` . Observera i PostgreSQL behandlas enhets namnet som Skift läges okänsligt om det inte anges i citat tecken.| Inga | Sträng | DocumentDB |
| Batchstorlek | Ange en batchstorlek för att segmentera stora data i batchar. | Inga | Integer | batchSize |
| Isoleringsnivå | Välj någon av följande isolerings nivåer:<br>-Läs bekräftad<br>-Läs-undedikerat (standard)<br>– Upprepnings bar läsning<br>-Serialiserbar<br>-Ingen (ignorera isolerings nivå) | Inga | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALISERA<br/>ALTERNATIVET</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Skript exempel för Azure Database for PostgreSQLs källa

När du använder Azure Database for PostgreSQL som ursprungs typ är det associerade data flödes skriptet:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Omvandling av mottagare

I tabellen nedan visas de egenskaper som stöds av Azure Database for PostgreSQL Sink. Du kan redigera dessa egenskaper på fliken **mottagar alternativ** .

| Namn | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Uppdaterings metod | Ange vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar.<br>Om du vill uppdatera, upsert eller ta bort rader krävs en [Alter Row-omvandling](data-flow-alter-row.md) för att tagga rader för dessa åtgärder. | Ja | `true` eller `false` | bort <br/>infognings bara <br/>uppdaterings bara <br/>upsertable |
| Nyckel kolumner | För uppdateringar, upsertar och borttagningar måste nyckel kolumnerna anges för att avgöra vilken rad som ska ändras.<br>Kolumn namnet som du väljer som nyckel kommer att användas som en del av den efterföljande uppdateringen, upsert, Delete. Därför måste du välja en kolumn som finns i Sink-mappningen. | Inga | Matris | keys |
| Hoppa över att skriva nyckel kolumner | Om du inte vill skriva värdet till nyckel kolumnen väljer du hoppa över skrivning av nyckel kolumner. | Inga | `true` eller `false` | skipKeyWrites |
| Tabell åtgärd |Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.<br>- **Ingen**: ingen åtgärd utförs i tabellen.<br>- **Återskapa**: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.<br>- **Trunkera**: alla rader från mål tabellen tas bort. | Inga | `true` eller `false` | återskapa<br/>truncate |
| Batchstorlek | Ange hur många rader som skrivs i varje batch. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras. | Inga | Integer | batchSize |
| För-och-post-SQL-skript | Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din Sink-databas. | Inga | Sträng | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Skript exempel för Azure Database for PostgreSQL mottagare

När du använder Azure Database for PostgreSQL som mottagar typ är det associerade data flödes skriptet:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns [i lookup-aktivitet i Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
