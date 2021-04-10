---
title: Kopiera data från Presto med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Presto till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 33e521d418c219be8eb85b79a0e07d999edb1b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374277"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Kopiera data från Presto med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Presto. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Presto-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Presto till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning, och du behöver därför inte installera någon driv rutin manuellt med hjälp av den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Presto-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Presto:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Presto** | Ja |
| värd | IP-adressen eller värd namnet för Presto-servern. (t. ex. 192.168.222.160)  | Ja |
| serverVersion | Presto-serverns version. (t. ex. 0,148-t)  | Ja |
| katalogen | Katalog kontexten för all begäran mot servern.  | Ja |
| port | TCP-porten som Presto-servern använder för att lyssna efter klient anslutningar. Standardvärdet är 8080.  | Inga |
| authenticationType | Den autentiseringsmekanism som används för att ansluta till Presto-servern. <br/>Tillåtna värden är: **Anonym**, **LDAP** | Ja |
| användarnamn | Det användar namn som används för att ansluta till Presto-servern.  | Inga |
| password | Lösen ordet som motsvarar användar namnet. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| enableSsl | Anger om anslutningarna till servern är krypterade med TLS. Standardvärdet är false.  | Inga |
| trustedCertPath | Den fullständiga sökvägen till. pem-filen som innehåller certifikat från betrodda certifikat utfärdare för att verifiera servern vid anslutning via TLS. Den här egenskapen kan bara anges när du använder TLS på IR med egen värd. Standardvärdet är den cacerts. PEM-fil som installeras med IR.  | Inga |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från systemets betrodda lager eller från en angiven PEM-fil. Standardvärdet är false.  | Inga |
| allowHostNameCNMismatch | Anger om ett CA-utfärdat TLS/SSL-certifikat namn ska matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är false.  | Inga |
| allowSelfSignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| timeZoneID | Den lokala tids zonen som används av anslutningen. Giltiga värden för det här alternativet anges i IANA-databasen för tids zoner. Standardvärdet är systemets tidszon.  | Inga |

**Exempel:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Presto DataSet.

Om du vill kopiera data från Presto anger du egenskapen type för data uppsättningen till **PrestoObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **PrestoObject** | Ja |
| schema | Schemats namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Namnet på tabellen med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och `table` för nya arbets belastningar. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Presto-källan.

### <a name="presto-as-source"></a>Presto som källa

Om du vill kopiera data från Presto anger du käll typen i kopierings aktiviteten till **PrestoSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **PrestoSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
