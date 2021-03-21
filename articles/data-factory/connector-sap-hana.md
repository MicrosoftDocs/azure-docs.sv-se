---
title: Kopiera data från SAP HANA
description: Lär dig hur du kopierar data från SAP HANA till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ce3c1e22dd030c0730bf4d9859591c00860908a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382284"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuell version](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!TIP]
>Information om hur du hanterar ADF: s övergripande support på SAP data integrations scenario finns i [SAP-dataintegrering med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion till varje SAP-koppling, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP HANA anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP HANA-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här SAP HANA-anslutningen:

- Kopiera data från en version av SAP HANA Database.
- Kopiera data från **Hana-informations modeller** (till exempel analys-och beräknings visningar) och **rad/kolumn-tabeller**.
- Kopiera data med **Basic** -eller **Windows** -autentisering.
- Parallell kopiering från en SAP HANA källa. Mer information finns i avsnittet [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) .

> [!TIP]
> Om du vill kopiera data **till** SAP HANA data lager använder du allmän ODBC-anslutning. Mer information finns i avsnittet [SAP HANA mottagare](#sap-hana-sink) . Observera att de länkade tjänsterna för SAP HANA koppling och ODBC-koppling är av olika typ, så att de inte kan återanvändas.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP HANA-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .
- Installera SAP HANA ODBC-drivrutinen på datorn Integration Runtime. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SAP HANA koppling.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för SAP HANA länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapHana** | Ja |
| Begär | Ange information som behövs för att ansluta till SAP HANA med hjälp av **grundläggande autentisering** eller **Windows-autentisering**. Se följande exempel.<br>I anslutnings strängen är server/port obligatorisk (standard porten är 30015) och användar namn och lösen ord är obligatoriska när du använder grundläggande autentisering. Mer avancerade inställningar finns i [SAP HANA egenskaper för ODBC-anslutning](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Du kan också ställa in lösen ord i Azure Key Vault och hämta lösen ords konfigurationen från anslutnings strängen. Se [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| userName | Ange användar namn när du använder Windows-autentisering. Exempel: `user@domain.com` | Inga |
| password | Ange lösen ordet för användar kontot. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel: Använd grundläggande autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: Använd Windows-autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Om du använder SAP HANA länkade tjänsten med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen.

**Exempel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA data uppsättning.

Följande egenskaper stöds för att kopiera data från SAP HANA:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SapHanaTable** | Ja |
| schema | Namnet på schemat i SAP HANA databasen. | Nej (om "fråga" i aktivitets källan har angetts) |
| tabell | Namnet på tabellen i SAP HANA databasen. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA källa.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

>[!TIP]
>Om du vill mata in data från SAP HANA effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) -avsnittet.

För att kopiera data från SAP HANA, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SapHanaSource** | Ja |
| DocumentDB | Anger SQL-frågan för att läsa data från SAP HANA-instansen. | Ja |
| partitionOptions | Anger de data partitionerings alternativ som används för att mata in data från SAP HANA. Läs mer från  [SAP HANA avsnittet om parallell kopiering](#parallel-copy-from-sap-hana) .<br>Tillåtna värden är: **ingen**   (standard), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Läs mer från  [SAP HANA avsnittet om parallell kopiering](#parallel-copy-from-sap-hana) . `PhysicalPartitionsOfTable` kan endast användas när du kopierar data från en tabell men inte frågar. <br>När ett partitions alternativ är aktiverat (dvs. inte `None` ), kontrol leras graden av parallellitet för att samtidigt läsa in data från SAP HANA av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. | Falskt |
| partitionSettings | Ange gruppen med inställningar för data partitionering.<br>Använd när partition alternativet är `SapHanaDynamicRange` . | Falskt |
| partitionColumnName | Ange namnet på den käll kolumn som ska användas av en partition för parallell kopiering. Om detta inte anges identifieras indexet eller primär nyckeln för tabellen automatiskt och används som partition-kolumn.<br>Använd när alternativet partition är  `SapHanaDynamicRange` . Om du använder en fråga för att hämta källdata, Hook  `?AdfHanaDynamicRangePartitionCondition` i WHERE-satsen. Se exempel i [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) -avsnittet. | Ja när du använder `SapHanaDynamicRange` partition. |
| packetSize | Anger storleken på nätverks paketet (i kilobyte) för att dela data till flera block. Om du har stora mängder data som ska kopieras kan ökande paket storlek öka Läs hastigheten från SAP HANA i de flesta fall. Prestanda testning rekommenderas när du justerar paket storleken. | Nej.<br>Standardvärdet är 2048 (2 MB). |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ kopierings källa, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="parallel-copy-from-sap-hana"></a>Parallell kopiering från SAP HANA

Data Factory SAP HANA-anslutningen innehåller inbyggda data partitioner för att kopiera data från SAP HANA parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din SAP HANA källa för att hämta data efter partitioner. Den parallella graden styrs av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel anger `parallelCopies` fyra Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från SAP HANA.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du matar in stora mängder data från din SAP HANA. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                           | Inställningar för förslag                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                        | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt typen av fysisk partition för den angivna SAP HANA tabellen och väljer motsvarande partitionsnyckel:<br>- **Intervall partitionering**: hämta den partitionstabell och de partition intervall som har definierats för tabellen och kopiera sedan data efter intervall. <br>- **Hash**-partitionering: Använd nyckel för hash-partition som partition och sedan partitionera och kopiera data baserat på beräknade intervall i ADF. <br>- **Partitionering** med resursallokering eller **ingen partition**: Använd primär nyckel som partitionstabell och partitionera sedan och kopiera data baserat på beräknade intervall i ADF. |
| Läs in stora mängder data med hjälp av en anpassad fråga. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partitionstabell**: Ange den kolumn som används för att använda partitionen för dynamiskt intervall. <br><br>Under körningen beräknar Data Factory först värde intervallen för den angivna partitionstabellen, genom att fördela raderna i ett antal buckets i enlighet med antalet distinkta värden för kolumn värden och inställningen för parallell kopiering, ersätter `?AdfHanaDynamicRangePartitionCondition` med att filtrera partitionens kolumn värde intervall för varje partition och skickar till SAP HANA.<br><br>Om du vill använda flera kolumner som partitionstabell kan du sammanfoga värdena i varje kolumn som en kolumn i frågan och ange den som partitions kolumn i ADF, t `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . ex.. |

**Exempel: fråga med fysiska partitioner i en tabell**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med Dynamic Range-partition**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Data typs mappning för SAP HANA

När du kopierar data från SAP HANA används följande mappningar från SAP HANA data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| SAP HANA data typ | Data fabrikens interimistiska datatyp |
| ------------------ | ------------------------------ |
| ALPHANUM           | Sträng                         |
| BIGINT             | Int64                          |
| BINARY             | Byte []                         |
| Bintext            | Sträng                         |
| BLOB               | Byte []                         |
| BOOLESKA               | Byte                           |
| CLOB               | Sträng                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLYTA              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | Sträng                         |
| NVARCHAR           | Sträng                         |
| REAL               | Enkel                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | Sträng                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte []                         |
| STPOINTTYPE        | Byte []                         |
| TEXT               | Sträng                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Sträng                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte []                         |

## <a name="sap-hana-sink"></a>SAP HANA mottagare

SAP HANA-anslutningen stöds för närvarande inte som mottagare, men du kan använda allmän ODBC-anslutning med SAP HANA driv rutin för att skriva data till SAP HANA. 

Följ [kraven](#prerequisites) för att konfigurera egen värd integration Runtime och installera SAP HANA ODBC-drivrutinen först. Skapa en ODBC-länkad tjänst för att ansluta till din SAP HANA data lager som visas i följande exempel, skapa data uppsättning och kopiera aktivitets mottagare med ODBC-typ. Läs mer från artikeln om [ODBC-koppling](connector-odbc.md) .

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
