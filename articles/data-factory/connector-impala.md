---
title: Kopiera data från Impala med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Impala till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Data Factory-pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 810ee757b70550c9dbeea708266b3fec48669571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378578"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiera data från Impala med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från Impala. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Impala-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Impala till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning. Därför behöver du inte installera en driv rutin manuellt för att använda den här anslutningen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Impala-anslutningen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Impala-tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Impala**. | Ja |
| värd | IP-adressen eller värd namnet för Impala-servern (det vill säga 192.168.222.160).  | Ja |
| port | TCP-porten som Impala-servern använder för att lyssna efter klient anslutningar. Standardvärdet är 21050.  | Inga |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är **Anonymous**, **SASLUsername** och **UsernameAndPassword**. | Ja |
| användarnamn | Det användar namn som används för att få åtkomst till Impala-servern. Standardvärdet är anonyma när du använder SASLUsername.  | Inga |
| password | Det lösen ord som motsvarar användar namnet när du använder UsernameAndPassword. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| enableSsl | Anger om anslutningarna till servern krypteras med hjälp av TLS. Standardvärdet är **falskt**.  | Inga |
| trustedCertPath | Den fullständiga sökvägen till. pem-filen som innehåller certifikat från betrodda certifikat utfärdare som används för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara anges när du använder TLS på egen värd Integration Runtime. Standardvärdet är cacerts. pem-filen som installeras med integration Runtime.  | Inga |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från systemets betrodda lager eller från en angiven PEM-fil. Standardvärdet är **falskt**.  | Inga |
| allowHostNameCNMismatch | Anger om ett CA-utfärdat TLS/SSL-certifikat namn ska matcha värd namnet för servern när du ansluter via TLS. Standardvärdet är **falskt**.  | Inga |
| allowSelfSignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är **falskt**.  | Inga |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Impala-datauppsättningen.

Om du vill kopiera data från Impala anger du egenskapen type för data uppsättningen till **ImpalaObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **ImpalaObject** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av käll typen Impala.

### <a name="impala-as-a-source-type"></a>Impala som typ av källa

Om du vill kopiera data från Impala anger du käll typen i kopierings aktiviteten till **ImpalaSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **ImpalaSource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
