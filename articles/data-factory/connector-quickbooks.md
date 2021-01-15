---
title: Kopiera data från QuickBooks online med Azure Data Factory (för hands version)
description: Lär dig hur du kopierar data från QuickBooks online till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: ecdb0e55aa7127a373e63612908ed58109c1f8e2
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233176"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Kopiera data från QuickBooks online med Azure Data Factory (för hands version)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från QuickBooks online. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

> [!IMPORTANT]
> Den här kopplingen är för närvarande en för hands version. Du kan prova det och ge oss feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här QuickBooks-kopplingen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från QuickBooks online till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Den här anslutningen stöder QuickBooks OAuth 2,0-autentisering.

## <a name="getting-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för QuickBooks Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för QuickBooks-länkade tjänster:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **QuickBooks** | Ja |
| connectionProperties | En grupp egenskaper som definierar hur du ansluter till QuickBooks. | Ja |
| **_Under `connectionProperties` :_* _ | | |
| slutpunkt | Slut punkten för QuickBooks Online-servern. (det vill säga quickbooks.api.intuit.com)  | Ja |
| companyId | Företags-ID för QuickBooks-företaget som ska auktoriseras. Information om hur du hittar företags-ID finns i [Hur gör jag för att hitta mitt företags-ID](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Ja |
| consumerKey | Klient-ID för ditt QuickBooks online-program för OAuth 2,0-autentisering. Läs mer [här](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). | Ja |
| consumerSecret | Klient hemligheten för ditt QuickBooks online-program för OAuth 2,0-autentisering. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| refreshToken | OAuth 2,0-uppdateringstoken som är associerad med QuickBooks-programmet. Läs mer [här](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). Observera att uppdateringstoken upphör att gälla efter 180 dagar. Kunden måste regelbundet uppdatera uppdateringstoken. <br/>Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).| Ja |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | Nej |

_ *Exempel:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av QuickBooks-datauppsättningen.

Om du vill kopiera data från QuickBooks online anger du egenskapen type för data uppsättningen till **QuickBooksObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **QuickBooksObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av QuickBooks-källan.

### <a name="quickbooks-as-source"></a>QuickBooks som källa

Om du vill kopiera data från QuickBooks online anger du käll typen i kopierings aktiviteten till **QuickBooksSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **QuickBooksSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Kopiera data från QuickBooks Desktop

Kopierings aktiviteten i Azure Data Factory kan inte kopiera data direkt från QuickBooks Desktop. Om du vill kopiera data från QuickBooks Desktop exporterar du dina QuickBooks-data till en fil med kommaavgränsade värden (CSV) och laddar sedan upp filen till Azure Blob Storage. Därifrån kan du använda Data Factory för att kopiera data till den mottagare du väljer.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
