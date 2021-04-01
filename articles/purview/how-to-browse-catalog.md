---
title: 'Gör så här: Bläddra i Data Catalog'
description: Den här artikeln ger en översikt över hur du bläddrar i Azure avdelningens kontroll-Data Catalog baserat på till gångs typ.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: dd2fd4fbc9d8aecd9821d668dd02bd4b68659cf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695063"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Bläddra i Azure avdelningens kontroll Data Catalog

I den här artikeln beskrivs hur du identifierar data i din Azure avdelningens kontroll-Data Catalog med hjälp av det hierarkiska namn området för data källan.

## <a name="browse-experience"></a>Bläddra i upplevelse

En data konsument kan identifiera data med hjälp av det välkända hierarkiska namn området för var och en av data källorna med hjälp av en trädvy. När data källan har registrerats och genomsökts extraherar data kartan information om strukturen (hierarkisk namnrymd) för data källan. Den här informationen används för att bygga webb läsnings upplevelsen för data identifiering.

Du kan till exempel enkelt hitta en data uppsättning med namnet *DateDimension* under en mapp med namnet *Dimensions* i Azure Data Lake Storage gen 2. Du kan använda upplevelsen "Bläddra efter till gångs typ" för att navigera till ADLS gen 2-lagrings konto, sedan bläddra till tjänsten > container > mapp (ar) för att nå mappen med de olika *dimensionerna* och sedan se *DateDimension* -tabellen.

En inbyggd webbläsare med hierarkiskt namn område tillhandahålls för var och en av de motsvarande data källorna.

## <a name="browse-the-data-catalog-by-asset-type"></a>Bläddra i Data Catalog efter till gångs typ

1. Du kan bläddra bland data till gångar genom att välja **Bläddra efter till gångs typ** på Start sidan.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Start sida för avdelningens kontroll" border="true":::

1. På sidan **Bläddra till till gångs typer** kategoriseras paneler av data källor. Om du vill utforska till gångar ytterligare i varje data källa väljer du motsvarande panel.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Sidan Bläddra till resurs typer" border="true":::

1. På nästa sida visas toppnivå till gångar under den valda data typen. Välj en av till gångarna för att ytterligare utforska innehållet.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Till gångs typ för en speciell Browse-sida. Exemplet som visas är Azure Storage konto" border="true":::

1. Explorer-vyn öppnas. Börja surfa genom att välja till gången i den vänstra panelen. Underordnade till gångar visas i den högra panelen på sidan.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Trädvy" border="true":::

1. Om du vill visa information om en till gång väljer du knappen namn eller ellipser längst till höger.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Välj knappen ovaler om du vill se sidan med till gångs information" border="true":::

## <a name="view-related-data-assets"></a>Visa relaterade datatillgångar

När du är på sidan till gångs information kan du också visa andra relaterade data till gångar. Du kan till exempel navigera till den överordnade mappen i *DateDimension* för att se mappen *dimensioner* eller till och med navigera till behållaren för att se de till gångarna under den aktuella hierarkin.

1. Välj fliken **relaterad** på sidan till gångs information för att se andra relaterade till gångar.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Starta relaterad flik" border="true":::

1. Den aktuella till gångens fullständiga hierarki visas till vänster.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Hierarkisk struktur" border="true":::

1. Välj en nivå i hierarkin för att visa de omedelbara till gångarna under den nivån.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Välj en annan hierarki" border="true":::

## <a name="next-steps"></a>Nästa steg

- [Skapa, importera och exportera ord listans villkor](how-to-create-import-export-glossary.md)
- [Så här hanterar du term mallar för företags ord lista](how-to-manage-term-templates.md)
