---
title: Kopiera data från Spark
description: Lär dig hur du kopierar data från Spark till mottagar data lager med stöd för en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 0a31ed7a8df080c0e1186ed75f325e36aff32920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388829"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Kopiera data från Spark med Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Spark. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Spark-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Spark till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning, och du behöver därför inte installera någon driv rutin manuellt med hjälp av den här anslutningen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Spark-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Spark-länkade tjänster:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen måste anges till: **Spark** | Ja |
| värd | IP-adressen eller värd namnet för Spark-servern  | Ja |
| port | TCP-porten som Spark-servern använder för att lyssna efter klient anslutningar. Om du ansluter till Azure HDInsights anger du port som 443. | Ja |
| serverType | Typen av Spark-Server. <br/>Tillåtna värden är: **SharkServer**, **SharkServer2**, **SparkThriftServer** | Inga |
| thriftTransportProtocol | Transport protokollet som ska användas i Thrift-skiktet. <br/>Tillåtna värden är: **Binary**, **sasl**, **http** | Inga |
| authenticationType | Autentiseringsmetoden som används för att få åtkomst till Spark-servern. <br/>Tillåtna värden är: **Anonym**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| användarnamn | Det användar namn som du använder för att få åtkomst till Spark-servern.  | Inga |
| password | Lösen ordet som motsvarar användaren. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| httpPath | Den partiella URL som motsvarar Spark-servern.  | Inga |
| enableSsl | Anger om anslutningarna till servern är krypterade med TLS. Standardvärdet är false.  | Inga |
| trustedCertPath | Den fullständiga sökvägen till. pem-filen som innehåller certifikat från betrodda certifikat utfärdare för att verifiera servern vid anslutning via TLS. Den här egenskapen kan bara anges när du använder TLS på IR med egen värd. Standardvärdet är den cacerts. PEM-fil som installeras med IR.  | Inga |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från systemets betrodda lager eller från en angiven PEM-fil. Standardvärdet är false.  | Inga |
| allowHostNameCNMismatch | Anger om ett CA-utfärdat TLS/SSL-certifikat namn ska matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är false.  | Inga |
| allowSelfSignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Spark DataSet.

Om du vill kopiera data från Spark anger du egenskapen type för data uppsättningen till **SparkObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SparkObject** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Spark-källan.

### <a name="spark-as-source"></a>Spark som källa

Om du vill kopiera data från Spark anger du käll typen i kopierings aktiviteten till **SparkSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SparkSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
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
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
