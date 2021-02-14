---
title: Kopiera data från Sybase med Azure Data Factory
description: Lär dig hur du kopierar data från Sybase till mottagar data lager som stöds genom att använda en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 2ef63eded5403c1cf5faddec71ed3503c3ae2138
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384817"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Kopiera data från Sybase med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-sybase-connector.md)
> * [Aktuell version](connector-sybase.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en Sybase-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna Sybase-koppling stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Sybase Database till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna Sybase-anslutning:

- SAP Sybase SQL Anywhere (ASA) **version 16 och senare**.
- Kopiera data med **Basic** -eller **Windows** -autentisering.

Sybase-SWEETIQ och ASE stöds inte. Du kan använda allmän ODBC-anslutning med Sybase-drivrutin i stället.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här Sybase-kopplingen måste du:

- Konfigurera en egen värd Integration Runtime. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .
- Installera [dataprovidern för Sybase iAnywhere. data. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 eller senare på den integration runtime datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Sybase Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Sybase:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till: **Sybase** | Ja |
| server | Sybase-serverns namn. |Ja |
| databas | Sybase-databasens namn. |Ja |
| authenticationType | Typ av autentisering som används för att ansluta till Sybase-databasen.<br/>Tillåtna värden är: **Basic** och **Windows**. |Ja |
| användarnamn | Ange användar namnet för att ansluta till Sybase-databasen. |Ja |
| password | Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase-datauppsättningen.

Följande egenskaper stöds för att kopiera data från Sybase:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SybaseTable** | Ja |
| tableName | Namnet på tabellen i Sybase-databasen. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "SybaseTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att använda den nya som går framåt.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Sybase-källa.

### <a name="sybase-as-source"></a>Sybase som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Sybase:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SybaseSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "SybaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen framåt.

## <a name="data-type-mapping-for-sybase"></a>Data typs mappning för Sybase

När du kopierar data från Sybase används följande mappningar från Sybase-datatyper för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

Sybase stöder T-SQL-typer. En mappnings tabell från SQL-typer till Azure Data Factory interimistiska data typer finns i avsnittet [Azure SQL Database koppling – data typs mappning](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database) .

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).



## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
