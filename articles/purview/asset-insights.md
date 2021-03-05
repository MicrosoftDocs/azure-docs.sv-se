---
title: Till gångs insikter om dina data i Azure avdelningens kontroll (för hands version)
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll Insights till gångs rapportering för dina data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: b9a207ffa14a18a5f4421fd21cebed28290b5ea6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183088"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Till gångs insikter om dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar avdelningens kontroll till gångs Insight-rapporter för dina data.

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> * Visa insikter från ditt avdelningens kontroll-konto.
> * Få en fågel ögon-vy över dina data.
> * Öka detalj nivån för mer information om till gångar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

* Konfigurera dina Azure-resurser och fyll i kontot med data.

* Konfigurera och slutför en genomsökning av käll typen.

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Använd avdelningens kontroll Asset Insights

I Azure avdelningens kontroll kan du registrera och skanna käll typer. När genomsökningen är klar kan du Visa till gångs distributionen i till gångs information, vilket ger dig tillstånd för din datafastighet efter klassificering och resurs uppsättningar. Du får också information om hur data storleken ändras.

> [!NOTE]
> När du har skannat dina käll typer ger du till gångs Insights 3-8 timmar för att avspegla de nya till gångarna. Fördröjningen kan bero på hög trafik i distributions regionen eller storleken på din arbets belastning. Om du vill ha mer information kan du kontakta support teamet för fältet.

1. Navigera till din Azure avdelningens kontroll-resurs i Azure Portal.

1. På sidan **Översikt** i avsnittet **Kom igång** väljer du panelen **Öppna avdelningens kontroll Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Starta avdelningens kontroll från Azure Portal":::

1. På **Start** sidan för avdelningens kontroll väljer du panelen **Visa insikter** för att komma åt dina **insikter** - :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: områden.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Visa dina insikter i Azure Portal":::

1. I avsnittet **insikter** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: väljer du **till gångar** för att visa rapporten avdelningens kontroll **Asset Insights** .

### <a name="view-asset-insights"></a>Visa till gångs insikter

1. Sidan main **Asset Insights** visar följande områden:

2. KPI: er på hög nivå för att Visa käll typer, klassificerade till gångar och identifierade till gångar
 
3. Det första diagrammet visar **till gångar per källtyp**.

4. Visa din till gångs distribution efter källtyp. Välj en klassificering eller en hel klassificerings kategori för att se till gångs distribution efter källtyp. 
 
5. Om du vill visa mer väljer du **Visa mer**, som visar en tabell form av käll typerna och antalet till gångar. Klassificerings filtren överförs till den här sidan.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Visa KPI: er och diagram i till gångs insikter":::
 
6. Välj en speciell källa som du vill se de främsta mapparna med till gångs antalet. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Välj typ av källa":::
 
7. Välj de totala till gångarna mot den översta mappen i den källtyp som du valde ovan.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Visa fil Sök vägar":::

8. Visa listan över filer i mappen. Gå tillbaka till insikter med synlig Crumbs.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Visa lista över till gångar":::  

### <a name="file-based-source-types"></a>Filbaserade käll typer
I nästa par grafer i Asset Insights visas en distribution av filbaserade käll typer. Det första diagrammet, som kallas **storleks trend (GB) av filtypen i käll typerna**, visar den viktigaste fil typs storleken trend under de senaste 30 dagarna. 
 
1. Välj typ av källa för att visa filtypen i källan. 
 
1. Välj **Visa mer** om du vill se aktuell data storlek, ändrings storlek, antal aktuella till gångar och ändringar i till gångs antal.
 
   > [!NOTE]
   > Om genomsökningen bara har körts en gång under de senaste 30 dagarna eller om en katalog ändring som klassificerings tillägg/borttaget bara har skett en gång på 30 dagar, så visas ändrings informationen ovan tom.

1. Se de översta mapparna med antalet ändra högsta till gångar när du klickar på typ av källa.

1. Välj sökvägen för att visa till gångs listan.

Den andra grafen i filbaserade käll typer är ***filer som inte är associerade med en resurs uppsättning***. Om du förväntar dig att alla filer ska samlas in i en resurs uppsättning, kan det här grafen hjälpa dig att förstå vilka till gångar som inte har registrerats. Saknade till gångar kan vara en indikation på fel fil mönster i mappen. Följ samma steg som i andra grafer om du vill visa mer information om filerna.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Visa filbaserade till gångar":::  

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Azure avdelningens kontroll Insight-rapporter med [scanning Insights](./scan-insights.md)
