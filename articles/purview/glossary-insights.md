---
title: Ord lista för dina data med hjälp av avdelningens kontroll Insights
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll Insights-rapporter om dina data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095858"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Ord lista för dina data i Azure avdelningens kontroll

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar avdelningens kontrolls ord insikts rapporter för dina data.

I den här instruktions guiden lär du dig att:

> [!div class="checklist"]
> - Gå till insikter från ditt avdelningens kontroll-konto
> - Få en fågel ögon-vy över dina data

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

- Konfigurera dina Azure-resurser och fyll i kontot med data

- Konfigurera och slutför en sökning på käll typen

- Skapa en ord lista och bifoga till gångar till ord listans villkor

Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Använd avdelningens kontroll Glossy Insights

I Azure avdelningens kontroll kan du skapa ord lista och bifoga dem till till gångar. Senare kan du Visa ord listans distribution i ord lista. Detta ger dig en status för din ord lista med villkor kopplade till till gångar. Det visar också villkor för status och distribution av roller efter antal användare.

**Så här visar du ord insikter:**

1. Gå till skärmen **Azure avdelningens kontroll** [instance i Azure Portal](https://aka.ms/purviewportal) och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** går du till avsnittet **Kom igång** och väljer konto panelen **Starta avdelningens kontroll** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Starta avdelningens kontroll från Azure Portal":::

1. På **Start** sidan för avdelningens kontroll väljer du panelen **Visa insikter** för att komma åt dina **insikter** - :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: områden.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Visa dina insikter i Azure Portal":::

1. I avsnittet **insikter** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: väljer du **ord lista** för att Visa avdelningens kontroll- **ordlista** .

Med **blanka insikter** får du till gång till en företags användare, värdefull information för att underhålla en väldefinierad ord lista för din organisation.

1. Rapporten börjar med **KPI: er på hög nivå** som **visar de _totala villkoren_*_ i ditt avdelningens kontroll-konto, _*_godkända villkor utan till gångar_*_ och _*_Utgångna villkor med till gångar_**. Vart och ett av dessa värden hjälper dig att identifiera hälso tillståndet för din ord lista.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Visa blank insikter-KPI"::: 


2. Avsnittet **ögonblicks bild av villkor** (visas ovan) visar villkors status som **_utkast_*_, _*_godkänt_*_, _*_avisering_*_ och _*_upphör att gälla_** för termer med till gångar och villkor utan till gångar.

3. Klicka på **Visa mer** om du vill se term namnen med olika status och mer information om **_vårder_*_ och _*-_experter_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Ögonblicks bild av villkor med och utan till gångar":::  

4. När du klickar på "Visa mer" för ***godkända villkor med till gångar** _, kan du gå till sidan _ *ord* lista * term, där du kan gå vidare till listan med till gångar med de bifogade villkoren. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Insikter om ord lista"::: 

4. På sidan ord i ord lista kan du Visa en distribution av **ofullständiga villkor** efter typ av information som saknas. Grafen visar antalet villkor med den **_saknade definitionen_*_, _*_saknar expert_*_, _*_saknar_ remissing *_ och _*_saknas flera_** fält.

1. Klicka på ***Visa mer** _ från _ * ofullständiga villkor * * för att Visa termer som saknar information. Du kan gå till sidan med ord för detaljerad information och ange den information som saknas och se till att ord listan är klar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du skapar en ord lista med [ord lista](./how-to-create-import-export-glossary.md)