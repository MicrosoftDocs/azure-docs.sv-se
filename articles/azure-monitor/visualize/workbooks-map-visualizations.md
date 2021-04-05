---
title: Azure Monitor arbets bok kart visualiseringar
description: Lär dig mer om att Azure Monitor arbets bok kart visualiseringar.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622847"
---
# <a name="map-visualization"></a>Kartvisualisering

Kart visualiseringen hjälper till att fästa problem i vissa regioner och visar sammansatta vyer av övervaknings data genom att tillhandahålla möjlighet att sammanställa alla data som mappas till varje plats/land/region.

Skärm bilden nedan visar det totala antalet transaktioner och svars tid från slut punkt till slut punkt för olika lagrings konton. Här definieras storleken på det totala antalet transaktioner och färg måtten under kartan visar svars tiden från slut punkt till slut punkt. Vid den första observationen är antalet transaktioner i regionen **västra USA** intill ett litet värde jämfört med regionen **USA, östra** , men svars tiden från slut punkt till slut punkt för regionen **västra USA** är högre än regionen **USA, östra** . Det ger inledande insikter om att något är Amiss för **västra USA**.

![Skärm bild av Azures plats karta](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Lägga till en karta

Kartan kan visualiseras om underliggande data/mått har information om latitud/longitud, information om Azure-resurser, information om Azure-platser eller land/region, namn eller lands-/region kod.

### <a name="using-azure-location"></a>Använda Azure-platsen

1. Ändra arbets boken till redigerings läge genom att välja i objektet **Redigera** verktygsfält.
2. Välj **Lägg till** och *Lägg till fråga*.
3. Ändra *data källan* till och `Azure Resource Graph` Välj sedan en prenumeration som har ett lagrings konto.
4. Ange frågan nedan för analysen och välj **Kör fråga**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Ställ in *storlek* på `Large` .
6. Ange *visualiseringen* till `Map` .
7. Alla inställningar fylls i automatiskt. För anpassade inställningar väljer du knappen **kart inställningar** för att öppna fönstret inställningar.
8. Nedan visas en skärm bild av kart visualiseringen som visar lagrings konton för varje Azure-region för den valda prenumerationen.

![Skärm bild av Azures plats karta med ovanstående fråga](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Använda Azure Resource

1. Ändra arbets boken till redigerings läge genom att välja i objektet **Redigera** verktygsfält.
2. Välj **Lägg till** och *Lägg till mått*.
3. Använd en prenumeration som har lagrings konton.
4. Ändra *resurs typ* till `storage account` och i *resurs* Välj flera lagrings konton.
5. Välj **Lägg till mått** och Lägg till ett transaktions mått.
    1. Namnområde: `Account`
    2. Kostnads `Transactions`
    3. Aggregat `Sum`
    
    ![Skärm bild av transaktions mått](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Välj **Lägg till mått** och Lägg till E2E svars tids mått.
    1. Namnområde: `Account`
    1. Kostnads `Success E2E Latency`
    1. Aggregat `Average`
    
    ![Skärm bild av lyckad svars tid från slut punkt till slut punkt](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Ställ in *storlek* på `Large` .
1. Ange *visualiserings* storlek till `Map` .
1. I **Map-inställningar** anger du följande inställningar:
    1. Plats information med: `Azure Resource`
    1. Fält för Azure-resurs: `Name`
    1. Storlek efter: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Sammansättning för plats: `Sum of values`
    1. Typ av färgning: `Heatmap`
    1. Färg efter: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agg regering för färg: `Sum of values`
    1. Färgpalett: `Green to Red`
    1. Minsta värde: `0`
    1. Mått värde: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregera andra mått efter: `Sum of values`
    1. Välj rutan **anpassad formatering**
    1. Processor `Milliseconds`
    1. ATS `Decimal`
    1. Maximalt antal decimaler: `2`

### <a name="using-countryregion"></a>Använda land/region

1. Ändra arbets boken till redigerings läge genom att välja i objektet **Redigera** verktygsfält.
2. Välj **Lägg till* och *Lägg sedan till fråga*.
3. Ändra *data källan* till `Log` .
4. Välj *resurs typ* som `Application Insights` och välj sedan en Application Insights resurs som har pageViews-data.
5. Använd Frågeredigeraren för att ange KQL för din analys och välj **Kör fråga**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Ange värdena för storlek `Large` .
7. Ange visualiseringen till `Map` .
8. Alla inställningar fylls i automatiskt. För anpassade inställningar väljer du **kart inställningar**.

### <a name="using-latitudelocation"></a>Använda latitud/plats

1. Ändra arbets boken till redigerings läge genom att välja i objektet **Redigera** verktygsfält.
2. Välj **Lägg till* och *Lägg sedan till fråga*.
3. Ändra *data källan* till `JSON` .
1. Ange JSON-data nedan i Frågeredigeraren och välj **Kör fråga**.
1. Ange värdena för *storlek* `Large` .
1. Ange *visualiseringen* till `Map` .
1. I **Map-inställningar** under "mått inställningar" anger du *mått etiketten* till och `displayName` väljer sedan **Spara och Stäng**.

Kart visualiseringen nedan visar användare för varje latitud och longitud plats med den valda etiketten för mått.

![Skärm bild av en kart visualisering som visar användare för varje latitud och longitud plats med den valda etiketten för mått](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Kart inställningar

### <a name="layout-settings"></a>Inställningar för layout

| Inställning | Förklaring |
|:-------------|:-------------|
| `Location info using` | Välj ett sätt att hämta platsen för objekt som visas på kartan. <br> **Latitud/longitud**: Välj det här alternativet om det finns kolumner med information om latitud och longitud. Varje rad med data från latitud och longitud visas som distinkt objekt på kartan. <br> **Azure-plats**: Välj det här alternativet om det finns en kolumn med Azure Location (östra, westeurope, Kanada osv.) information. Ange att kolumnen och den kommer att hämta motsvarande latitud och longitud för varje Azure-plats, gruppera samma plats rader (baserat på agg regering anges) tillsammans för att Visa platserna på kartan. <br> **Azure-resurs**: Välj det här alternativet om det finns en kolumn med Azure Resource information (lagrings konto, cosmosdb-konto osv.). Ange att kolumnen och den kommer att hämta motsvarande latitud och longitud för varje Azure-resurs, gruppera samma plats (Azure location) rader (baserat på agg regering som anges) för att Visa platserna på kartan. <br> **Land/region**: Välj det här alternativet om det finns en kolumn med namnet/koden för landet/regionen (US, USA, i, Indien, CN, Kina). Ange att kolumnen och den kommer att hämta motsvarande latitud och longitud för varje land/region/kod och gruppera raderna tillsammans med samma Country-Region kod/land regions namn för att Visa platserna på kartan. Lands namnet och lands koden grupperas inte tillsammans som en enskild enhet på kartan.
| `Latitude/Longitude` | De här två alternativen visas om värdet för plats informations fältet är: latitud/longitud. Välj den kolumn som har latitud i fältet latitud och longitud i fältet longitud. |
| `Azure location field` | Det här alternativet visas om värdet för plats informations fältet är: Azure location. Markera den kolumn som Azure plats informationen finns i. |
| `Azure resource field` | Det här alternativet visas om värdet för plats informations fältet är: Azure-resurs. Markera den kolumn som Azure-resursinformation. |
| `Country/Region field` | Det här alternativet visas om värdet för plats informations fältet är: land eller region. Markera kolumnen som land/region-informationen. |
| `Size by` | Det här alternativet styr storleken på de objekt som visas på kartan. Storleken beror på värdet i kolumnen som anges av användaren. För närvarande är radien för cirkeln direkt proportionerlig till kvadratroten för kolumnens värde. Om ingen... är markerad visas standard områdets storlek i alla cirklar.|
| `Aggregation for location` | Det här fältet anger hur **storleken** på kolumner som har samma Azure-plats/Azure-resurs/land-region sammanställs. |
| `Minimum region size` | Det här fältet anger vad som är den minsta radien för det objekt som visas på kartan. Detta används när det finns en betydande skillnad mellan värdena för kolumn storlek och därför är mindre objekt synliga på kartan. |
| `Maximum region size` | Det här fältet anger vad som är den största radien för det objekt som visas på kartan. Detta används när värdet för kolumnens storlek är mycket stort och de täcker det enorma områdes diagrammet.|
| `Default region size` | Det här fältet anger vad som är standard-radien för det objekt som visas på kartan. RADIUS-standardvärdet används när storleken efter kolumn är none... eller så är värdet 0.|
| `Minimum value` | Det minsta värdet som används för att beräkna regions storlek. Om inget värde anges blir minimivärdet det minsta värdet efter AGG regering. |
| `Maximum value` | Det maximala värdet som används för att beräkna region storlek. Om inget värde anges kommer det högsta värdet att vara det största värdet efter AGG regering.|
| `Opacity of items on Map` | Det här fältet anger hur transparent är de objekt som visas på kartan. Ogenomskinlighet på 1 betyder, ingen transparens, där ogenomskinligheten på 0 innebär att objekten inte syns på kartan. Om det finns för många objekt på kartan kan opacitet anges till lågt värde så att alla överlappande objekt visas.|

### <a name="color-settings"></a>Färg inställningar

| Typ av färgning | Förklaring |
|:------------- |:-------------|
| `None` | Alla noder har samma färg. |
| `Thresholds` | I den här typen anges cell färger enligt tröskelvärdes regler (till exempel _CPU > 90% => Red, 60% > CPU > 90% => gul, CPU < 60% => grönt_). <ul><li> **Färg efter**: värde för den här kolumnen används av tröskelvärden/termisk karta Logic.</li> <li>**Agg regering för färg**: det här fältet anger hur du sammanställer **färg efter** kolumner som har samma Azure-plats/Azure-resurs/land-region. </li> <ul> |
| `Heatmap` | I den här typen är cellerna färgade baserat på fältet färgpalett och färg efter. Detta kommer också att ha samma **färg** och **agg regering för färg** alternativ som tröskelvärden. |

### <a name="metric-settings"></a>Mått inställningar
| Inställning | Förklaring |
|:------------- |:-------------|
| `Metric Label` | Det här alternativet visas om värdet för plats informations fältet är: latitud/longitud. Med den här funktionen kan användaren välja etiketten för att visa mått som visas under kartan. |
| `Metric Value` | Det här fältet anger det mått värde som ska visas under kartan. |
| `Create 'Others' group after` | I det här fältet anges gränsen innan en "andra"-grupp skapas. |
| `Aggregate 'Others' metrics by` | Det här fältet anger den agg regering som används för gruppen "andra" om den visas. |
| `Custom formatting` | Använd det här fältet om du vill ange alternativ för enheter, format och formatering för numeriska värden. Detta är samma som [rutnätets anpassade formatering](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar en [honung kamma-visualisering i arbets böcker](../visualize/workbooks-honey-comb.md).