---
title: Power BI själv studie kurs för Azure Cosmos DB koppling
description: Använd den här Power BI själv studie kursen för att importera JSON, skapa informativa rapporter och visualisera data med hjälp av Azure Cosmos DB och Power BI koppling.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359871"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualisera Azure Cosmos DB-data med hjälp av anslutningsprogrammet för Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/) är en online tjänst där du kan skapa och dela instrument paneler och rapporter. Power BI Desktop är ett verktyg för rapportredigering som gör att du kan hämta data från olika datakällor. Azure Cosmos DB är en av de datakällor som du kan använda med Power BI Desktop. Du kan ansluta Power BI Desktop till ett Azure Cosmos DB-konto med Azure Cosmos DB-anslutningsprogrammet för Power BI.  När du har importerat Azure Cosmos DB-data till Power BI kan du omvandla dem, skapa rapporter och publicera rapporterna till Power BI.   

Den här artikeln beskriver de steg som krävs för att ansluta ett Azure Cosmos DB-konto till Power BI Desktop. När du har anslutit navigerar du till en samling, extraherar data, omvandlar JSON-data till tabellformat och publicerar en rapport till Power BI.

> [!NOTE]
> Power BI anslutningen för Azure Cosmos DB ansluter till Power BI Desktop. Rapporter som skapats i Power BI Desktop kan publiceras till PowerBI.com. Direkt extrahering av Azure Cosmos DB data kan inte utföras från PowerBI.com. 

> [!NOTE]
> Att ansluta till Azure Cosmos DB med Power BI Connector stöds för närvarande endast för Azure Cosmos DB SQL API-och Gremlin API-konton.

## <a name="prerequisites"></a>Förutsättningar
Innan du följer anvisningarna i den här Power BI själv studie kursen, se till att du har åtkomst till följande resurser:

* [Ladda ned den senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Ladda ned [exempel Volcano-data](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) från GitHub.

* [Skapa ett Azure Cosmos Database-konto](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) och importera Volcano-data med hjälp av [verktyget Azure Cosmos DB datamigrering](import-data.md). När du importerar data bör du tänka på följande inställningar för källan och målen i verktyget datamigrering:

   * **Käll parametrar** 

       * **Importera från:** JSON-fil (er)

   * **Mål parametrar** 

      * **Anslutnings sträng:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Partitionsnyckel:** /Country 

      * **Samlings data flöde:** 1000 

Om du vill dela dina rapporter i PowerBI.com måste du ha ett konto i PowerBI.com.  Mer information om Power BI och Power BI Pro finns i [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Nu sätter vi igång
I den här självstudien ska vi föreställa dig att du är en Geologist-Volcanoes över hela världen. Volcano-data lagras i ett Azure Cosmos DB konto och JSON-dokumentets format är följande:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Du hämtar Volcano-data från Azure Cosmos DB-kontot och visualiserar data i en interaktiv Power BI-rapport.

1. Kör Power BI Desktop.

2. Du kan **Hämta data**, se de **senaste källorna** eller **öppna andra rapporter** direkt från välkomst skärmen. Välj "X" i det övre högra hörnet för att stänga skärmen. **Rapportvyn** för Power BI Desktop visas.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop Report View-Power BI koppling":::

3. Välj menyfliksområdet **Start** och klicka på **Hämta data**.  Fönstret **Hämta data** ska visas.

4. Klicka på **Azure**, Välj **Azure Cosmos dB (beta)** och klicka sedan på **Anslut**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop hämta data Power BIs koppling":::

5. På sidan för **hands versions koppling** klickar du på **Fortsätt**. Fönstret **Azure Cosmos DB** visas.

6. Ange Azure Cosmos DB kontots slut punkts-URL som du vill hämta data från enligt nedan och klicka sedan på **OK**. Om du vill använda ditt eget konto kan du hämta URL: en från rutan URI i bladet **nycklar** i Azure Portal. Alternativt kan du ange databasens namn, samlings namn eller använda navigatören för att välja databasen och samlingen för att identifiera var data kommer från.
   
7. Om du ansluter till den här slut punkten för första gången uppmanas du att ange konto nyckeln. Hämta nyckeln från **primär nyckel** rutan på bladet **skrivskyddade nycklar** i Azure Portal för ditt eget konto. Ange lämplig nyckel och klicka sedan på **Anslut**.
   
   Vi rekommenderar att du använder den skrivskyddade nyckeln när du skapar rapporter. Detta förhindrar onödig exponering av primär nyckeln för potentiella säkerhets risker. Den skrivskyddade nyckeln är tillgänglig från bladet **nycklar** i Azure Portal. 
    
8. När kontot har anslutits visas fönstret **navigatör** . I **navigatören** visas en lista över databaser under kontot.

9. Klicka på och expandera i databasen där data för rapporten kommer från och välj **volcanodb** (ditt databas namn kan vara olika).   

10. Välj nu en samling som innehåller de data som ska hämtas och välj **volcano1** (ditt samlings namn kan vara olika).
    
    I förhands gransknings fönstret visas en lista över **post** objekt.  Ett dokument visas som en **post** typ i Power BI. På samma sätt är ett kapslat JSON-block i ett dokument också en **post**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – navigerings fönstret":::

12. Klicka på **Redigera** för att starta Frågeredigeraren i ett nytt fönster för att transformera data.

## <a name="flattening-and-transforming-json-documents"></a>Förenkling och transformering av JSON-dokument
1. Växla till fönstret Power BI Frågeredigeraren där kolumnen **dokument** visas i rutan i mitten.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Frågeredigeraren i Power BI Desktop":::

1. Klicka på expanderaen på höger sida av **dokumentets** kolumn rubrik.  Snabb menyn med en lista över fält visas.  Välj de fält som du behöver för rapporten, t. ex. Volcano namn, land, region, plats, höjning, typ, status och senaste resultat. Avmarkera rutan **Använd ursprungligt kolumn namn som prefix** och klicka sedan på **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – expandera dokument":::

1. I mittenfönstret visas en förhands granskning av resultatet med fälten valt.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – förenkla resultat":::

1. I vårt exempel är egenskapen location ett interjson-block i ett dokument.  Som du kan se visas platsen som en **post** typ i Power BI Desktop.  

1. Klicka på expanderaen på höger sida av dokumentets. plats kolumn rubrik.  Snabb menyn med fälten typ och koordinater visas.  Nu ska vi välja fältet koordinater, se till att **använda ursprungligt kolumn namn som prefix** inte är markerat och klicka på **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Power BI själv studie kurs för Azure Cosmos DB Power BI kopplings plats post":::

1. I mittenfönstret visas nu en kolumn med en kolumn med **list** typen.  Som du ser i början av självstudien är de interjson-data i den här självstudien av punkt typ med värden för latitud och longitud registrerade i matrisen koordinater.
   
   Elementet koordinater [0] representerar longitud medan koordinaterna [1] representerar latitud.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – koordinater-lista":::

1. Skapa en **anpassad kolumn** med namnet LatLong för att förenkla koordinaten.  Välj menyfliksområdet **Lägg till kolumn** och klicka på **anpassad kolumn**.  Fönstret **anpassad kolumn** visas.

1. Ange ett namn för den nya kolumnen, t. ex. LatLong.

1. Ange sedan den anpassade formeln för den nya kolumnen.  I vårt exempel sammanfogar vi värdena för latitud och longitud, avgränsade med kommatecken, som du ser nedan med hjälp av följande formel: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Klicka på **OK**.
   
   Mer information om data analys uttryck (DAX) inklusive DAX-funktioner finns [i grunderna för DAX i Power BI Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – Lägg till anpassad kolumn":::

1. Nu visar rutan i mitten de nya LatLong-kolumnerna som har fyllts med värdena.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Power BI själv studie kurs om Azure Cosmos DB Power BI koppling – anpassad LatLong-kolumn":::
    
    Om du får ett fel i den nya kolumnen ser du till att de tillämpade stegen under frågeinställningar matchar följande figur:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Tillämpade steg ska vara källa, navigering, expanderat dokument, expanderat dokument. location, tillagd anpassad":::
    
    Om stegen är olika tar du bort de extra stegen och försöker lägga till den anpassade kolumnen igen. 

1. Klicka på **Stäng och Använd** för att spara data modellen.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI själv studie kurs för Azure Cosmos DB Power BI koppling – Stäng & tillämpa":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Bygg rapporterna

I Power BI Desktop rapportvyn kan du börja skapa rapporter för att visualisera data.  Du kan skapa rapporter genom att dra och släppa fält i **rapport** arbets ytan.

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop rapportvyn – dra och släpp de obligatoriska fälten":::

I rapportvyn bör du hitta:

1. I fönstret **fält** kan du se en lista över data modeller med fält som du kan använda för dina rapporter.
1. Fönstret **visualiseringar** . En rapport kan innehålla en eller flera visualiseringar.  Välj de visuella typer som passar dina behov från fönstret **visualiseringar** .
1. På **rapport** arbets ytan kan du bygga visualiseringarna för din rapport.
1. **Rapport** sidan. Du kan lägga till flera rapport sidor i Power BI Desktop.

Nedan visas de grundläggande stegen för att skapa en enkel interaktiv kart visnings rapport.

1. I vårt exempel skapar vi en Map-vy som visar platsen för varje Volcano.  I fönstret **visualiseringar** klickar du på kartans visuella typ som marker ATS i skärm bilden ovan.  Du bör se den visuella typen för kartan som målats på **rapport** arbets ytan.  **Visualiserings** fönstret bör också visa en uppsättning egenskaper som är relaterade till typen av kart visualisering.
1. Nu ska du dra och släppa fältet LatLong från fönstret **fält** till egenskapen **location** i fönstret **visualiseringar** .
1. Dra sedan och släpp fältet Volcano namn till egenskapen **Legend** .  
1. Dra och släpp fältet för höjning till egenskapen **storlek** .  
1. Nu bör du se kartan som visar en uppsättning bubblor som visar platsen för varje Volcano med storleken på bubblan som motsvarar upphöjningen av Volcano.
1. Nu har du skapat en Basic-rapport.  Du kan anpassa rapporten ytterligare genom att lägga till fler visualiseringar.  I vårt fall lade vi till ett Volcano-utsnitt för att göra rapporten interaktivt.  
   
1. Klicka på **Spara** på Arkiv-menyn och spara filen som PowerBITutorial. pbix.

## <a name="publish-and-share-your-report"></a>Publicera och dela din rapport
Om du vill dela rapporten måste du ha ett konto i PowerBI.com.

1. Klicka på menyfliksområdet **Start** i Power BI Desktop.
1. Klicka på **Publicera**.  Du uppmanas att ange användar namn och lösen ord för ditt PowerBI.com-konto.
1. När autentiseringsuppgiften har autentiserats publiceras rapporten till ditt mål som du har valt.
1. Klicka på **Öppna "PowerBITutorial. pbix" i Power BI** för att se och dela din rapport på PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Publicering till Power BI lyckades! Öppna självstudie i Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Skapa en instrumentpanel i PowerBI.com
Nu när du har en rapport kan du dela den på PowerBI.com

När du publicerar rapporten från Power BI Desktop till PowerBI.com, genererar den en **rapport** och en **data uppsättning** i PowerBI.com-klienten. När du till exempel har publicerat en rapport som heter **PowerBITutorial** till PowerBI.com visas PowerBITutorial i avsnittet **rapporter** och **data uppsättningar** på PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Skärm bild av den nya rapporten och data uppsättningen i PowerBI.com":::

Om du vill skapa en delbar instrument panel klickar du på knappen **Fäst live-sida** i PowerBI.com-rapporten.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Skärm bild av hur du fäster en rapport i PowerBI.com":::

Följ sedan anvisningarna i [fästa en panel från en rapport](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) för att skapa en ny instrument panel. 

Du kan också göra ad hoc-ändringar i rapporten innan du skapar en instrument panel. Vi rekommenderar dock att du använder Power BI Desktop för att utföra ändringarna och publicera om rapporten till PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Nästa steg
* Mer information om Power BI finns i [Kom igång med Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Mer information om Azure Cosmos DB finns i [landnings sidan för Azure Cosmos DB-dokumentation](https://azure.microsoft.com/documentation/services/cosmos-db/).
