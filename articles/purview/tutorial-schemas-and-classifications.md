---
title: 'Självstudie: utforska resurs uppsättningar, information, scheman och klassificeringar i Azure dataavdelningens kontrolls (för hands version)'
description: I den här självstudien beskrivs hur du använder resurs uppsättningar, till gångs information, scheman och klassificeringar.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693320"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Självstudie: utforska resurs uppsättningar, information, scheman och klassificeringar i Azure dataavdelningens kontrolls (för hands version)

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här självstudien får du utforska viktiga komponenter i katalogen: resurs uppsättningar, till gångs information, scheman och klassificeringar.

Den här självstudien är *del 4 i en själv studie serie i fem delar* där du får lära dig grunderna i hur du hanterar data styrning över en datafastighet med Azure avdelningens kontroll. Den här självstudien bygger på det arbete som du slutförde i [del 3: Bläddra till gångar i Azure avdelningens kontroll (för hands version) och visa deras härkomst](tutorial-browse-and-view-lineage.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Visa resurs uppsättningar.
> * Visa till gångs information.
> * Redigera schemat och Lägg till klassificeringar.

## <a name="prerequisites"></a>Förutsättningar

* Fullständig [självstudie: Bläddra bland till gångar i Azure avdelningens kontroll (för hands version) och visa deras härkomst](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="view-resource-sets"></a>Visa resurs uppsättningar

En resurs uppsättning är ett enda objekt i katalogen som representerar många fysiska objekt i lagringen. Objekten delar vanligt vis ett gemensamt schema och, i de flesta fall, en namn konvention eller mappstruktur. Datum formatet är till exempel *åååå/mm/dd*. Mer information om resurs uppsättningar finns i [förstå resurs uppsättningar](concept-resource-sets.md).

1. Gå till Azure avdelningens kontroll-resursen i Azure Portal och välj **Öppna avdelningens kontroll Studio**. Du kommer automatiskt till avdelningens kontroll Studios start sida.

2. Ange **contoso_staging_positivecashflow_ n** i rutan **Sök till gångar** och välj sedan **Contoso_Staging_PositiveCashFlow_ {n}. csv** från Sök resultaten.

## <a name="view-asset-details"></a>Visa till gångs information

1. På fliken **Översikt** visas **beskrivningen**, **ord listans villkor** och **Egenskaper**, till exempel **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Skärm bild som visar fliken Översikt på en resurs uppsättnings till gångs sida.":::

1. Observera följande två fält under **Egenskaper**:

   * **partitionCount**: anger antalet fysiska filer som är associerade med den här resurs uppsättningen.
   * **schemaCount**: anger antalet variationer i schemat som hittades i den här resurs uppsättningen.

   Azure-avdelningens kontroll fyller i dessa egenskaper inom 24 timmar efter att du har slutfört sökningen i [del 1 av den här själv studie serien](tutorial-scan-data.md).

1. Välj fliken **kontakter** för att granska **experternas** och **ägarnas** värden.

## <a name="edit-the-schema-and-add-classifications"></a>Redigera schemat och Lägg till klassificeringar

1. Välj fliken **schema** . Observera kolumn namnen och de klassificeringar som är associerade med dem. Observera att Sök efter egenskaper fylldes i automatiskt.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Skärm bild som visar fliken schema.":::

1. Om du vill redigera till gången väljer du knappen **Redigera** längst upp till vänster. Välj sedan fliken **schema** .

1. Lägg till en **Beskrivning** för en kolumn eller Byt namn på kolumnen till ett mer eget namn.

1. Lägg till en klassificering på till gångs nivå genom att välja **kolumn nivå klassificerings** List rutan för ett kolumn namn som inte har någon angiven klassificering.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Redigera schema-sida":::

1. När du är klar med ändringarna väljer du **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Visa resurs uppsättningar.
> * Visa till gångs information.
> * Redigera schemat och Lägg till klassificeringar.

Gå vidare till nästa självstudie och lär dig mer om ord listan och hur du definierar och importerar nya villkor för till gångar.

> [!div class="nextstepaction"]
> [Skapa och importera ord listans villkor](tutorial-import-create-glossary-terms.md)