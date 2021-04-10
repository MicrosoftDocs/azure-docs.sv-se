---
title: Kopiera och transformera data i Azure Cosmos DB (SQL API)
description: Lär dig hur du kopierar data till och från Azure Cosmos DB (SQL API) och transformera data i Azure Cosmos DB (SQL API) med hjälp av Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608078"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Cosmos DB (SQL API) med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktivitet i Azure Data Factory till att kopiera data från och till Azure Cosmos DB (SQL API) samt hur du använder dataflöden till att transformera data i Azure Cosmos DB (SQL API). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.



>[!NOTE]
>Den här anslutningen har endast stöd för Cosmos DB SQL API. För MongoDB-API, se [Connector för Azure Cosmos DB s API för MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Cosmos DB-anslutningen (SQL API) stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

För kopierings aktivitet stöder den här Azure Cosmos DB-anslutaren (SQL API):

- Kopiera data från och till Azure Cosmos DB [SQL API](../cosmos-db/introduction.md).
- Skriv till Azure Cosmos DB som **insert** eller **upsert**.
- Importera och exportera JSON-dokument som-är eller kopiera data från eller till en tabell data uppsättning. Exempel på detta är en SQL-databas och en CSV-fil. Om du vill kopiera dokument som-är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB samling, se [Importera och exportera JSON-dokument](#import-and-export-json-documents).

Data Factory integreras med [Azure Cosmos DB bulk utförar-biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att ge bästa möjliga prestanda när du skriver till Azure Cosmos dB.

> [!TIP]
> [Videomigreringen](https://youtu.be/5-SRNiC_qOU) vägleder dig genom stegen för att kopiera data från Azure Blob storage till Azure Cosmos dB. Videon beskriver också prestanda justerings överväganden för att mata in data till Azure Cosmos DB allmänt.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Azure Cosmos DB (SQL API):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **CosmosDb**. | Ja |
| Begär |Ange information som krävs för att ansluta till Azure Cosmos DB databasen.<br />**Obs!** du måste ange databas information i anslutnings strängen som visas i exemplen som följer. <br/> Du kan också ange konto nyckeln i Azure Key Vault och hämta `accountKey` konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller en egen värd för integration Runtime (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges används standard Azure Integration Runtime. |Inga |

**Exempel**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: lagrings konto nyckel i Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md).

Följande egenskaper stöds för data uppsättningen Azure Cosmos DB (SQL API): 

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **CosmosDbSqlApiCollection**. |Ja |
| Samling |Namnet på den Azure Cosmos DB dokument samlingen. |Ja |

Om du använder data uppsättningen "DocumentDbCollection" stöds den fortfarande för bakåtkompatibilitet för kopierings-och söknings aktiviteter, men det stöds inte för data flöden. Du rekommenderas att använda den nya modellen som går framåt.

**Exempel**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-källan (SQL API) och mottagar tjänsten. En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) som källa

Om du vill kopiera data från Azure Cosmos DB (SQL API) anger du **käll** typen i kopierings aktivitet till **DocumentDbCollectionSource**. 

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **CosmosDbSqlApiSource**. |Ja |
| DocumentDB |Ange Azure Cosmos DB fråga för att läsa data.<br/><br/>Exempel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om detta inte anges körs SQL-instruktionen: `select <columns defined in structure> from mycollection` |
| preferredRegions | Den önskade listan över regioner att ansluta till när data hämtas från Cosmos DB. | Inga |
| pageSize | Antalet dokument per sida i frågeresultatet. Standardvärdet är "-1", vilket innebär att den dynamiska sid storleken på tjänst sidan är upp till 1000. | Inga |
| detectDatetime | Anger om datetime ska identifieras från sträng värden i dokumenten. Tillåtna värden är: **Sant** (standard), **falskt**. | Inga |

Om du använder typen "DocumentDbCollectionSource"-källa stöds den fortfarande för bakåtkompatibilitet. Du rekommenderas att använda den nya modellen som går framåt, vilket ger rikare funktioner för att kopiera data från Cosmos DB.

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

När du kopierar data från Cosmos DB, om du inte vill [Exportera JSON-dokument i befintligt skick](#import-and-export-json-documents), är det bästa sättet att ange mappningen i kopierings aktiviteten. Data Factory följer den mappning som du angav i aktiviteten – om en rad inte innehåller något värde för en kolumn, anges ett null-värde för kolumnvärdet. Om du inte anger någon mappning, Data Factory härleder schemat genom att använda den första raden i data. Om den första raden inte innehåller det fullständiga schemat, kommer vissa kolumner saknas i resultatet av aktivitets åtgärden.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) som mottagare

Om du vill kopiera data till Azure Cosmos DB (SQL API) anger du **mottagar** typen i kopierings aktivitet till **DocumentDbCollectionSink**. 

Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **CosmosDbSqlApiSink**. |Ja |
| writeBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **insert** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om det redan finns ett dokument med samma ID. annars infogar du dokumentet.<br /><br />**Obs!** Data Factory automatiskt genererar ett ID för ett dokument om inget ID anges i det ursprungliga dokumentet eller med kolumn mappning. Det innebär att du måste se till att ditt dokument har ett ID för att **upsert** ska fungera som förväntat. |Inga<br />(Standardvärdet är **insert**) |
| writeBatchSize | Data Factory använder [Azure Cosmos DB bulk utförar-biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att skriva data till Azure Cosmos dB. Egenskapen **writeBatchSize** styr storleken på dokument som ADF tillhandahåller till biblioteket. Du kan prova att öka värdet för **writeBatchSize** för att förbättra prestandan och minska värdet om dokument storleken är stor, se tipsen nedan. |Inga<br />(Standardvärdet är **10 000**) |
| disableMetricsCollection | Data Factory samlar in mått som Cosmos DB ru: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger `true` du för att inaktivera det. | Nej (standard är `false` ) |
| maxConcurrentConnections |Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| Inga |


>[!TIP]
>Information om hur du importerar JSON-dokument finns i avsnittet [Importera eller exportera JSON-dokument](#import-and-export-json-documents) . Om du vill kopiera från tabellbaserade data, se [Migrera från Relations databas till Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är en begär ande storlek = enskild dokument storlek * Skriv batchstorleken. Om du klickar på fel som säger att **"begäran är för stor"**, **minskar du `writeBatchSize` värdet** i Kopiera Sink-konfigurationen.

Om du använder typen "DocumentDbCollectionSink"-källa stöds den fortfarande för bakåtkompatibilitet. Du rekommenderas att använda den nya modellen som går framåt, vilket ger rikare funktioner för att kopiera data från Cosmos DB.

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Schemamappning

Om du vill kopiera data från Azure Cosmos DB till tabell mottagare eller omvänd, se [schema mappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till samlingar i Cosmos DB. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden.

### <a name="source-transformation"></a>Käll omvandling

Inställningar som är aktuella för Azure Cosmos DB finns tillgängliga på fliken **käll alternativ** i käll omvandlingen. 

**Inkludera system kolumner:** Om sant, ```id``` , ```_ts``` och andra system kolumner inkluderas i metadata för data flödet från CosmosDB. När du uppdaterar samlingar är det viktigt att ta med detta så att du kan ta tag i det befintliga rad-ID: t.

**Sid storlek:** Antalet dokument per sida i frågeresultatet. Standardvärdet är "-1" som använder den dynamiska sidan för tjänsten upp till 1000.

**Data flöde:** Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet under Läs åtgärden. Minimum är 400.

**Prioriterade regioner:** Välj önskade Läs regioner för den här processen.

#### <a name="json-settings"></a>JSON-inställningar

**Enstaka dokument:** Välj det här alternativet om ADF ska behandla hela filen som ett enda JSON-dokument.

**Icke-citerade kolumn namn:** Välj det här alternativet om kolumn namn i JSON inte anges som citerade.

**Har kommentarer:** Använd det här alternativet om dina JSON-dokument har kommentarer i data.

**Enkelt citat tecken:** Välj det här alternativet om kolumnerna och värdena i dokumentet är citat tecken med enkla citat tecken.

**Omvänt snedstreck Escaped:** Välj det här alternativet om du använder omvända snedstreck för att kringgå tecken i JSON.

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är tillgängliga för Azure Cosmos DB finns på fliken **Inställningar** i omvandlingen för mottagare.

**Uppdaterings metod:** Avgör vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar. Om du vill uppdatera, upsert eller ta bort rader krävs en Alter-Row-omvandling för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Samlings åtgärd:** Bestämmer om du vill återskapa mål samlingen innan du skriver.
* Ingen: ingen åtgärd utförs för samlingen.
* Återskapa: samlingen tas bort och återskapas

**Batchstorlek**: ett heltal som representerar hur många objekt som skrivs till Cosmos DB samlingen i varje batch. Normalt räcker det att börja med standard storleken på batch. Observera följande om du vill finjustera det här värdet:

- Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är "begär storlek = enskild dokument storlek * batchstorlek". Om du trycker på fel som säger att "begär ande storleken är för stor" minskar du värdet för batch-storlek.
- Ju större batchstorlek, desto bättre data flödes-ADF kan uppnå, samtidigt som du ser till att du allokerar tillräckligt med ru: er för att ge din arbets belastning.

**Partitionsnyckel:** Ange en sträng som representerar partitionens partitionsnyckel för din samling. Exempel: ```/movies/title```

**Data flöde:** Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet. Minimum är 400.

**Skriv data flödes budget:** Ett heltal som representerar den ru: er som du vill allokera för den här data flödets Skriv åtgärd, från det totala data flödet som allokerats till samlingen.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importera och exportera JSON-dokument

Du kan enkelt använda den här Azure Cosmos DBs anslutningen (SQL API) för att:

* Kopiera dokument mellan två Azure Cosmos DB samlingar som de är.
* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från Azure Blob Storage, Azure Data Lake Store och andra filbaserade arkiv som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade arkiv.

För att uppnå schema-oberoende kopia:

* När du använder Kopiera data-verktyget väljer du alternativet **Exportera som är till JSON-filer eller Cosmos DB samling** .
* När du använder redigering av aktiviteter väljer du JSON-format med motsvarande fil Arkiv för källa eller mottagare.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrera från Relations databas till Cosmos DB

När du migrerar från en Relations databas, t. ex. SQL Server Azure Cosmos DB, kan kopierings aktiviteten enkelt mappa tabell data från källa för att förenkla JSON-dokument i Cosmos DB. I vissa fall kanske du vill utforma om data modellen för att optimera den för NoSQL användnings fall enligt [data modellering i Azure Cosmos DB](../cosmos-db/modeling-data.md), till exempel för att avnormalisera data genom att bädda in alla relaterade under objekt i ett JSON-dokument. I det här fallet kan du läsa [den här artikeln](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) med en genom gång av hur du uppnår det med hjälp av Azure Data Factory kopierings aktivitet.

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).