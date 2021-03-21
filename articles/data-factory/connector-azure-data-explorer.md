---
title: Kopiera data till eller från Azure Datautforskaren
description: Lär dig hur du kopierar data till eller från Azure Datautforskaren med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379989"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Datautforskaren med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till eller från [Azure datautforskaren](/azure/data-explorer/data-explorer-overview). Den bygger på artikeln [Kopiera aktivitets översikt](copy-activity-overview.md) , som innehåller en översikt över kopierings aktiviteten.

>[!TIP]
>För Azure Data Factory och Azure Datautforskaren-integrering i allmänhet kan du läsa mer i [integrera Azure-datautforskaren med Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Datautforskaren-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från alla käll data lager som stöds till Azure Datautforskaren. Du kan också kopiera data från Azure Datautforskaren till alla mottagar data lager som stöds. En lista över data lager som kopierings aktiviteten stöder som källor eller mottagare finns i tabellen [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>Det går att kopiera data till eller från Azure Datautforskaren via ett lokalt data lager med hjälp av integration runtime med egen värd i version 3,14 och senare.

Med Azure Datautforskaren-anslutningen kan du göra följande:

* Kopiera data med hjälp av Azure Active Directory (Azure AD) Application token-autentisering med ett **huvud namn för tjänsten**.
* Som källa hämtar du data med hjälp av en KQL-fråga (Kusto).
* Lägg till data i en mål tabell som mottagare.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genom gång av Azure Datautforskaren Connector finns i [Kopiera data till/från Azure datautforskaren med Azure Data Factory](/azure/data-explorer/data-factory-load-data) och [Mass kopiering från en databas till Azure datautforskaren](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Datautforskaren Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Azure Datautforskaren-anslutningen använder autentisering av tjänstens huvud namn. Följ de här stegen för att hämta ett huvud namn för tjänsten och bevilja behörigheter:

1. Registrera en program enhet i Azure Active Directory genom att följa stegen i [Registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klient-ID:t

2. Ge tjänstens huvud namn rätt behörigheter i Azure Datautforskaren. Mer detaljerad information om roller och behörigheter och om att hantera behörigheter finns i [Hantera behörigheter för Azure datautforskaren Database](/azure/data-explorer/manage-database-permissions) . I allmänhet måste du:

    - **Som källa**, beviljar du minst rollen **databas hanterare** till din databas
    - **Som mottagare**, beviljar du minst rollen **databas** inmatnings roll till din databas

>[!NOTE]
>När du använder Data Factory gränssnittet för att redigera, används ditt inloggnings användar konto för att Visa Azure Datautforskaren-kluster, databaser och tabeller. Ange namnet manuellt om du inte har behörighet för de här åtgärderna.

Följande egenskaper stöds för den länkade Azure Datautforskaren-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AzureDataExplorer**. | Ja |
| slutpunkt | Slut punkts-URL för Azure Datautforskaren-klustret, med formatet `https://<clusterName>.<regionName>.kusto.windows.net` . | Ja |
| databas | Namn på databasen. | Ja |
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Detta kallas "auktoritets-ID" i [Kusto anslutnings sträng](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Hämta den genom att hovra med mus pekaren i det övre högra hörnet av Azure Portal. | Ja |
| servicePrincipalId | Ange programmets klient-ID. Detta kallas "AAD-programklient-ID" i [Kusto anslutnings sträng](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ja |
| servicePrincipalKey | Ange programmets nyckel. Detta kallas "AAD program Key" i Kusto- [anslutningssträngen](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till säkra data som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel på länkade tjänst egenskaper:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns [i data uppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). I det här avsnittet visas egenskaper som stöds av Azure Datautforskaren-datauppsättningen.

Om du vill kopiera data till Azure Datautforskaren anger du egenskapen type för data uppsättningen till **AzureDataExplorerTable**.

Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AzureDataExplorerTable**. | Ja |
| tabell | Namnet på den tabell som den länkade tjänsten refererar till. | Ja för mottagare; Nej för källa |

**Exempel på data uppsättnings egenskaper:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som Azure Datautforskaren-källor och stöd för mottagare.

### <a name="azure-data-explorer-as-source"></a>Azure Datautforskaren som källa

Om du vill kopiera data från Azure Datautforskaren anger du egenskapen **Type** i kopierings aktivitetens källa till **AzureDataExplorerSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till: **AzureDataExplorerSource** | Ja |
| DocumentDB | En skrivskyddad begäran har angetts i ett [KQL-format](/azure/kusto/query/). Använd den anpassade KQL-frågan som referens. | Ja |
| queryTimeout | Vänte tiden innan fråge förfrågningen nådde tids gränsen. Standardvärdet är 10 min (00:10:00); det högsta tillåtna värdet är 1 timme (01:00:00). | Inga |
| notrunkering | Anger om den returnerade resultat uppsättningen ska trunkeras. Som standard trunkeras resultatet efter 500 000 poster eller 64 megabyte (MB). Trunkering rekommenderas starkt för att säkerställa rätt beteende för aktiviteten. |Inga |

>[!NOTE]
>Som standard har Azure Datautforskaren-källan en storleks gräns på 500 000 poster eller 64 MB. Om du vill hämta alla poster utan trunkering kan du ange `set notruncation;` i början av frågan. Mer information finns i [begränsningar för frågor](/azure/kusto/concepts/querylimits).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Datautforskaren som mottagare

Om du vill kopiera data till Azure Datautforskaren anger du egenskapen type i kopierings aktiviteten Sink till **AzureDataExplorerSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till: **AzureDataExplorerSink**. | Ja |
| ingestionMappingName | Namnet på en i förväg skapad [mappning](/azure/kusto/management/mappings#csv-mapping) i en Kusto-tabell. För att mappa kolumnerna från källa till Azure Datautforskaren (som gäller för [Alla käll Arkiv och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats), inklusive CSV/JSON/Avro-format), kan du använda [kolumn mappningen](copy-activity-schema-and-type-mapping.md) kopiera aktivitet (implicit efter namn eller uttryckligen konfigurerad) och/eller Azure datautforskaren-mappningar. | Inga |
| additionalProperties | En egenskaps uppsättning som kan användas för att ange någon av de inmatnings egenskaper som inte redan anges av Azure Datautforskaren-mottagare. Mer specifikt kan det vara praktiskt att ange inmatnings etiketter. Läs mer från [Azure Data utforska data inmatnings dokument](/azure/data-explorer/ingestion-properties). | Inga |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Mer information om egenskaperna finns i [Lookup Activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg

* En lista över data lager som kopierings aktiviteten i Azure Data Factory stöder som källor och mottagare finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

* Läs mer om hur du [kopierar data från Azure Data Factory till Azure datautforskaren](/azure/data-explorer/data-factory-load-data).