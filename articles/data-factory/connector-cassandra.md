---
title: Kopiera data från Cassandra med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Cassandra till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383712"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiera data från Cassandra med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuell version](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en Cassandra-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Cassandra-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Cassandra-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna Cassandra-anslutning:

- Cassandra- **versionerna 2. x och 3. x**.
- Kopiera data med **Basic** eller **Anonym** autentisering.

>[!NOTE]
>För att aktiviteter som körs på egen värd Integration Runtime, stöds Cassandra 3. x sedan IR version 3,7 och senare.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime innehåller en inbyggd Cassandra-drivrutin, och du behöver därför inte installera någon driv rutin manuellt när du kopierar data från/till Cassandra.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Cassandra-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Cassandra:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **Cassandra** |Ja |
| värd |En eller flera IP-adresser eller värd namn för Cassandra-servrar.<br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klient anslutningar. |Nej (standard är 9042) |
| authenticationType | Typ av autentisering som används för att ansluta till Cassandra-databasen.<br/>Tillåtna värden är: **Basic** och **Anonymous**. |Ja |
| användarnamn |Ange användar namn för användar kontot. |Ja, om authenticationType har angetts till Basic. |
| password |Ange lösen ordet för användar kontot. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja, om authenticationType har angetts till Basic. |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

>[!NOTE]
>Anslutning till Cassandra som använder TLS stöds inte.

**Exempel:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra DataSet.

Om du vill kopiera data från Cassandra anger du egenskapen type för data uppsättningen till **CassandraTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **CassandraTable** | Ja |
| keyspace |Namnet på det här utrymmet eller schemat i Cassandra-databasen. |Nej (om "fråga" för "CassandraSource" har angetts) |
| tableName |Namnet på tabellen i Cassandra-databasen. |Nej (om "fråga" för "CassandraSource" har angetts) |

**Exempel:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet


En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra-källan.

### <a name="cassandra-as-source"></a>Cassandra som källa

Om du vill kopiera data från Cassandra anger du käll typen i kopierings aktiviteten till **CassandraSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **CassandraSource** | Ja |
| DocumentDB |Använd den anpassade frågan för att läsa data. SQL-92 fråga eller CQL-fråga. Se [referens för CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga anger du namnet på det **. tabell namn** som ska representera den tabell som du vill fråga. |Nej (om "tableName" och "tecken utrymme" i data uppsättningen har angetts). |
| consistencyLevel |Konsekvens nivån anger hur många repliker som måste svara på en Read-begäran innan data returneras till klient programmet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla Read-begäran. Mer information finns i [Konfigurera data konsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) .<br/><br/>Tillåtna värden är: **ett**, **två**, **tre**, **kvorum**, **alla**, **LOCAL_QUORUM**, **EACH_QUORUM** och **LOCAL_ONE**. |Nej (standard är `ONE` ) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Data typs mappning för Cassandra

När du kopierar data från Cassandra används följande mappningar från Cassandra data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| Data typen Cassandra | Data fabrikens interimistiska datatyp |
|:--- |:--- |
| ASCII |Sträng |
| BIGINT |Int64 |
| BLOB |Byte [] |
| BOOLESKT |Boolesk |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLYTA |Enkel |
| INET |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIMESTAMP |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sträng |
| VARINT |Decimal |

> [!NOTE]
> För samlings typer (karta, uppsättning, lista osv.), se [arbeta med Cassandra-samlings typer med hjälp av virtuell tabell](#work-with-collections-using-virtual-table) avsnitt.
>
> Användardefinierade typer stöds inte.
>
> Längden på binära kolumn-och sträng kolumn längder får inte vara större än 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med hjälp av virtuell tabell

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från Cassandra-databasen. För samlings typer, inklusive karta, uppsättning och lista, normaliserar driv rutinen data till motsvarande virtuella tabeller. Mer specifikt, om en tabell innehåller alla samlings kolumner, genererar driv rutinen följande virtuella tabeller:

* En **bas tabell** som innehåller samma data som den verkliga tabellen, förutom samlings kolumnerna. Bas tabellen använder samma namn som den verkliga tabell som den representerar.
* En **virtuell tabell** för varje samlings kolumn som utökar de kapslade data. De virtuella tabellerna som representerar samlingar namnges med hjälp av namnet på den verkliga tabellen, en avgränsare "*VT*" och namnet på kolumnen.

Virtuella tabeller refererar till datan i den verkliga tabellen, vilket gör att driv rutinen kan komma åt denormaliserade data. Mer information finns i avsnittet exempel. Du kan komma åt innehållet i Cassandra-samlingar genom att fråga och ansluta till de virtuella tabellerna.

### <a name="example"></a>Exempel

Till exempel är följande "ExampleTable" en Cassandra-databas tabell som innehåller en heltals primär nyckel kolumn med namnet "pk_int", en text kolumn med namnet värde, en List kolumn, en kart kolumn och en Set-kolumn (med namnet "StringSet").

| pk_int | Värde | Lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"exempel värde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"exempel värde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Driv rutinen skulle generera flera virtuella tabeller som representerar den här enskilda tabellen. Sekundär nyckel kolumnerna i de virtuella tabellerna refererar till primär nyckel kolumnerna i den verkliga tabellen och anger vilken verklig tabell rad den virtuella tabell raden motsvarar.

Den första virtuella tabellen är bas tabellen med namnet "ExampleTable" som visas i följande tabell: 

| pk_int | Värde |
| --- | --- |
| 1 |"exempel värde 1" |
| 3 |"exempel värde 3" |

Bas tabellen innehåller samma data som den ursprungliga databas tabellen förutom samlingarna, som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

I följande tabeller visas de virtuella tabeller som normaliserar data från kolumnerna List, Map och StringSet. Kolumnerna med namn som slutar med "_index" eller "_key" visar positionen för data i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med "_value" innehåller utökade data från samlingen.

**Tabell ExampleTable_vt_List:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabell ExampleTable_vt_Map:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabell ExampleTable_vt_StringSet:**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
