---
title: Kopiera data från Salesforce marknadsförings moln
description: Lär dig hur du kopierar data från Salesforce Marketing-moln till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369755"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopiera data från Salesforce Marketing Cloud med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från Salesforce marknadsförings molnet. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Salesforce Marketing Cloud Connector stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Salesforce Marketing Cloud till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Salesforce Marketing Cloud Connector stöder OAuth 2-autentisering och stöder både äldre och utökat paket typer. Anslutningen är byggd ovanpå [REST API Salesforce Marketing Cloud](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Den här kopplingen stöder inte hämtning av anpassade objekt eller anpassade data tillägg.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopierings aktivitet med hjälp av .NET SDK, python SDK, Azure PowerShell, REST API eller Azure Resource Manager mall. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](quickstart-create-data-factory-dot-net.md) .

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Salesforce Marketing Cloud Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Salesforce Marketing Cloud:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SalesforceMarketingCloud** | Ja |
| connectionProperties | En grupp egenskaper som definierar hur du ansluter till Salesforce Marketing-molnet. | Ja |
| ***Under `connectionProperties` :*** | | |
| authenticationType | Anger vilken autentiseringsmetod som ska användas. Tillåtna värden är `Enhanced sts OAuth 2.0` eller `OAuth_2.0` .<br><br>Det äldre paketet för Salesforce Marketing Cloud stöder bara `OAuth_2.0` , medan de förbättrade paket behoven `Enhanced sts OAuth 2.0` . <br>Sedan den 1 augusti 2019 har Salesforce Marketing-molnet tagit bort möjligheten att skapa äldre paket. Alla nya paket är förstärkta paket. | Ja |
| värd | För förbättrat paket ska värden vara din [under domän](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) som representeras av en sträng på 28 tecken som börjar med bokstäverna "MC", t. ex. `mc563885gzs27c5t9-63k636ttgm` . <br>För gammalt paket anger du `www.exacttargetapis.com` . | Ja |
| ClientID | Det klient-ID som är associerat med Salesforce Marketing Cloud Application.  | Ja |
| clientSecret | Den klient hemlighet som är kopplad till Salesforce Marketing Cloud Application. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF, eller lagra hemligheten i Azure Key Vault och låta ADF Copy-aktivitet hämta från där när data kopieringen ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| useEncryptedEndpoints | Anger om data källans slut punkter krypteras med HTTPS. Standardvärdet är True.  | Inga |
| useHostVerification | Anger om värd namnet i Server certifikatet måste matcha värd namnet för servern vid anslutning via TLS. Standardvärdet är True.  | Inga |
| usePeerVerification | Anger om du vill verifiera serverns identitet vid anslutning via TLS. Standardvärdet är True.  | Inga |

**Exempel: använda utökad STS OAuth 2-autentisering för förbättrat paket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Exempel: använda OAuth 2-autentisering för ett äldre paket** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Om du använder den länkade tjänsten för Salesforce Marketing-molnet med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vidarebefordran som lägger till förbättrat paket stöd.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av data uppsättningen för Salesforce-marknadsföring.

Om du vill kopiera data från Salesforce Marketing-molnet anger du egenskapen type för data uppsättningen till **SalesforceMarketingCloudObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SalesforceMarketingCloudObject** | Ja |
| tableName | Tabellens namn. | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce marknadsförings moln källa.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce marknadsförings moln som källa

Om du vill kopiera data från Salesforce Marketing-molnet anger du käll typen i kopierings aktiviteten till **SalesforceMarketingCloudSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SalesforceMarketingCloudSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
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
