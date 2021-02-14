---
title: Kopiera data till Sök index
description: Lär dig mer om att skicka eller kopiera data till ett Azure Search-index med hjälp av kopierings aktiviteten i en Azure Data Factory pipeline.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 8c0fe30961e8ca0f31374bfdb5c5f17d58cb7673
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385803"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopiera data till ett Azure Kognitiv sökning-index med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Aktuell version](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till Azure Kognitiv sökning index. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla käll data lager som stöds till Sök index. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Kognitiv sökning Connector.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Azure Kognitiv sökning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **AzureSearch** | Ja |
| url | URL för Sök tjänsten. | Ja |
| key | Administratörs nyckel för Sök tjänsten. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller egen värd Integration Runtime (om ditt data lager finns i privat nätverk). Om inget värde anges används standard Azure Integration Runtime. |Inga |

> [!IMPORTANT]
> När du kopierar data från ett moln data lager till sökindexet i Azure Kognitiv sökning länkade tjänsten måste du referera till en Azure Integration Runtime med en explicit region i connactVia. Ange regionen som den plats där Sök tjänsten finns. Läs mer från [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Exempel:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Kognitiv sökning DataSet.

Följande egenskaper stöds för att kopiera data till Azure Kognitiv sökning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **AzureSearchIndex** | Ja |
| indexName | Sök Indexets namn. Data Factory skapar inte indexet. Indexet måste finnas i Azure Kognitiv sökning. | Ja |

**Exempel:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Kognitiv sökning Source.

### <a name="azure-cognitive-search-as-sink"></a>Azure Kognitiv sökning som mottagare

Om du vill kopiera data till Azure Kognitiv sökning anger du käll typen i kopierings aktiviteten till **AzureSearchIndexSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **AzureSearchIndexSink** | Ja |
| writeBehavior | Anger om du vill sammanfoga eller ersätta när ett dokument redan finns i indexet. Se [egenskapen WriteBehavior](#writebehavior-property).<br/><br/>Tillåtna värden är: **sammanfoga** (standard) och **Ladda upp**. | Inga |
| writeBatchSize | Överför data till Sök indexet när buffertstorleken når writeBatchSize. Mer information finns i [WriteBatchSize-egenskapen](#writebatchsize-property) .<br/><br/>Tillåtna värden är: heltal 1 till 1 000; Standardvärdet är 1000. | Inga |

### <a name="writebehavior-property"></a>WriteBehavior-egenskap

AzureSearchSink upsertar när data skrivs. När du skriver ett dokument, och om dokument nyckeln redan finns i Sök indexet, kommer Azure Kognitiv sökning att uppdatera det befintliga dokumentet i stället för att ett konflikt undantag utlöses.

AzureSearchSink innehåller följande två upsert-beteenden (med hjälp av AzureSearch SDK):

- **Sammanslagning**: kombinera alla kolumner i det nya dokumentet med det befintliga. För kolumner med null-värde i det nya dokumentet bevaras värdet i det befintliga.
- **Ladda upp**: det nya dokumentet ersätter det befintliga. För kolumner som inte anges i det nya dokumentet anges värdet null om det inte finns något värde som inte är null i det befintliga dokumentet eller inte.

Standard beteendet **slås samman**.

### <a name="writebatchsize-property"></a>WriteBatchSize-egenskap

Azure Kognitiv sökning-tjänsten har stöd för skrivning av dokument som en batch. En batch kan innehålla 1 till 1 000-åtgärder. En åtgärd hanterar ett dokument för att utföra uppladdnings-/sammanslagnings åtgärden.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Data typs stöd

I följande tabell anges om data typen Azure Kognitiv sökning stöds eller inte.

| Data typen Azure Kognitiv sökning | Stöds i Azure Kognitiv sökning-mottagare |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Sträng mat ris | N |
| GeographyPoint | N |

För närvarande stöds inte andra data typer, t. ex. ComplexType. En fullständig lista över data typer som stöds av Azure Kognitiv sökning finns i [data typer som stöds (Azure kognitiv sökning)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).