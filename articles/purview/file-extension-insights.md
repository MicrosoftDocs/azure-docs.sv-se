---
title: Fil tilläggs rapportering för dina data i Azure avdelningens kontroll med hjälp av avdelningens kontroll Insights
description: Den här instruktions guiden beskriver hur du visar och använder avdelningens kontroll för fil tillägg på dina data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 246a9dcfa35498d99fd403f10e5c17d7707efe84
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202631"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Fil namns insikter om dina data från Azure avdelningens kontroll 

Den här instruktions guiden beskriver hur du kommer åt, visar och filtrerar insikter om fil namns tilläggen eller filtyper som finns i dina data.

Data källor som stöds är: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2

I den här instruktions guiden lär du dig att:
> [!div class="checklist"]
> * Starta ditt avdelningens kontroll-konto från Azure. 
> - Visa fil namns insikter för dina data
> - Öka detalj nivån för mer information om fil tillägg på dina data

## <a name="prerequisites"></a>Förutsättningar 

Innan du börjar med avdelningens kontroll Insights bör du kontrol lera att du har slutfört följande steg:

- Konfigurera dina Azure-resurser och fyll i relevanta konton med test data

- Konfigurera och slutför en genomsökning av test data i varje data Källa

- Inloggad på avdelningens kontroll med konto med en [data läsare eller data curator-roll](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).


Mer information finns i [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Använd avdelningens kontroll File Extension Insights

När du skannar dina till gångar kan Azure avdelningens kontroll identifiera de filtyper som finns i din datafastighet och du får mer information om varje filtyp. Information omfattar hur många filer av varje typ du har, var dessa filer finns och om de är lättlästa för känsliga data.

> [!NOTE]
> När du har skannat dina käll typer ger du **fil namns tillägget** ett par timmar för att avspegla de nya till gångarna.

**Så här visar du fil namns insikter:**

1. Gå till skärmen **Azure avdelningens kontroll** [instance i Azure Portal](https://aka.ms/purviewportal) och välj ditt avdelningens kontroll-konto.

1. På sidan **Översikt** går du till avsnittet **Kom igång** och väljer konto panelen **Starta avdelningens kontroll** .

1. I avdelningens kontroll väljer du meny alternativet **insikter** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: till vänster för att komma åt ditt **Insights** -fält.
    
1. Välj fliken **fil namns tillägg** i **insikter**.

    Rapporten visar en sammanfattning av hur många unika fil namns tillägg som hittas, samt ett diagram över de 10 populära tilläggen som hittas under den valda tids ramen (standard: 30 dagar).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Fil tilläggs rapport – översikt" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Gör något av följande för att få mer information:

    - Välj **tids** väljaren överst i rapporten för att ändra tidsintervallet för vilket fil namns tillägget hittades.
    
    - Välj **Visa mer** under grafen om du vill visa en fullständig lista över de fil namns tillägg som har hittats. Mer information finns i avsnittet om [insikter för fil namns tillägg](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Specificering av fil namns ökning

När du har sett den övergripande informationen om de filtyper som finns i din datafastighet kan du öka detalj nivån för mer information om var de finns och om de kan genomsökas efter känsliga data.

Exempel:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Fil tilläggs rapport – specificera" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

I rutnätet visas information om varje fil tillägg som hittas, inklusive:

- **Antal filer**. Antal filer med det angivna tillägget.
- **Innehålls genomsökning**. Huruvida fil namns tillägget stöds för genomsökning av känsliga data.
- **Prenumerationer**. Antalet prenumerationer där det angivna tillägget hittades.
- **Källor**. Antalet källor som hittades med det angivna fil namns tillägget.



Använd filtren ovanför rutnätet för att filtrera data som visas:

|Alternativ  |Beskrivning  |
|---------|---------|
|**Filtrera efter nyckelord**     |    Ange text i rutan **Filtrera efter nyckelord**  om du vill visa filter för fil typerna efter namn. Om du till exempel bara vill visa PDF-filer anger du `PDF` .     |
|**Tid**        | Välj om du vill filtrera efter ett bestämt tidsintervall för när dina data skapades. <br>**Standard:** 30 dagar  |
|**Filnamnstillägg**     |Välj om du vill filtrera rutnätet efter en eller flera filtyper.        |
|**Källor**    |Välj om du vill filtrera rutnätet efter de enskilda data källorna. |
|**Innehålls genomsökning**     |Välj att välja **stöds** eller **inte stöds**, för att endast Visa filtyper som kan genomsökas efter känsliga data eller data som inte kan genomsökas, till exempel **. cert** -eller **. jpg** -filer. |
| | |

Ovanför filtren väljer du **Redigera kolumner** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: om du vill visa fler eller färre kolumner i rutnätet eller ändra ordningen. 

Om du vill sortera rutnätet väljer du en kolumn rubrik som du vill sortera efter kolumnen.
## <a name="next-steps"></a>Nästa steg

Läs mer om Azure avdelningens kontroll Insight-rapporter
> [!div class="nextstepaction"]
> [Ord lista](glossary-insights.md)

> [!div class="nextstepaction"]
> [Skanna insikter](scan-insights.md)

> [!div class="nextstepaction"]
> [Klassificerings insikter](./classification-insights.md)

> [!div class="nextstepaction"]
> [Etikett insikter för känslighet](sensitivity-insights.md)
