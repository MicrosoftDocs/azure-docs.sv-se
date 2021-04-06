---
title: Kopiera data från MongoDB Atlas
description: Lär dig hur du kopierar data från MongoDB Atlas till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368803"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Kopiera data från MongoDB Atlas med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en MongoDB Atlas-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB Atlas-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna MongoDB Atlas-anslutning **versioner upp till 4,2**.

## <a name="prerequisites"></a>Förutsättningar

Om du använder Azure Integration Runtime för kopiering, se till att du lägger till den gällande regionens Azure Integration Runtime IP- [adresser](azure-integration-runtime-ip-addresses.md) i MongoDB Atlas IP Access List.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för MongoDB Atlas-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade MongoDB-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **MongoDbAtlas** |Ja |
| Begär |Ange anslutnings strängen för MongoDB Atlas t. ex. `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` . <br/><br /> Du kan också ange en anslutnings sträng i Azure Key Vault. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) . |Ja |
| databas | Namnet på den databas som du vill få åtkomst till. | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för MongoDB Atlas-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **MongoDbAtlasCollection** | Ja |
| Samling |Namnet på samlingen i MongoDB Atlas-databasen. |Ja |

**Exempel:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB Atlas-källa.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **MongoDbAtlasSource** | Ja |
| filter | Anger urvals filter med hjälp av fråge operatorer. Om du vill returnera alla dokument i en samling utelämnar du den här parametern eller skickar ett tomt dokument ( {} ). | Inga |
| cursorMethods. Project | Anger de fält som ska returneras i dokument för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods. sort | Anger i vilken ordning som frågan returnerar matchande dokument. Se [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods. Limit | Anger det maximala antalet dokument som servern returnerar. Referera till [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga |
| cursorMethods. Skip | Anger antalet dokument som ska hoppas över och från där MongoDB Atlas börjar returnera resultat. Se [cursor. SKIP ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB Atlas-instansen. I de flesta fall kommer ändringar av batchstorleken inte att påverka användaren eller programmet. Cosmos DB gränser för varje batch får inte överstiga 40MB i storlek, vilket är summan av batchSize-antalet dokument storlek, så minska det här värdet om dokument storleken är stor. | Inga<br/>(Standardvärdet är **100**) |

>[!TIP]
>ADF-support använder BSON-dokument i **strikt läge**. Kontrol lera att filter frågan är i strikt läge i stället för Shell-läge. Du hittar mer information på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>Exportera JSON-dokument som-är

Du kan använda den här MongoDB Atlas-anslutningen för att exportera JSON-dokument som-är från en MongoDB-Atlas-samling till olika filbaserade butiker eller till Azure Cosmos DB. För att få en sådan oberoende kopia kan du hoppa över avsnittet "struktur" (kallas även *schema*) i data uppsättning och schema mappning i kopierings aktiviteten.

## <a name="schema-mapping"></a>Schemamappning

Information om hur du kopierar data från MongoDB Atlas till tabell mottagare finns i [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
