---
title: Konfigurera och använda Azure Synapse Link för Azure Cosmos DB
description: Lär dig hur du aktiverar Synapse-länken för Azure Cosmos DB konton, skapar en behållare med analytiskt Arkiv aktiverat, ansluter Azure Cosmos-databasen till Synapse-arbetsytan och kör frågor.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: 7accf705ee64be5ba416be9df2f92693b07e8a8c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799657"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db"></a>Konfigurera och använda Azure Synapse Link för Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

[Azure Synapse-länken för Azure Cosmos DB](synapse-link.md) är en molnbaserad hybrid transaktions-och analys bearbetnings funktion (HTAP) som gör att du kan köra nära real tids analys över drifts data i Azure Cosmos dB. Synapse-länken skapar en tätt sömlös integrering mellan Azure Cosmos DB och Azure Synapse Analytics.

Azure Synapse-länken är tillgänglig för Azure Cosmos DB SQL API-behållare eller Azure Cosmos DB-API för mongo DB-samlingar. Använd följande steg för att köra analytiska frågor med Azure Synapse-länken för Azure Cosmos DB:

* [Aktivera Synapse-länk för dina Azure Cosmos DB-konton](#enable-synapse-link)
* [Skapa ett analys lager som är aktiverat Azure Cosmos DB container](#create-analytical-ttl)
* [Valfritt-uppdatera analytiskt lagrings-TTL för en Azure Cosmos DB container](#update-analytical-ttl)
* [Anslut Azure Cosmos DB-databasen till en Synapse-arbetsyta](#connect-to-cosmos-database)
* [Fråga analys lagret med hjälp av Synapse Spark](#query-analytical-store-spark)
* [Fråga analys lagret med Server lös SQL-pool](#query-analytical-store-sql-on-demand)
* [Använd Server lös SQL-pool för att analysera och visualisera data i Power BI](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Aktivera Azure Synapse-länk för Azure Cosmos DB-konton

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. [Skapa ett nytt Azure-konto](create-sql-api-dotnet.md#create-account)eller Välj ett befintligt Azure Cosmos DB-konto.

1. Gå till ditt Azure Cosmos DB-konto och öppna fönstret **funktioner** .

1. Välj **Synapse länk** från listan funktioner.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Funktionen Hitta Synapse-länk":::

1. Härnäst kommer du att bli ombedd att aktivera Synapse-länken på ditt konto. Välj **Aktivera**. Den här processen kan ta 1 till 5 minuter att slutföra.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Aktivera Synapse länk funktion":::

1. Ditt konto har nu Aktiver ATS för att använda Synapse-länken. Härnäst lär du dig hur du skapar ett analytiskt Arkiv med aktiverade behållare för att automatiskt starta replikeringen av dina drift data från transaktions arkivet till analys lagret.

> [!NOTE]
> När du aktiverar Synapse-länken aktive ras inte analys lagret automatiskt. När du har aktiverat Synapse-länken i Cosmos DB-kontot aktiverar du analys lager på behållare när du skapar dem, för att börja replikera dina åtgärds data till ett analys lager. 

### <a name="azure-cli"></a>Azure CLI

Följande länkar visar hur du aktiverar Synapse-länken med hjälp av Azure CLI:

* [Skapa ett nytt Azure Cosmos DB konto med Synapse-länk aktiverat](https://docs.microsoft.com/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create-optional-parameters&preserve-view=true)
* [Uppdatera ett befintligt Azure Cosmos DB-konto för att aktivera Synapse-länk](https://docs.microsoft.com/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_update-optional-parameters&preserve-view=true)

### <a name="powershell"></a>PowerShell

* [Skapa ett nytt Azure Cosmos DB konto med Synapse-länk aktiverat](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount?view=azps-5.5.0#description&preserve-view=true)
* [Uppdatera ett befintligt Azure Cosmos DB-konto för att aktivera Synapse-länk](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccount?view=azps-5.5.0&preserve-view=true)


Följande länkar visar hur du aktiverar Synapse-länken med hjälp av PowerShell:

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Skapa en Azure Cosmos-behållare med analys lager

Du kan aktivera analys lager på en Azure Cosmos-behållare när du skapar behållaren. Du kan använda Azure Portal eller konfigurera `analyticalTTL` egenskapen vid skapande av behållare med hjälp av Azure Cosmos DB SDK: er.

> [!NOTE]
> För närvarande kan du aktivera analys lagring för **nya** behållare (både i nya och befintliga konton). Du kan migrera data från dina befintligt-behållare till nya behållare med hjälp av [Azure Cosmos DB Migreringsverktyg.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/) eller [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Gå till ditt Azure Cosmos DB-konto och öppna fliken **datautforskaren** .

1. Välj **ny behållare** och ange ett namn för din databas, behållare, partitionsnyckel och data flödes information. Aktivera alternativet **analys Arkiv** . När du har aktiverat analys lagret skapas en behållare med `AnalyicalTTL` egenskapen inställd på standardvärdet-1 (oändlig kvarhållning). Detta analys lager behåller alla tidigare versioner av poster.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Aktivera analys lager för Azure Cosmos-behållare":::

1. Om du tidigare inte har aktiverat Synapse-länken för det här kontot kommer du att uppmanas att göra det eftersom det är ett krav för att skapa en aktive rad behållare för analys lager. Om du uppmanas väljer du **Aktivera Synapse länk**. Den här processen kan ta 1 till 5 minuter att slutföra.

1. Välj **OK** för att skapa en Azure Cosmos-behållare som är analytiskt lagrad.

1. När behållaren har skapats kontrollerar du att analys lagret har Aktiver ATS genom att klicka på **Inställningar**, högerklicka på dokument i datautforskaren och kontrol lera om alternativet **analys lager Time to Live** är aktiverat.

### <a name="net-sdk"></a>.NET SDK

Följande kod skapar en behållare med analytisk lagring med hjälp av .NET SDK. Ställ in egenskapen analytisk TTL på det obligatoriska värdet. Listan över tillåtna värden finns i artikeln [analytiska TTL-värden som stöds](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to -1 (infinite retention)
ContainerProperties properties = new ContainerProperties()
{
    Id = "myContainerId",
    PartitionKeyPath = "/id",
    AnalyticalStoreTimeToLiveInSeconds = -1,
};
CosmosClient cosmosClient = new CosmosClient("myConnectionString");
await cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(properties);
```

### <a name="java-v4-sdk"></a>Java v4 SDK

Följande kod skapar en behållare med analytisk lagring med hjälp av Java v4 SDK. Ställ in `AnalyticalStoreTimeToLiveInSeconds` egenskapen på det obligatoriska värdet:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Python v4 SDK

Python 2,7 och Azure Cosmos DB SDK-4.1.0 är de lägsta versionerna som krävs och SDK är bara kompatibelt med SQL-API: et.

Det första steget är att se till att du använder minst version 4.1.0 av [Azure Cosmos DB python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
Nästa steg skapar en behållare med analys lager med hjälp av Azure Cosmos DB python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="azure-cli"></a>Azure CLI

Följande länkar visar hur du skapar ett analytiskt lager med aktiverade behållare med hjälp av Azure CLI:

* [Azure Cosmos DB-API för mongo DB](https://docs.microsoft.com/cli/azure/cosmosdb/mongodb/collection?view=azure-cli-latest#az_cosmosdb_mongodb_collection_create-examples&preserve-view=true)
* [Azure Cosmos DB SQL API](https://docs.microsoft.com/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_create&preserve-view=true)

### <a name="powershell"></a>PowerShell

Följande länkar visar hur du skapar ett analytiskt lager med aktiverade behållare med hjälp av PowerShell:

* [Azure Cosmos DB-API för mongo DB](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection?view=azps-5.5.0#description&preserve-view=true)
* [Azure Cosmos DB SQL API](https://docs.microsoft.com/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_create&preserve-view=true)


## <a name="optional---update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Valfri – uppdatera tiden för analys av analys platsen till Live

När analys lagret har Aktiver ATS med ett visst TTL-värde kanske du vill uppdatera det till ett annat giltigt värde senare. Du kan uppdatera värdet med hjälp av Azure Portal, Azure CLI, PowerShell eller Cosmos DB SDK: er. Information om de olika alternativen för konfiguration av analytiska TTL-alternativ finns i artikeln [stöd för analytiska TTL-värden](analytical-store-introduction.md#analytical-ttl) .


### <a name="azure-portal"></a>Azure Portal

Om du har skapat en aktive rad behållare för analys lager via Azure Portal, innehåller den ett standard-analytiskt TTL-värde på-1. Använd följande steg för att uppdatera det här värdet:

1. Logga in på [Azure Portal](https://portal.azure.com/) eller [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Gå till ditt Azure Cosmos DB-konto och öppna fliken **datautforskaren** .

1. Välj en befintlig behållare som har analytiskt Arkiv aktiverat. Expandera den och ändra följande värden:

  * Öppna fönstret **Scale & Settings** (Skalning och inställningar).
  * Under **Ange** Sök, * * analys lagring Time to Live * *.
  * Välj **På (inte standard)** eller välj **På** och ange ett TTL-värde
  * Klicka på **Spara** för att spara ändringarna.

### <a name="net-sdk"></a>.NET SDK

Följande kod visar hur du uppdaterar TTL för analytisk lagring med hjälp av .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

### <a name="java-v4-sdk"></a>Java v4 SDK

Följande kod visar hur du uppdaterar TTL för analytisk lagring med hjälp av Java v4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

### <a name="python-v4-sdk"></a>Python v4 SDK

Stöds inte för närvarande.


### <a name="azure-cli"></a>Azure CLI

Följande länkar visar hur du uppdaterar behållare analys-TTL med hjälp av Azure CLI:

* [Azure Cosmos DB-API för mongo DB](https://docs.microsoft.com/cli/azure/cosmosdb/mongodb/collection?view=azure-cli-latest#az_cosmosdb_mongodb_collection_update&preserve-view=true)
* [Azure Cosmos DB SQL API](https://docs.microsoft.com/cli/azure/cosmosdb/sql/container?view=azure-cli-latest#az_cosmosdb_sql_container_update&preserve-view=true)

### <a name="powershell"></a>PowerShell

Följande länkar visar hur du uppdaterar behållare analytiskt TTL med hjälp av PowerShell:

* [Azure Cosmos DB-API för mongo DB](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollection?view=azps-5.5.0&preserve-view=true)
* [Azure Cosmos DB SQL API](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbsqlcontainer?view=azps-5.5.0&preserve-view=true)


## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Ansluta till en Synapse-arbetsyta

Använd anvisningarna i [ansluta till Azure Synapse-länken](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) om hur du kommer åt en Azure Cosmos DB databas från Azure Synapse Analytics Studio med Azure Synapse-länken.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Fråga analys lager med Apache Spark för Azure Synapse Analytics

Följ anvisningarna i artikeln [fråga Azure Cosmos DB analys lager](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) om hur du frågar med Synapse Spark. Artikeln innehåller några exempel på hur du kan interagera med analys lagret från Synapse-gester. Dessa gester visas när du högerklickar på en behållare. Med gester kan du snabbt skapa kod och anpassa den efter dina behov. De är också perfekta för att identifiera data med ett enda klick.

## <a name="query-the-analytical-store-using-serverless-sql-pool-in-azure-synapse-analytics"></a><a id="query-analytical-store-sql-on-demand"></a> Fråga analys lagret med Server lös SQL-pool i Azure Synapse Analytics

Med Server lös SQL-poolen kan du fråga efter och analysera data i Azure Cosmos DB behållare som är aktiverade med Azure Synapse-länken. Du kan analysera data nästan i real tid utan att påverka prestandan för dina transaktions arbets belastningar. Den erbjuder en välkänd T-SQL-syntax för att fråga data från analys lagret och integrerad anslutning till en mängd olika BI-och ad hoc-frågemeddelanden via T-SQL-gränssnittet. Mer information finns i artikeln [fråga analys lager med hjälp av Server lös SQL-pool](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

## <a name="use-serverless-sql-pool-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>Använd Server lös SQL-pool för att analysera och visualisera data i Power BI

Du kan bygga en databas för SQL-poolen utan server och Visa vyer över Synapse-länken för Azure Cosmos DB. Senare kan du skicka frågor till Azure Cosmos-behållare och sedan bygga en modell med Power BI över dessa vyer för att återspegla den frågan. Mer information finns i så här använder du [Server lös SQL-pool för att analysera Azure Cosmos db data med Synapse Link](synapse-link-power-bi.md) -artikel.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

[Azure Resource Manager-mallen](./manage-with-templates.md#azure-cosmos-account-with-analytical-store) skapar en Synapse-länk aktive rad Azure Cosmos DB konto för SQL-API. Den här mallen skapar ett huvud-API-konto i en region med en behållare som kon figurer ATS med analytiskt TTL aktiverat och ett alternativ för att använda manuellt eller autoskalning av data flöde. Om du vill distribuera den här mallen klickar **du på distribuera till Azure** på sidan viktigt.

## <a name="getting-started-with-azure-synapse-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Komma igång med Azure Synapse Link-samples

Du hittar exempel för att komma igång med Azure Synapse-länk på [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Dessa demonstrerar lösningar från slut punkt till slut punkt med IoT och detalj handels scenarier. Du kan också hitta de exempel som motsvarar Azure Cosmos DB API för MongoDB i samma lagrings platsen under mappen [MongoDB](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples) . 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse-länk för Azure Cosmos DB.](synapse-link.md)

* [Översikt över Azure Cosmos DB-analysarkiv.](analytical-store-introduction.md)

* [Vanliga frågor om Synapse-länk för Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark i Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Stöd för SQL-adresspool utan server i Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
