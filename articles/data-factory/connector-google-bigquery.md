---
title: Kopiera data från Google BigQuery med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Google BigQuery till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Data Factory-pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: e3fcaa6c1542578d983461623da743724a3114d9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389696"
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Kopiera data från Google BigQuery med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från Google BigQuery. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google BigQuery-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Google BigQuery till alla mottagar data lager som stöds. En lista över data lager som stöds som källor eller mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Data Factory innehåller en inbyggd driv rutin som möjliggör anslutning. Därför behöver du inte installera en driv rutin manuellt för att använda den här anslutningen.

>[!NOTE]
>Den här Google BigQuery-anslutningen är byggd ovanpå BigQuery-API: erna. Tänk på att BigQuery begränsar Max antalet inkommande begär Anden och tillämpar lämpliga kvoter per projekt, se [kvoter & gränser – API-begäranden](https://cloud.google.com/bigquery/quotas#api_requests). Se till att du inte utlöser för många samtidiga begär anden till kontot.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Google BigQuery-anslutningen.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Google BigQuery-tjänsten.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **GoogleBigQuery**. | Ja |
| projekt | Projekt-ID: t för det standard BigQuery-projekt som frågas mot.  | Ja |
| additionalProjects | En kommaavgränsad lista med projekt-ID: n för offentliga BigQuery-projekt att komma åt.  | Inga |
| requestGoogleDriveScope | Om du vill begära åtkomst till Google Drive. Att tillåta Google Drive Access aktiverar stöd för federerade tabeller som kombinerar BigQuery-data med data från Google Drive. Standardvärdet är **falskt**.  | Inga |
| authenticationType | OAuth 2,0-autentiseringsmekanismen som används för autentisering. ServiceAuthentication kan bara användas på egna värdbaserade Integration Runtime. <br/>Tillåtna värden är **UserAuthentication** och **ServiceAuthentication**. I avsnitt under den här tabellen hittar du fler egenskaper och JSON-exempel för de typerna av autentisering. | Ja |

### <a name="using-user-authentication"></a>Använda användarautentisering

Ange egenskapen "authenticationType" till **UserAuthentication** och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| ClientID | ID för programmet som används för att generera uppdateringstoken. | Inga |
| clientSecret | Hemlighet för det program som används för att generera uppdateringstoken. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| refreshToken | Den uppdateringstoken som hämtades från Google som används för att ge åtkomst till BigQuery. Lär dig hur du får en från att [Skaffa OAuth 2,0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) -åtkomsttoken och [den här Community-bloggen](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |

**Exempel:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Använda tjänsteautentisering

Ange egenskapen "authenticationType" till **ServiceAuthentication** och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt. Den här autentiseringstypen kan endast användas på egna värdbaserade Integration Runtime.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| e-post | E-postadressen för tjänst kontot som används för ServiceAuthentication. Den kan endast användas på egna värdbaserade Integration Runtime.  | Inga |
| keyFilePath | Den fullständiga sökvägen till nyckel filen. p12 som används för att autentisera tjänst kontots e-postadress. | Inga |
| trustedCertPath | Den fullständiga sökvägen till. pem-filen som innehåller certifikat från betrodda certifikat utfärdare som används för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara anges när du använder TLS på egen värd Integration Runtime. Standardvärdet är cacerts. pem-filen som installeras med integration Runtime.  | Inga |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från systemets betrodda lager eller från en angiven. PEM-fil. Standardvärdet är **falskt**.  | Inga |

**Exempel:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Google BigQuery-datauppsättningen.

Om du vill kopiera data från Google BigQuery anger du egenskapen type för data uppsättningen till **GoogleBigQueryObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **GoogleBigQueryObject** | Ja |
| data uppsättning | Namnet på Google BigQuery-datauppsättningen. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitets källan har angetts)  |
| tableName | Tabellens namn. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `dataset` belastning `table` . | Nej (om "fråga" i aktivitets källan har angetts) |

**Exempel**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av käll typen Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource som typ av källa

Om du vill kopiera data från Google BigQuery anger du käll typen i kopierings aktiviteten till **GoogleBigQuerySource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **GoogleBigQuerySource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om "tableName" i data uppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
