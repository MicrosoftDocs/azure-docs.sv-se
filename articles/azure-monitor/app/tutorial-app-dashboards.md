---
title: Skapa anpassade instrumentpaneler i Azure Application Insights | Microsoft Docs
description: Självstudie för att skapa anpassade KPI-instrumentpaneler med Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 701480300101b019830c57d9aa000534fa63bb6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628132"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Skapa anpassade KPI-instrumentpaneler med Azure Application Insights

Du kan skapa flera instrumentpaneler i Azure-portalen som var och en inkluderar paneler som visualiserar data från flera Azure-resurser i olika grupper och prenumerationer.  Du kan fästa olika diagram och vyer från Azure Application Insights för att skapa anpassade instrumentpaneler som ger dig en fullständig bild av programmets hälsa och prestanda. Den här självstudien vägleder dig igenom skapandet av en anpassad instrumentpanel som inkluderar fler typer av data och visualiseringar från Azure Application Insights.

 Lär dig att:

> [!div class="checklist"]
> * Skapa en anpassad instrumentpanel i Azure
> * Lägga till en panel från panelgalleriet
> * Lägga till standardmått i Application Insights till instrumentpanelen
> * Lägga till ett anpassat måttdiagram i Application Insights till instrumentpanelen
> * Lägg till resultaten av en loggfiler (analys) i instrument panelen

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Distribuera ett .NET-program till Azure och [aktivera Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> De behörigheter som krävs för att arbeta med instrument paneler beskrivs i artikeln om [att förstå åtkomst kontroll för instrument paneler](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel

> [!WARNING]
> Om du flyttar din Application Insights-resurs till en annan resurs grupp eller prenumeration måste du uppdatera instrument panelen manuellt genom att ta bort de gamla panelerna och fästa nya paneler från samma Application Insights resurs på den nya platsen.

En enda instrumentpanel kan innehålla resurser från flera program, resursgrupper och prenumerationer.  Börja självstudien genom att skapa en ny instrumentpanel för ditt program.  

1. I list rutan till vänster i Azure Portal väljer du **instrument panel**.

    ![List Rute meny för Azure Portal](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. I fönstret instrument panel väljer du **ny instrument panel** och sedan **Tom instrument panel**.

   ![Ny instrumentpanel](media/tutorial-app-dashboards/new-dashboard.png)

3. Ange ett namn för instrumentpanelen.
4. Ta en titt i **panelgalleriet** och se olika paneler som du kan lägga till i instrumentpanelen.  Förutom att lägga till paneler från galleriet kan du fästa diagram och andra vyer direkt från Application Insights till instrument panelen.
5. Leta rätt på panelen **Markdown** och dra den till din instrumentpanel.  Med den här panelen kan du lägga till text som är formaterad i markdown, vilket är idealiskt för att lägga till beskrivande text på din instrument panel. Mer information finns i [använda en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll](../../azure-portal/azure-portal-markdown-tile.md).
6. Lägg till text i panelens egenskaper och ändra storlek på den på instrumentpanelens arbetsyta.

    [![Redigera Markdown-panel](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Välj anpassning som **gjorts** överst på skärmen för att avsluta panel anpassnings läge.

## <a name="add-health-overview"></a>Lägga till hälsoöversikt

En instrument panel med statisk text är inte mycket intressant, så nu lägger du till en panel från Application Insights för att visa information om ditt program. Du kan lägga till Application Insights-paneler från panelgalleriet eller fästa dem direkt från skärmarna i Application Insights. På så sätt kan du konfigurera tabeller och vyer som du redan är bekant med innan du fäster dem på instrumentpanelen.  Börja med att lägga till en standardhälsoöversikt för programmet.  Det kräver ingen konfiguration och gör en minimal anpassning i instrumentpanelen.


1. Välj din **Application Insights**-resurs på startsidan.
2. I **översikts** fönstret väljer du PIN-ikonen PIN-ikon ![ ](media/tutorial-app-dashboards/pushpin.png) för att lägga till panelen på en instrument panel.
3. På fliken fäst på instrument panelen väljer du vilken instrument panel du vill lägga till panelen i eller skapar en ny.
 
3. I det övre högra hörnet visas ett meddelande om att din panel har fästs på instrument panelen.  Välj **Fäst på instrument panelen** i meddelandet för att återgå till din instrument panel eller Använd fönstret instrument panel.
4. Panelen läggs nu till i instrumentpanelen. Välj **Redigera** för att ändra placeringen av panelen. Markera och dra den till plats och välj **anpassning som gjorts**. Instrumentpanelen har nu en panel med lite användbar information.

    [![Instrument panel i redigerings läge](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Lägga till anpassade måttdiagram

I panelen **Mått** kan du visa ett diagram över ett mått som samlas in av Application Insights över tid med valfritt filter och valfri gruppering.  Du kan lägga till det här diagrammet på instrumentpanelen, precis som med allt annat i Application Insights.  Det kräver lite anpassning först.

1. Välj din **Application Insights**-resurs på startsidan.
1. Välj **Mått**.  
2. Ett tomt diagram har redan skapats, och du uppmanas att lägga till ett mått.  Lägg till ett mått i diagrammet och lägg eventuellt till ett filter och en gruppering.  I exemplet nedan visas antalet serverbegäranden grupperade efter framgång.  Då får du en löpande vy över slutförda och misslyckade begäranden.

    [![Lägga till mått](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Välj **Fäst på instrumentpanelen** till höger.

3.  I det övre högra hörnet visas ett meddelande om att din panel har fästs på instrument panelen. Välj **Fäst på instrument panelen** i meddelandet för att återgå till instrument panelen eller på fliken instrument panel.

4. Panelen läggs nu till i instrumentpanelen. Välj **Redigera** för att ändra placeringen av panelen. Markera och dra panelen till plats och välj sedan **anpassningen har gjorts**.

## <a name="add-logs-query"></a>Lägg till loggar fråga

Azure Application Insights-loggar innehåller ett rikt frågespråk som gör att du kan analysera alla data som samlas in Application Insights. Precis som med diagram och andra vyer kan du lägga till utdata från en loggad fråga på din instrument panel.

1. Välj din **Application Insights**-resurs på startsidan.
2. Välj **loggar** till vänster under "övervakning" för att öppna fliken loggar.
3. Skriv följande fråga som returnerar de 10 mest efterfrågade sidorna och antal förfrågningar:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Välj **Kör** för att validera resultatet av frågan.
5. Välj fästikonen ![Fästikon](media/tutorial-app-dashboards/pushpin.png) och välj namnet på din instrument panel.

5. Innan du går tillbaka till instrument panelen, lägger du till en annan fråga, men återger den som ett diagram så att du kan se olika sätt att visualisera en loggfils fråga på en instrument panel. Börja med följande fråga som summerar de 10 vanligaste åtgärderna med flest undantag.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Välj **Diagram** och byt till en **ring** för att visualisera resultatet.

    [![Ring diagram med frågan ovan](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Välj fästikonen ![Fästikon](media/tutorial-app-dashboards/pushpin.png) längst upp till höger för att fästa diagrammet på instrument panelen och sedan återgå till instrument panelen.
7. Resultatet av frågorna läggs nu till i instrumentpanelen i det format du har valt. Markera och dra var och en på plats och välj sedan **anpassning**.
8. Välj Penn ikonen ![Pennikon](media/tutorial-app-dashboards/pencil.png) på varje rubrik för att ge dem ett beskrivande namn.

## <a name="share-dashboard"></a>Dela instrumentpanel

1. Klicka på **dela** överst på instrument panelen för att publicera ändringarna.
2. Om du vill kan du definiera specifika användare som ska ha åtkomst till instrumentpanelen. Mer information finns i [dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomst kontroll i Azure](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Välj **Publicera**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att skapa anpassade instrumentpaneler kan du ta en titt på resten av Application Insights-dokumentationen, som innehåller en fallstudie.

> [!div class="nextstepaction"]
> [Djupgående diagnostik](../app/devops.md)
