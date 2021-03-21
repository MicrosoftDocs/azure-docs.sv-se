---
title: Kopiera data från MongoDB
description: Lär dig hur du kopierar data från Mongo DB till mottagar data lager med stöd för en kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2021
ms.openlocfilehash: eae2d2adfe2cfdd7e47f2ace9ede9253003aa5bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368786"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiera data från MongoDB med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en MongoDB-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!IMPORTANT]
>ADF släpper den nya versionen av MongoDB Connector som ger bättre inbyggt stöd för MongoDB. Om du använder den tidigare MongoDB-anslutningen i din lösning som stöds för bakåtkompatibilitet, se [MongoDB Connector-artikeln (bakåtkompatibelt)](connector-mongodb-legacy.md) .


## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från MongoDB-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder denna MongoDB-anslutning **versioner upp till 4,2**.


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]


## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för MongoDB-anslutaren.


## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten MongoDB:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste anges till: **MongoDbV2** |Ja |
| Begär |Ange anslutnings strängen MongoDB, t. ex. `mongodb://[username:password@]host[:port][/[database][?options]]` . Mer information finns i [MongoDB manuell i anslutnings strängen](https://docs.mongodb.com/manual/reference/connection-string/) . <br/><br /> Du kan också ange en anslutnings sträng i Azure Key Vault. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) . |Ja |
| databas | Namnet på den databas som du vill få åtkomst till. | Ja |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Läs mer från avsnittet [krav](#prerequisites) . Om inget värde anges används standard Azure Integration Runtime. |Inga |

**Exempel:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **MongoDbV2Collection** | Ja |
| Samling |Namnet på samlingen i MongoDB-databasen. |Ja |

**Exempel:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av MongoDB-källan.

### <a name="mongodb-as-source"></a>MongoDB som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **MongoDbV2Source** | Ja |
| filter | Anger urvals filter med hjälp av fråge operatorer. Om du vill returnera alla dokument i en samling utelämnar du den här parametern eller skickar ett tomt dokument ( {} ). | Inga |
| cursorMethods. Project | Anger de fält som ska returneras i dokument för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods. sort | Anger i vilken ordning som frågan returnerar matchande dokument. Se [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods. Limit | Anger det maximala antalet dokument som servern returnerar. Referera till [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga |
| cursorMethods. Skip | Anger antalet dokument som ska hoppas över och från där MongoDB börjar returnera resultat. Se [cursor. SKIP ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB-instansen. I de flesta fall kommer ändringar av batchstorleken inte att påverka användaren eller programmet. Cosmos DB gränser för varje batch får inte överstiga 40 MB i storlek, vilket är summan av batchSize storlek, så minska det här värdet om dokument storleken är stor. | Inga<br/>(Standardvärdet är **100**) |

>[!TIP]
>ADF-support använder BSON-dokument i **strikt läge**. Kontrol lera att filter frågan är i strikt läge i stället för Shell-läge. Du hittar mer information på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

Du kan använda den här MongoDB-anslutningen för att exportera JSON-dokument från en MongoDB-samling till olika filbaserade butiker eller för att Azure Cosmos DB. För att få en sådan oberoende kopia kan du hoppa över avsnittet "struktur" (kallas även *schema*) i data uppsättning och schema mappning i kopierings aktiviteten.


## <a name="schema-mapping"></a>Schemamappning

Information om hur du kopierar data från MongoDB till tabell mottagare finns i [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).


## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
