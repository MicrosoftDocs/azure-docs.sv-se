---
title: Skanna insikter om dina data i Azure avdelningens kontroll
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll Insights-skannings rapportering på dina data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548699"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Skanna insikter om dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar insikter om Azure avdelningens kontroll-genomsökningar för dina data.

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> * Visa insikter från ditt avdelningens kontroll-konto.
> * Få en fågel ögon-vy över dina genomsökningar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

* Konfigurera dina Azure-resurser och fyll i kontot med data.
* Konfigurera och slutför en sökning på data källan.

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Använda avdelningens kontroll Scan Insights

I Azure avdelningens kontroll kan du registrera och skanna käll typer. Du kan visa status för genomsökningen över tid i Skanna insikter. Insikterna talar om hur många genomsökningar som misslyckats, lyckades eller var avbruten inom en viss tids period.

### <a name="view-scan-insights"></a>Visa genomsökningsinsikter

1. Gå till skärmen **Azure avdelningens kontroll** instance i Azure Portal och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** i avsnittet **Kom igång** väljer du panelen **Öppna avdelningens kontroll Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Starta avdelningens kontroll från Azure Portal":::

1. På **Start** sidan för avdelningens kontroll väljer du panelen **Visa insikter** för att komma åt dina **insikter** - :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: områden.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Visa dina insikter i Azure Portal":::

1. I avsnittet **insikter** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: väljer du **genomsökningar** för att visa rapporten avdelningens kontroll **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Visa KPI: er på hög nivå för att visa antal genomsökningar efter status och djup att gå in i varje genomsökning
 
1. KPI: er på hög nivå visar de totala genomsökningarna som körs inom en period. Tids perioden är standard under de senaste 30 dagarna. Men du kan även välja de senaste sju dagarna. Baserat på tids filtret återspeglar KPI-värdena antalet genomsökningar korrekt.


1. Baserat på det tids filter värde som valts kan du se distributionen av lyckade, misslyckade och avbrutna genomsökningar per vecka eller dag i diagrammet.

1. Längst ned i diagrammet finns det en **mer** länk som du kan utforska vidare. Länken öppnar sidan  **skannings status** i inläsnings upplevelsen. Här kan du se ett namn på genomsökningen och hur många gånger det har lyckats, misslyckats eller avbrutits under de senaste 30 dagarna.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Visa skannings status över tid":::

4. Du kan utforska en speciell sökning ytterligare genom att klicka på namnet på den **genomsökning** som ska ansluta dig till genomsöknings historiken i Azure-avdelningens kontroll **källor** . På sidan körnings historik kan du hämta körnings-ID: t som kan hjälpa till vid ytterligare fel undersökning.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Visa information om genomsökning":::

5. Slutligen kan du gå tillbaka till sidan Skanna insikter- **status** genom att följa bröd Crumbs i det övre vänstra hörnet på sidan körnings historik.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Visa genomsökningshistorik"::: 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om Azure avdelningens kontroll **Insights** med [data insikter](./concept-insights.md)

* Lär dig mer om Azure avdelningens kontroll- **källor** med [Hantera data källor](./manage-data-sources.md)