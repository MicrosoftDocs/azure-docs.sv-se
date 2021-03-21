---
title: Power BI och Server lös SQL-pool för att analysera Azure Cosmos DB data med Synapse-länk
description: Lär dig hur du skapar en server lös SQL-adresspool och vyer över Synapse-länken för Azure Cosmos DB, frågar Azure Cosmos DB-behållare och sedan skapar en modell med Power BI över dessa vyer.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.openlocfilehash: a16d22a49e42408271c91d961bcdb004536e3980
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659757"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Använd Power BI-och Server lös Synapse SQL-pool för att analysera Azure Cosmos DB data med Synapse-länk 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

I den här artikeln får du lära dig hur du skapar en server lös SQL-pool-databas och vyer över Synapse-länken för Azure Cosmos DB. Du kommer att fråga Azure Cosmos DB behållare och sedan bygga en modell med Power BI över dessa vyer för att återspegla den frågan.

I det här scenariot ska du använda dummy-data om produkt försäljning i en partner butik. Du analyserar intäkterna per butik baserat på närhet till stora hushåll och effekten av annonsering under en viss vecka. I den här artikeln skapar du två vyer med namnet **RetailSales** och **StoreDemographics** och en fråga mellan dem. Du kan hämta exempel produkt data från den här [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) -lagrings platsen.

## <a name="prerequisites"></a>Förutsättningar

Se till att skapa följande resurser innan du börjar:

* [Skapa ett Azure Cosmos DB konto av typen SQL (Core) eller MongoDB.](create-cosmosdb-resources-portal.md)

* Aktivera Azure Synapse-länken för ditt [Azure Cosmos-konto](configure-synapse-link.md#enable-synapse-link)

* Skapa en databas i Azure Cosmos-kontot och två behållare som har ett [analytiskt Arkiv aktiverat.](configure-synapse-link.md#create-analytical-ttl)

* Läs in produkt data i Azure Cosmos-behållare enligt beskrivningen i den här antecknings boken för [batch-datautdata](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) .

* [Skapa en Synapse-arbetsyta](../synapse-analytics/quickstart-create-workspace.md) med namnet **SynapseLinkBI**.

* [Anslut Azure Cosmos-databasen till arbets ytan Synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Skapa en databas och vyer

Från arbets ytan Synapse går du till fliken **utveckla** , väljer **+** ikonen och väljer **SQL-skript**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Lägga till ett SQL-skript i Synapse Analytics-arbetsytan":::

Varje arbets yta levereras med en SQL-slutpunkt utan server. När du har skapat ett SQL-skript går du från verktygsfältet överst till **inbyggt**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Aktivera SQL-skriptet för att använda Server lös SQL-slutpunkten på arbets ytan":::

Det är inte rekommenderat eller stöd för att skapa vyer i **huvud** databasen eller **standard** databaserna. Skapa en ny databas med namnet **RetailCosmosDB** och en SQL-vy över Synapse-länkens aktiverade behållare. Följande kommando visar hur du skapar en databas:

```sql
-- Create database
Create database RetailCosmosDB
```

Skapa sedan flera vyer över olika Synapse-länkade Azure Cosmos-behållare. Med vyer kan du använda T-SQL för att ansluta till och fråga Azure Cosmos DB data som sitter i olika behållare.  Se till att välja **RetailCosmosDB** -databasen när du skapar vyerna.

Följande skript visar hur du skapar vyer för varje behållare. För enkelhetens skull ska vi använda funktionen för [automatiskt schema härledning](analytical-store-introduction.md#analytical-schema) för Server lös SQL-pool över Synapse-aktiverade behållare:


### <a name="retailsales-view"></a>RetailSales vy:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Se till att infoga din Azure Cosmos DB region och primär nyckel i föregående SQL-skript. Alla tecken i region namnet bör vara i gemener utan blank steg. Till skillnad från andra parametrar för `OPENROWSET` kommandot ska container namn-parametern anges utan citat tecken runt den.

### <a name="storedemographics-view"></a>StoreDemographics vy:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Kör nu SQL-skriptet genom att välja kommandot **Kör** .

## <a name="query-the-views"></a>Fråga vyerna

Nu när de två vyerna har skapats definierar vi frågan för att ansluta till dessa två vyer enligt följande:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Välj **körning** som ger följande tabell resultat:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Fråga efter resultat när du har anslutit till vyerna StoreDemographics och RetailSales":::

## <a name="model-views-over-containers-with-power-bi"></a>Modellera vyer över behållare med Power BI

Öppna sedan Power BI Skriv bordet och Anslut till den serverbaserade SQL-slutpunkten med hjälp av följande steg:

1. Öppna programmet Power BI Desktop. Välj **Hämta data** och välj **mer**.

1. Välj **Azure Synapse Analytics (SQL DW)** i listan över anslutnings alternativ.

1. Ange namnet på den SQL-slutpunkt där databasen finns. Ange `SynapseLinkBI-ondemand.sql.azuresynapse.net` i fältet **Server** . I det här exemplet är  **SynapseLinkBI** namnet på arbets ytan. Ersätt det om du har fått ett annat namn på din arbets yta. Välj **direkt fråga** för data anslutnings läge och klicka sedan på **OK**.

1. Välj önskad autentiseringsmetod, till exempel Azure AD.

1. Välj **RetailCosmosDB** -databasen och vyn **RetailSales**, **StoreDemographics** .

1. Välj **load** för att läsa in de två vyerna i Direct-frågeläge.

1. Välj **modell** för att skapa en relation mellan de två vyerna via kolumnen **storeId** .

1. Dra kolumnen **StoreId** från vyn **RetailSales** till kolumnen **StoreId** i **StoreDemographics** -vyn.

1. Välj relationen många till en (*: 1) eftersom det finns flera rader med samma lagrings-ID i vyn **RetailSales** . **StoreDemographics** har bara en lager-ID-rad (det är en dimensions tabell).

Gå nu till **rapport** fönstret och skapa en rapport för att jämföra den relativa vikten av hushålls storleken med den genomsnittliga intäkten per lager baserat på den spridda representationen av intäkterna och LargeHH index:

1. Välj **punkt diagram**.

1. Dra och släpp **LargeHH** från **StoreDemographics** -vyn till X-axeln.

1. Dra och släpp **intäkter** från **RetailSales** -vyn i Y-axeln. Välj **genomsnitt** för att hämta den genomsnittliga försäljningen per produkt per butik och per vecka.

1. Dra och släpp **ProductCode** från **RetailSales** -vyn i förklaringen för att välja en speciell produkt linje.
När du har valt dessa alternativ bör du se ett diagram som följande skärm bild:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Rapport som jämför den relativa vikten av hushålls storlek med den genomsnittliga intäkten per butik":::

## <a name="next-steps"></a>Nästa steg

[Använd T-SQL för att fråga Azure Cosmos DB data med Azure Synapse-länk](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Använd Server lös SQL-pool för att [analysera Azure Open-datauppsättningar och visualisera resultaten i Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
