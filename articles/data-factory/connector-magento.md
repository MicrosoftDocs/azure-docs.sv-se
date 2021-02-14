---
title: Kopiera data från Magento med Azure Data Factory (för hands version)
description: Lär dig hur du kopierar data från Magento till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 534a7cd8c9a40a807d05ef40b084a7c73c0a7710
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378476"
---
# <a name="copy-data-from-magento-using-azure-data-factory-preview"></a>Kopiera data från Magento med Azure Data Factory (för hands version)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Magento. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

> [!IMPORTANT]
> Den här kopplingen är för närvarande en för hands version. Du kan prova det och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Magento-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Magento till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning, och du behöver därför inte installera någon driv rutin manuellt med hjälp av den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Magento-anslutaren.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Magento:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Magento** | Ja |
| värd | URL: en för Magento-instansen. (dvs 192.168.222.110/magento3)  | Ja |
| accessToken | Åtkomsttoken från Magento. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värd namnet i Server certifikatet måste matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är True.  | Inga |
| usePeerVerification | Anger om du vill verifiera serverns identitet vid anslutning via TLS. Standardvärdet är True.  | Inga |

**Exempel:**

```json
{
    "name": "MagentoLinkedService",
    "properties": {
        "type": "Magento",
        "typeProperties": {
            "host" : "192.168.222.110/magento3",
            "accessToken": {
                "type": "SecureString",
                "value": "<accessToken>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Magento DataSet.

Om du vill kopiera data från Magento anger du egenskapen type för data uppsättningen till **MagentoObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **MagentoObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "MagentoDataset",
    "properties": {
        "type": "MagentoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Magento linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Magento-källan.

### <a name="magento-as-source"></a>Magento som källa

Om du vill kopiera data från Magento anger du käll typen i kopierings aktiviteten till **MagentoSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **MagentoSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM Customers"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMagento",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Magento input dataset name>",
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
                "type": "MagentoSource",
                "query": "SELECT * FROM Customers where Id > XXX"
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
