---
title: Kopiera och transformera data i Azure Database for MySQL
description: Lär dig hur du kopierar och transformerar data i Azure Database for MySQL med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012619"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Database for MySQL med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Database for MySQL och använda data flödet för att transformera data i Azure Database for MySQL. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

Den här anslutningen är specialiserad för [Azure Database for MySQL-tjänsten](../mysql/overview.md). Använd [MySQL Connector](connector-mysql.md)om du vill kopiera data från en allmän MySQL-databas som finns lokalt eller i molnet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Database for MySQL anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Database for MySQL koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Azure Database for MySQL länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzureMySql** | Ja |
| Begär | Ange information som krävs för att ansluta till Azure Database for MySQL-instansen. <br/> Du kan också ställa in lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

En typisk anslutnings sträng är `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Fler egenskaper som du kan ställa in per ärende:

| Egenskap | Beskrivning | Alternativ | Obligatorisk |
|:--- |:--- |:--- |:--- |
| SSLMode | Det här alternativet anger om driv rutinen använder TLS-kryptering och verifiering vid anslutning till MySQL. T.ex. `SSLMode=<0/1/2/3/4>`| Inaktive rad (0)/PRIORITERAt (1) **(standard)** /obligatoriskt (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Inga |
| UseSystemTrustStore | Det här alternativet anger om du vill använda ett CA-certifikat från arkivet för system förtroende eller från en angiven PEM-fil. T.ex. `UseSystemTrustStore=<0/1>;`| Aktiverat (1)/inaktiverat (0) **(standard)** | Inga |

**Exempel:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagra lösen ord i Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Database for MySQL data uppsättning.

Om du vill kopiera data från Azure Database for MySQL anger du egenskapen type för data uppsättningen till **AzureMySqlTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **AzureMySqlTable** | Ja |
| tableName | Namnet på tabellen i MySQL-databasen. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Database for MySQL källa och mottagare.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL som källa

För att kopiera data från Azure Database for MySQL, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **AzureMySqlSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |
| queryCommandTimeout | Vänte tiden innan fråge förfrågningen nådde tids gränsen. Standardvärdet är 120 minuter (02:00:00) | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL som mottagare

För att kopiera data till Azure Database for MySQL, stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till: **AzureMySqlSink** | Ja |
| preCopyScript | Ange en SQL-fråga för kopierings aktiviteten som ska köras innan data skrivs till Azure Database for MySQL i varje körning. Du kan använda den här egenskapen för att rensa de förinstallerade data. | Inga |
| writeBatchSize | Infogar data i Azure Database for MySQL tabellen när buffertstorleken når writeBatchSize.<br>Tillåtet värde är Integer som representerar antalet rader. | Nej (standard är 10 000) |
| writeBatchTimeout | Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br>Tillåtna värden är TimeSpan. Ett exempel är 00:30:00 (30 minuter). | Nej (standard är 00:00:30) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till tabeller från Azure Database for MySQL. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden. Du kan välja att använda en Azure Database for MySQL data uppsättning eller en [infogad data uppsättning](data-flow-source.md#inline-datasets) som källa och mottagar typ.

### <a name="source-transformation"></a>Käll omvandling

I tabellen nedan visas de egenskaper som stöds av Azure Database for MySQL källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabell | Om du väljer tabell som indata hämtar data flödet alla data från tabellen som anges i data uppsättningen. | Inga | - |*(endast för infogad data uppsättning)*<br>tableName |
| Fråga | Om du väljer fråga som indata anger du en SQL-fråga för att hämta data från källan, vilket åsidosätter alla tabeller som du anger i data uppsättningen. Att använda frågor är ett bra sätt att minska rader för testning eller sökning.<br><br>**Order by** -satsen stöds inte, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell som du kan använda i data flödet.<br>Exempel på frågor: `select * from mytable where customerId > 1000 and customerId < 2000` eller `select * from "MyTable"` .| Inga | Sträng | DocumentDB |
| Batchstorlek | Ange en batchstorlek för att segmentera stora data i batchar. | Inga | Integer | batchSize |
| Isoleringsnivå | Välj någon av följande isolerings nivåer:<br>-Läs bekräftad<br>-Läs-undedikerat (standard)<br>– Upprepnings bar läsning<br>-Serialiserbar<br>-Ingen (ignorera isolerings nivå) | Inga | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALISERA<br/>ALTERNATIVET</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Skript exempel för Azure Database for MySQLs källa

När du använder Azure Database for MySQL som ursprungs typ är det associerade data flödes skriptet:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Omvandling av mottagare

I tabellen nedan visas de egenskaper som stöds av Azure Database for MySQL Sink. Du kan redigera dessa egenskaper på fliken **mottagar alternativ** .

| Name | Beskrivning | Krävs | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Uppdaterings metod | Ange vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar.<br>Om du vill uppdatera, upsert eller ta bort rader krävs en [Alter Row-omvandling](data-flow-alter-row.md) för att tagga rader för dessa åtgärder. | Ja | `true` eller `false` | bort <br/>infognings bara <br/>uppdaterings bara <br/>upsertable |
| Nyckel kolumner | För uppdateringar, upsertar och borttagningar måste nyckel kolumnerna anges för att avgöra vilken rad som ska ändras.<br>Kolumn namnet som du väljer som nyckel kommer att användas som en del av den efterföljande uppdateringen, upsert, Delete. Därför måste du välja en kolumn som finns i Sink-mappningen. | Inga | Matris | keys |
| Hoppa över att skriva nyckel kolumner | Om du inte vill skriva värdet till nyckel kolumnen väljer du hoppa över skrivning av nyckel kolumner. | Inga | `true` eller `false` | skipKeyWrites |
| Tabell åtgärd |Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.<br>- **Ingen**: ingen åtgärd utförs i tabellen.<br>- **Återskapa**: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.<br>- **Trunkera**: alla rader från mål tabellen tas bort. | Inga | `true` eller `false` | återskapa<br/>truncate |
| Batchstorlek | Ange hur många rader som skrivs i varje batch. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras. | Inga | Integer | batchSize |
| För-och-post-SQL-skript | Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din Sink-databas. | Inga | Sträng | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Skript exempel för Azure Database for MySQL mottagare

När du använder Azure Database for MySQL som mottagar typ är det associerade data flödes skriptet:

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
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Data typs mappning för Azure Database for MySQL

När du kopierar data från Azure Database for MySQL används följande mappningar från MySQL-datatyper för att Azure Data Factory tillfälliga data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Azure Database for MySQL data typ | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
