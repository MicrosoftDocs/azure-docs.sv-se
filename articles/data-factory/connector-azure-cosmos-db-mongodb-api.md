---
title: Kopiera data från Azure Cosmos DBs API för MongoDB
description: Lär dig hur du kopierar data från käll data lager som stöds till eller från Azure Cosmos DBs API för MongoDB till mottagar lager som stöds med hjälp av Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 6f1e865daf9ba42126c0f8a341a54d87ac7f374a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393096"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Cosmos DB API för MongoDB med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory till att kopiera data från och till Azure Cosmos DB:s API för MongoDB. Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

>[!NOTE]
>Den här kopplingen stöder endast kopiering av data till/från Azure Cosmos DB s API för MongoDB. SQL-API finns i [Cosmos DB SQL API-anslutning](connector-azure-cosmos-db.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DBs API för MongoDB till alla mottagar data lager som stöds, eller kopiera data från alla käll data lager som stöds till Azure Cosmos DB s API för MongoDB. En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DBs API för MongoDB-anslutning för att:

- Kopiera data från och till [Azure Cosmos DBS API för MongoDB](../cosmos-db/mongodb-introduction.md).
- Skriv till Azure Cosmos DB som **insert** eller **upsert**.
- Importera och exportera JSON-dokument som-är eller kopiera data från eller till en tabell data uppsättning. Exempel på detta är en SQL-databas och en CSV-fil. Om du vill kopiera dokument som de är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB samling, se Importera eller exportera JSON-dokument.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Azure Cosmos DBs API för MongoDB.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för Azure Cosmos DB: s API för den länkade tjänsten MongoDB:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **CosmosDbMongoDbApi**. | Ja |
| Begär |Ange anslutnings strängen för din Azure Cosmos DBs API för MongoDB. Du hittar det i Azure Portal-> Cosmos DB bladet > primär eller sekundär anslutnings sträng med mönstret `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` . <br/><br />Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) .|Ja |
| databas | Namnet på den databas som du vill få åtkomst till. | Ja |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller en egen värd för integration Runtime (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges används standard Azure Integration Runtime. |Inga |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper stöds för Azure Cosmos DB s API för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **CosmosDbMongoDbApiCollection**. |Ja |
| Samling |Namnet på den Azure Cosmos DB samlingen. |Ja |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som Azure Cosmos DBs API för MongoDB-källa och mottagar stöd.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB s API för MongoDB som källa

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **CosmosDbMongoDbApiSource**. |Ja |
| filter | Anger urvals filter med hjälp av fråge operatorer. Om du vill returnera alla dokument i en samling utelämnar du den här parametern eller skickar ett tomt dokument ( {} ). | Inga |
| cursorMethods. Project | Anger de fält som ska returneras i dokument för projektion. Om du vill returnera alla fält i de matchande dokumenten utelämnar du den här parametern. | Inga |
| cursorMethods. sort | Anger i vilken ordning som frågan returnerar matchande dokument. Se [cursor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Inga |
| cursorMethods. Limit |    Anger det maximala antalet dokument som servern returnerar. Referera till [cursor. Limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Inga | 
| cursorMethods. Skip | Anger antalet dokument som ska hoppas över och från där MongoDB börjar returnera resultat. Se [cursor. SKIP ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Inga |
| batchSize | Anger antalet dokument som ska returneras i varje batch av svaret från MongoDB-instansen. I de flesta fall kommer ändringar av batchstorleken inte att påverka användaren eller programmet. Cosmos DB gränser för varje batch får inte överstiga 40MB i storlek, vilket är summan av batchSize-antalet dokument storlek, så minska det här värdet om dokument storleken är stor. | Inga<br/>(Standardvärdet är **100**) |

>[!TIP]
>ADF-support använder BSON-dokument i **strikt läge**. Kontrol lera att filter frågan är i strikt läge i stället för Shell-läge. Du hittar mer information på [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB s API för MongoDB som mottagare

Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **CosmosDbMongoDbApiSink**. |Ja |
| writeBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **insert** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om det redan finns ett dokument med samma `_id` . annars infogar du dokumentet.<br /><br />**Obs!** Data Factory automatiskt genererar ett `_id` för ett dokument om inget `_id` anges i det ursprungliga dokumentet eller med kolumn mappning. Det innebär att du måste se till att ditt dokument har ett ID för att **upsert** ska fungera som förväntat. |Inga<br />(Standardvärdet är **insert**) |
| writeBatchSize | Egenskapen **writeBatchSize** styr storleken på dokument som ska skrivas i varje batch. Du kan prova att öka värdet för **writeBatchSize** för att förbättra prestandan och minska värdet om dokument storleken är stor. |Inga<br />(Standardvärdet är **10 000**) |
| writeBatchTimeout | Vänte tiden för att infoga batch-operationen ska avslutas innan den nådde tids gränsen. Det tillåtna värdet är TimeSpan. | Inga<br/>(Standardvärdet är **00:30:00** – 30 minuter) |

>[!TIP]
>Information om hur du importerar JSON-dokument finns i avsnittet [Importera eller exportera JSON-dokument](#import-and-export-json-documents) . Om du vill kopiera från tabellbaserade data, se [schema mappning](#schema-mapping).

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importera och exportera JSON-dokument

Du kan använda den här Azure Cosmos DB anslutningen för att enkelt:

* Kopiera dokument mellan två Azure Cosmos DB samlingar som de är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från MongoDB, Azure Blob Storage, Azure Data Lake Store och andra filbaserade arkiv som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade arkiv.

För att uppnå schema-oberoende kopia:

* När du använder Kopiera data-verktyget väljer du alternativet **Exportera som är till JSON-filer eller Cosmos DB samling** .
* När du använder redigering av aktiviteter väljer du JSON-format med motsvarande fil Arkiv för källa eller mottagare.

## <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från Azure Cosmos DBs API för MongoDB till tabell mottagare eller omvänd, referera till [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

För att du ska kunna skriva till Cosmos DB, för att se till att du fyller Cosmos DB med rätt objekt-ID från dina källdata, till exempel om du har en "ID"-kolumn i SQL Database-tabell och vill använda värdet som dokument-ID i MongoDB för INSERT/upsert, måste du ange rätt schema mappning enligt definitionen för MongoDB Strict mode ( `_id.$oid` ) som följer:

![Kart-ID i MongoDB-mottagare](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Efter körningen av kopierings aktiviteten skapas under BSON ObjectId i Sink:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).