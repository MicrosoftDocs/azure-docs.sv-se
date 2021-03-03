---
title: Kopiera data från och till IBM Informix med Azure Data Factory
description: Lär dig hur du kopierar data från och till IBM Informix genom att använda en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/20/2021
ms.author: jingwang
ms.openlocfilehash: 23fc5cabb947f579177a26afc6baec873f2df154
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727914"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Kopiera data från och till IBM Informix med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från ett IBM-data lager i Informix. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Informix-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en Informix-källa till alla mottagar data lager som stöds, eller kopiera från alla käll data lager som stöds till Informix-mottagare. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .


## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här Informix-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .
- Installera Informix ODBC-drivrutinen för data lagret på den Integration Runtime datorn. Om du vill ha mer information om driv rutins installation och installation kan du läsa artikeln om driv rutins [Guide för Informix ODBC](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) i IBM Knowledge Center eller kontakta IBM support team för att få installations vägledning

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Informix-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Informix-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till: **Informix** | Ja |
| Begär | ODBC-anslutningssträngen exklusive Credential-delen. Du kan ange anslutnings strängen eller använda system-DSN (data källans namn) som du ställer in på den Integration Runtime datorn (du behöver fortfarande ange Credential-delen i den länkade tjänsten). <br> Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) .| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till data lagret för Informix.<br/>Tillåtna värden är: **Basic** och **Anonymous**. | Ja |
| userName | Ange användar namn om du använder grundläggande autentisering. | Inga |
| password | Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| credential | Delen autentiseringsuppgifter för den anslutnings sträng som anges i drivrutinsspecifika egenskaps värde format. Markera det här fältet som en SecureString. | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Informix-datauppsättningen.

Följande egenskaper stöds för att kopiera data från Informix:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **InformixTable** | Ja |
| tableName | Namnet på tabellen i Informix. | Nej för källa (om "fråga" i aktivitets källan har angetts);<br/>Ja för mottagare |

**Exempel**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Informix-källan.

### <a name="informix-as-source"></a>Informix som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** för att kopiera data från Informix:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **InformixSource** | Ja |
| DocumentDB | Använd den anpassade frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** för att kopiera data till Informix:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till: **InformixSink** | Ja |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br/>Tillåtna värden är: TimeSpan. Exempel: "00:30:00" (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (Standardvärdet är 0 – identifieras automatiskt) |
| preCopyScript |Ange en SQL-fråga för kopierings aktivitet som ska köras innan data skrivs till data lagret i varje körning. Du kan använda den här egenskapen för att rensa förinlästa data. |Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
