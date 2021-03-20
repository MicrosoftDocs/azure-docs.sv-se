---
title: Bearbeta data från Event Hubs Azure med Stream Analytics | Microsoft Docs
description: Den här artikeln visar hur du bearbetar data från Azure Event Hub med ett Azure Stream Analytics-jobb.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 2db4fba59a1a06a24ee2939c51ecdf65aa06cef3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85312902"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Bearbeta data från händelsehubben med hjälp av Azure Stream Analytics 
Med tjänsten Azure Stream Analytics kan du enkelt mata in, bearbeta och analysera strömmande data från Azure Event Hubs, vilket möjliggör kraftfulla insikter för att köra real tids åtgärder. Med den här integreringen kan du snabbt skapa en pipeline för snabb Sök väg analys. Du kan använda Azure Portal för att visualisera inkommande data och skriva en Stream Analytics fråga. När din fråga är klar kan du flytta den till produktion på bara några få klick. 

## <a name="key-benefits"></a>Viktiga fördelar
Här är de viktigaste fördelarna med Azure Event Hubs och Azure Stream Analytics-integrering: 
- **Förhandsgranska data** – du kan förhandsgranska inkommande data från en Event hub i Azure Portal.
- **Testa frågan** – Förbered en omvandlings fråga och testa den direkt i Azure Portal. Syntaxen för frågespråket finns i [Stream Analytics frågespråk språk](/stream-analytics-query/built-in-functions-azure-stream-analytics) dokumentation.
- **Distribuera din fråga till produktion** – du kan distribuera frågan till produktion genom att skapa och starta ett Azure Stream Analytics jobb.

## <a name="end-to-end-flow"></a>Slut punkt till slut punkt

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till **Event Hubs namn området** och navigera sedan till **händelsehubben**, som innehåller inkommande data. 
1. Välj **process data** på sidan händelsehubben.  

    ![Panelen bearbeta data](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Välj **utforska** på panelen **Aktivera insikter i real tid från händelse** panelen. 

    ![Välj Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Du ser en frågenod med värden som redan har angetts för följande fält:
    1. **Händelsehubben** som inmatad för frågan.
    1. Exempel på **SQL-fråga** med SELECT-instruktion. 
    1. Ett **kolumnalias** för att referera till dina fråge test resultat. 

        ![Frågeredigeraren](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  När du använder den här funktionen för första gången frågar den här sidan efter din tillåtelse att skapa en konsument grupp och en princip för händelsehubben för att förhandsgranska inkommande data.
1. Välj **skapa** i **förhands gransknings** fönstret som visas i föregående bild. 
1. Du ser omedelbart en ögonblicks bild av de senaste inkommande data på den här fliken.
    - Serialiserings typen i dina data identifieras automatiskt (JSON/CSV). Du kan manuellt ändra den och JSON/CSV/AVRO.
    - Du kan förhandsgranska inkommande data i tabell format eller RAW-format. 
    - Om data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna. 

        Här är ett exempel på data i **tabell format**:   ![ resultat i tabell format](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Här är ett exempel på data i **RAW-format**: 

        ![Resulterar i RAW-format](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Välj **test fråga** för att se en ögonblicks bild av test resultatet av frågan på fliken **test resultat** . Du kan också hämta resultaten.

    ![Testa frågeresultat](./media/process-data-azure-stream-analytics/test-results.png)
1. Skriv din egen fråga för att transformera data. Se [Stream Analytics språk referens för frågor](/stream-analytics-query/stream-analytics-query-language-reference).
1. När du har testat frågan och vill flytta den till produktion väljer du **distribuera fråga**. Om du vill distribuera frågan skapar du ett Azure Stream Analytics jobb där du kan ange utdata för jobbet och starta jobbet. Om du vill skapa ett Stream Analytics jobb anger du ett namn för jobbet och väljer **skapa**.

      ![Skapa ett Azure Stream Analytics-jobb](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Vi rekommenderar att du skapar en konsument grupp och en princip för varje nytt Azure Stream Analytics jobb som du skapar från Event Hubs sidan. Konsument grupper tillåter endast fem samtidiga läsare, så att en dedikerad konsument grupp för varje jobb kommer att undvika eventuella fel som kan uppstå från att överskrida den gränsen. Med en dedikerad princip kan du rotera nyckeln eller återkalla behörigheter utan att påverka andra resurser. 
1. Ditt Stream Analytics jobb skapas nu där din fråga är samma som du har testat, och indatamängden är händelsehubben. 

9.  Slutför pipelinen genom att ange **utdata** för frågan och välj **Starta** för att starta jobbet.

    > [!NOTE]
    > Glöm inte att ersätta outputalias med utgångs namnet som du skapade i Azure Stream Analytics innan du startar jobbet.

      ![Ange utdata och starta jobbet](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Kända begränsningar
När du testar din fråga tar test resultaten ungefär 6 sekunder att läsa in. Vi arbetar med att förbättra testningens prestanda. Men när det distribueras i produktion har Azure Stream Analytics under en längre tid.

## <a name="streaming-units"></a>Strömningsenheter
Ditt Azure Stream Analytics jobb har standardvärdet tre enheter för strömning (SUs). Om du vill ändra den här inställningen väljer du **skala** på den vänstra menyn på sidan **Stream Analytics jobb** i Azure Portal. Mer information om strömnings enheter finns i [förstå och justera strömnings enheter](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Skala enheter för strömning](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics frågor finns i [Stream Analytics frågespråk](/stream-analytics-query/built-in-functions-azure-stream-analytics)
