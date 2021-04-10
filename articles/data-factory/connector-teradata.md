---
title: Kopiera data från Teradata-Vantage med hjälp av Azure Data Factory
description: Med Teradata-anslutaren i Data Factory-tjänsten kan du kopiera data från en Teradata-Vantage till data lager som stöds av Data Factory som mottagare.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381774"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopiera data från Teradata-Vantage med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuell version](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Teradata-Vantage. Den bygger på [Översikt över kopierings aktiviteten](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Teradata-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Teradata-Vantage till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här Teradata-anslutaren:

- Teradata **version 14,10, 15,0, 15,10, 16,0, 16,10 och 16,20**.
- Kopiera data med hjälp av **Basic**-, **Windows**-eller **LDAP-** autentisering.
- Parallell kopiering från en Teradata-källa. Mer information finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) .

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Om du använder Integration Runtime med egen värd är det en inbyggd Teradata-drivrutin som börjar med version 3,18. Du behöver inte installera någon driv rutin manuellt. Driv rutinen kräver "Visual C++ Redistributable 2012 uppdatering 4" på den lokala datorn för integration Runtime. Om du inte redan har installerat det kan du ladda ned det [här](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Teradata-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Den länkade tjänsten Teradata stöder följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Teradata**. | Ja |
| Begär | Anger den information som krävs för att ansluta till Teradata-instansen. Se följande exempel.<br/>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) . | Ja |
| användarnamn | Ange ett användar namn för att ansluta till Teradata. Gäller när du använder Windows-autentisering. | Inga |
| password | Ange ett lösen ord för det användar konto som du har angett som användar namn. Du kan också välja att [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). <br>Gäller när du använder Windows-autentisering eller refererar till ett lösen ord i Key Vault för grundläggande autentisering. | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

Fler anslutnings egenskaper som du kan ange i anslutnings strängen per ärende:

| Egenskap | Beskrivning | Standardvärde |
|:--- |:--- |:--- |
| TdmstPortNumber | Numret på porten som används för att få åtkomst till Teradata-databasen.<br>Ändra inte det här värdet om du inte uppmanas att göra det av teknisk support. | 1025 |
| UseDataEncryption | Anger om all kommunikation med Teradata-databasen ska krypteras. Tillåtna värden är 0 eller 1.<br><br/>- **0 (inaktive rad, standard)**: krypterar endast autentiseringsinformation.<br/>- **1 (aktive rad)**: krypterar alla data som skickas mellan driv rutinen och databasen. | `0` |
| CharacterSet | Den teckenuppsättning som ska användas för sessionen. T. ex., `CharacterSet=UTF16` .<br><br/>Det här värdet kan vara en användardefinierad teckenuppsättning eller någon av följande fördefinierade teckenuppsättningar: <br/>– ASCII<br/>– UTF8<br/>– UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-JIS (Windows, DOS-kompatibel, KANJISJIS_0S)<br/>– EUC (UNIX-kompatibel, KANJIEC_0U)<br/>– IBM-stordator (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>– BIG5 (TCHBIG5_1R0)<br/>– GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | `ASCII` |
| MaxRespSize |Maximal storlek för svars buffert för SQL-begäranden i kilobyte (KB). T. ex., `MaxRespSize=‭10485760‬` .<br/><br/>För Teradata-databas version 16,00 eller senare är det maximala värdet 7361536. För anslutningar som använder tidigare versioner är det maximala värdet 1048576. | `65536` |
| MechanismName | Om du vill använda LDAP-protokollet för att autentisera anslutningen anger du `MechanismName=LDAP` . | Ej tillämpligt |

**Exempel med grundläggande autentisering**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel som använder Windows-autentisering**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel med LDAP-autentisering**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Följande nytto Last stöds fortfarande. Du bör dock använda den nya.

**Föregående nytto last:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-datauppsättningen. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

Följande egenskaper stöds för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för data mängden måste anges till `TeradataTable` . | Ja |
| databas | Namnet på Teradata-instansen. | Nej (om "fråga" i aktivitets källan har angetts) |
| tabell | Namnet på tabellen i Teradata-instansen. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` typ data uppsättningen stöds fortfarande. Vi rekommenderar dock att du använder den nya data uppsättningen.

**Föregående nytto last:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som stöds av Teradata-källan. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata som källa

>[!TIP]
>Om du vill läsa in data från Teradata effektivt genom att använda data partitionering kan du läsa mer från [Parallel Copy från Teradata](#parallel-copy-from-teradata) -avsnittet.

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Teradata:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste vara inställd på `TeradataSource` . | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`.<br>När du aktiverar partitionerad belastning måste du koppla alla motsvarande inbyggda partitionsalternativ i frågan. Exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Nej (om tabellen i data uppsättningen har angetts) |
| partitionOptions | Anger de data partitionerings alternativ som används för att läsa in data från Teradata. <br>Tillåt värden är: **ingen** (standard), **hash** -och **DynamicRange**.<br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att data ska läsas in från Teradata av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Inga |
| partitionSettings | Ange gruppen med inställningar för data partitionering. <br>Använd när partition alternativet inte är `None` . | Inga |
| partitionColumnName | Ange namnet på den käll kolumn som ska användas av intervall partition eller hash-partition för parallell kopiering. Om inget anges identifieras primärt index för tabellen automatiskt och används som partition-kolumn. <br>Använd när alternativet partition är `Hash` eller `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfHashPartitionCondition` eller  `?AdfRangePartitionColumnName` i WHERE-satsen. Se exempel i [Parallel Copy från Teradata](#parallel-copy-from-teradata) -avsnittet. | Inga |
| partitionUpperBound | Det maximala värdet för partition-kolumnen för att kopiera data. <br>Använd när partition alternativet är `DynamicRange` . Om du använder Query för att hämta källdata, Hook `?AdfRangePartitionUpbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Inga |
| partitionLowerBound | Det minimala värdet för kolumnen partition som ut data ska kopieras. <br>Använd när alternativet partition är `DynamicRange` . Om du använder en fråga för att hämta källdata, Hook `?AdfRangePartitionLowbound` i WHERE-satsen. Ett exempel finns i avsnittet [Parallel Copy från Teradata](#parallel-copy-from-teradata) . | Inga |

> [!NOTE]
>
> `RelationalSource` typ kopierings källan stöds fortfarande, men den har inte stöd för den nya inbyggda parallella inläsningen från Teradata (partitionsalternativ). Vi rekommenderar dock att du använder den nya data uppsättningen.

**Exempel: kopiera data med en grundläggande fråga utan partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Parallell kopiering från Teradata

Data Factory Teradata-anslutaren tillhandahåller inbyggd data partitionering för att kopiera data från Teradata parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-teradata/connector-teradata-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din Teradata-källa för att läsa in data med partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från din Teradata.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du läser in stora mängder data från din Teradata. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager, skrivs de om för att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                                     | Inställningar för förslag                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                                   | **Partitionerings alternativ**: hash. <br><br/>Under körningen identifierar Data Factory automatiskt kolumnen primärt index, använder en hash mot den och kopierar data efter partitioner. |
| Läs in stora mängder data med hjälp av en anpassad fråga.                 | **Partitionerings alternativ**: hash.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för Apply hash-partition. Om inget anges identifierar Data Factory automatiskt kolumnen PK i tabellen som du angav i Teradata-datauppsättningen.<br><br>Under körningen ersätts Data Factory `?AdfHashPartitionCondition` med hash-partitionens logik och skickas till Teradata. |
| Läs in stora mängder data med hjälp av en anpassad fråga med en heltals kolumn med jämnt distribuerat värde för intervall partitionering. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att partitionera data. Du kan partitionera mot kolumnen med data typen Integer.<br>**Partitionens övre gränser** och **partition nedre gränser**: Ange om du vill filtrera mot kolumnen partition för att endast hämta data mellan det nedre och övre intervallet.<br><br>Under körningen ersätts Data Factory `?AdfRangePartitionColumnName` , `?AdfRangePartitionUpbound` och `?AdfRangePartitionLowbound` med det faktiska kolumn namnet och värde intervallet för varje partition och skickas till Teradata. <br>Om t. ex. partitionens kolumn "ID" har angetts med den nedre gränser som 1 och den övre gränser som 80, med parallell kopierings uppsättning som 4, Data Factory hämtar data med 4 partitioner. Deras ID: n är mellan [1, 20], [21, 40], [41, 60] och [61, 80]. |

**Exempel: fråga med hash-partition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Data typs mappning för Teradata

När du kopierar data från Teradata gäller följande mappningar. Information om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren finns i [schema-och data typs mappningar](copy-activity-schema-and-type-mapping.md).

| Data typen Teradata | Data Factory data typen Interim |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte [] |
| Byte |Byte [] |
| ByteInt |Int16 |
| Char |Sträng |
| CLOB |Sträng |
| Datum |DateTime |
| Decimal |Decimal |
| Double |Double |
| Infoga |Stöds inte. Använd explicit Cast i käll frågan. |
| Integer |Int32 |
| Intervall dag |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall, dag till timme |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall, dag till minut |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall, dag till sekund |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall timme |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall timme till minut |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall timme till sekund |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall minut |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall i minuter till sekund |Stöds inte. Använd explicit Cast i käll frågan. |
| Månads intervall |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall sekund |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall år |Stöds inte. Använd explicit Cast i käll frågan. |
| Intervall år till månad |Stöds inte. Använd explicit Cast i käll frågan. |
| Antal |Double |
| Period (datum) |Stöds inte. Använd explicit Cast i käll frågan. |
| Period (tid) |Stöds inte. Använd explicit Cast i käll frågan. |
| Period (tid med tidszon) |Stöds inte. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel) |Stöds inte. Använd explicit Cast i käll frågan. |
| Period (tidsstämpel med tidszon) |Stöds inte. Använd explicit Cast i käll frågan. |
| SmallInt |Int16 |
| Tid |TimeSpan |
| Tid med tidszon |TimeSpan |
| Timestamp |DateTime |
| Tidsstämpel med tidszon |DateTime |
| VarByte |Byte [] |
| VarChar |Sträng |
| VarGraphic |Stöds inte. Använd explicit Cast i käll frågan. |
| Xml |Stöds inte. Använd explicit Cast i käll frågan. |


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
