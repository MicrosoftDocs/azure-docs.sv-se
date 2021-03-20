---
title: Använda Azure Sentinel-visningslistor
description: Den här artikeln beskriver hur du använder Azure Sentinel watchlists för att undersöka hot, importera affärs data, skapa listor över tillåtna och utöka händelse data.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99555648"
---
# <a name="use-azure-sentinel-watchlists"></a>Använda Azure Sentinel-visningslistor

> [!IMPORTANT]
> Funktionen watchlists är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Azure Sentinel-watchlists möjliggör insamling av data från externa data källor för korrelation med händelser i din Azure Sentinel-miljö. När du har skapat kan du använda watchlists i din sökning, identifierings regler, Hot jakt och svars spel böcker. Watchlists lagras i Azure Sentinel-arbetsytan som namn-värde-par och cachelagras för optimala fråge prestanda och låg latens.

Vanliga scenarier för att använda watchlists är:

- **Undersöka hot** och svara på incidenter snabbt med snabb import av IP-adresser, filhash-filer och andra data från CSV-filer. När du har importerat kan du använda visnings lista namn/värde-par för kopplingar och filter i aviserings regler, hot på jakt, arbets böcker, bärbara datorer och allmänna frågor.

- **Importera affärs data** som en visnings lista. Du kan till exempel importera användar listor med privilegie rad system åtkomst eller avslutade medarbetare och sedan använda visnings lista för att skapa listor över tillåtna och nekade användare som används för att identifiera eller förhindra att användarna loggar in i nätverket.

- **Minska aviserings utmattningen**. Skapa listan över tillåtna för att förhindra aviseringar från en grupp användare, till exempel användare från auktoriserade IP-adresser som utför uppgifter som normalt utlöser aviseringen och förhindrar att ofarliga händelser blir aviseringar.

- **Omfattande händelse data**. Använd watchlists för att utöka dina händelse data med namn-värde-kombinationer som härleds från externa data källor.

## <a name="create-a-new-watchlist"></a>Skapa en ny visnings lista

1. Gå till **Azure Sentinel**  >  **Configuration**  >  **visnings lista** från Azure Portal och välj sedan **Lägg till ny**.

    > [!div class="mx-imgBorder"]
    > ![ny visnings lista](./media/watchlists/sentinel-watchlist-new.png)

1. På sidan **Allmänt** anger du namn, beskrivning och alias för visnings lista. Välj sedan **Nästa**.

    > [!div class="mx-imgBorder"]
    > ![visnings lista allmän sida](./media/watchlists/sentinel-watchlist-general.png)

1. På sidan **källa** väljer du typ av data uppsättning, laddar upp en fil och väljer sedan **Nästa**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="visnings lista käll sida" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Fil överföringar är för närvarande begränsade till filer på upp till 3,8 MB.

1. Granska informationen, kontrol lera att den är korrekt och välj sedan **skapa**.

    > [!div class="mx-imgBorder"]
    > ![visnings lista gransknings sida](./media/watchlists/sentinel-watchlist-review.png)

    Ett meddelande visas när visnings lista har skapats.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="avisering om visnings lista har skapats" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Använda watchlists i frågor

1. Från Azure Portal navigerar du till **Azure Sentinel**  >  **Configuration**  >  **visnings lista**, väljer den visnings lista som du vill använda och väljer sedan **Visa i Log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="använda watchlists i frågor" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Objekten i dina visnings lista extraheras automatiskt för din fråga och visas på fliken **resultat** . Exemplet nedan visar resultatet av extraheringen av fälten **servername** och **IpAddress** .

    > [!NOTE]
    > Tidsstämpeln för dina frågor kommer att ignoreras i både frågans användar gränssnitt och i schemalagda aviseringar.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="frågor med visnings lista-fält" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. Du kan fråga efter data i en tabell mot data från en visnings lista genom att behandla visnings lista som en tabell för kopplingar och uppslag.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="frågor mot visnings lista som sökning":::

## <a name="use-watchlists-in-analytics-rules"></a>Använda watchlists i analys regler

Om du vill använda watchlists i analys regler går du till **Azure Sentinel**  >  **Configuration**  >  **Analytics** från Azure Portal och skapar en regel med hjälp av `_GetWatchlist('<watchlist>')` funktionen i frågan.

1. I det här exemplet skapar du en visnings lista med namnet "ipwatchlist" med följande värden:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="lista över fyra objekt för visnings lista":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="Skapa visnings lista med fyra objekt":::

1. Skapa sedan analys regeln.  I det här exemplet inkluderar vi bara händelser från IP-adresser i visnings lista:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="använda watchlists i analys regler":::

## <a name="view-list-of-watchlists-aliases"></a>Visa lista över watchlists-alias

Om du vill hämta en lista över visnings lista-alias går du till **Azure Sentinel**-  >  **allmänna**  >  **loggar** från Azure Portal och kör följande fråga: `_GetWatchlistAlias` . Du kan se listan över alias på fliken **resultat** .

> [!div class="mx-imgBorder"]
> ![Visa lista watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du använder watchlists i Azure Sentinel för att utöka data och förbättra undersökningar. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
