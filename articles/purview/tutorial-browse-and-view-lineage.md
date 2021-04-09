---
title: 'Självstudie: Bläddra till resurser i Azure avdelningens kontroll och visa deras härkomst'
description: I den här självstudien beskrivs hur du bläddrar efter till gångar i katalogen och visar data härkomst.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696101"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Självstudie: Bläddra till resurser i Azure avdelningens kontroll (för hands version) och visa deras härkomst

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här självstudien får du lära dig hur du bläddrar till gångar i Azure avdelningens kontroll och ser deras viktiga information, till exempel härkomst.

Den här självstudien är *del tre i en själv studie serie i fem delar* där du får lära dig grunderna i hur du hanterar data styrning över en datafastighet med Azure avdelningens kontroll. Den här självstudien bygger på det arbete som du slutförde i [del 2: gå till start sidan för Azure-avdelningens kontroll (för hands version) och Sök efter en till gång](tutorial-asset-search.md)

I de här självstudierna får du:

> [!div class="checklist"]
>
> * Bläddra efter till gångar i katalogen.
> * Visa härkomst för till gångar.

## <a name="prerequisites"></a>Förutsättningar

* Fullständig [självstudie: gå till start sidan för Azure-avdelningens kontroll (för hands version) och Sök efter en till gång](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="browse-for-assets-in-the-catalog"></a>Bläddra efter till gångar i katalogen

I den föregående själv studie kursen lärde du dig hur du kan identifiera till gångar i Azure avdelningens kontroll-katalogen med hjälp av Sök funktionen. Ett annat sätt att identifiera till gångar i katalogen är att använda katalogens webb upplevelse.

Det här avsnittet visar hur du bläddrar i Azure avdelningens kontroll-katalogen.

1. Gå till Azure avdelningens kontroll-resursen i Azure Portal och välj **Öppna avdelningens kontroll Studio**. Du kommer automatiskt till avdelningens kontroll Studios start sida.

1. På sidan **Start** väljer du **Bläddra till gångar**.

   Sidan **Bläddra till gångar** visas, som visar en sammanfattning av alla till gångs typer i din katalog.

1. Om du vill utforska de olika Azure Data Lake-Gen2 till gångar som är tillgängliga i din katalog väljer du panelen **Azure Data Lake Gen2** .

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Skärm bild som visar sidan Bläddra efter till gång med Azure Data Lake Gen2 vald.":::

1. Om det finns flera till gångar kan du välja kolumn rubriken **namn** för att sortera till gångarna i alfabetisk ordning. I den här självstudien finns det bara en Azure Data Lake Storage Gen2 till gång.

1. Välj namnet på till gången.

1. Välj resurs uppsättningen **Contoso_GrossProfit_ {N}. SSV** . Om den här till gången inte finns i din katalog väljer du en annan.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Lista över Azure Data Lake Storage Gen2 resurser":::

## <a name="view-the-lineage-of-assets"></a>Visa härkomst för till gångar

På sidan till gångs information utforskar du data källan.

1. Välj fliken **härkomst** i resurs uppsättningen **Contoso_GrossProfit_ {N}. SSV** .

   Den till gång du har valt visas. Härkomst-informationen som visas visar att den här resurs uppsättningen har kopierats från ditt Azure Blob Storage-konto till Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Skärm bild som visar härkomst-vyn för till gången.":::

1. Välj blob-till gången på den här sidan och välj länken **Växla till till gång** .

   Observera att fönstret har växlat till resurs uppsättningen för Azure Blob, som var den ursprungliga Azure Data Lake Storage Gen2s källa.

1. Välj fliken **Översikt** för att undersöka till gången och bekräfta dess information.

Information om hur du skapar en Azure Data Factory data flödes aktivitet mellan en Azure-blob och Azure Data Lake Storage Gen2 resurs uppsättning och observera härkomst finns i [Azure Data Factory data flödes aktivitet](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Bläddra efter till gångar i katalogen.
> * Visa härkomst för till gångar.

Gå vidare till nästa självstudie om du vill lära dig mer om resurs uppsättningar, till gångs information, scheman och klassificeringar.

> [!div class="nextstepaction"]
> [Resurs uppsättningar, till gångs information, scheman och klassificeringar](tutorial-schemas-and-classifications.md)
