---
title: Visa och filtrera information om Azure-resurser
description: Filtrera informationen och Använd olika vyer för att bättre förstå dina Azure-resurser.
ms.topic: how-to
ms.date: 09/11/2020
ms.openlocfilehash: d3ad0c225a260fd0ae6b131b951b53c28042f4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738641"
---
# <a name="view-and-filter-azure-resource-information"></a>Visa och filtrera information om Azure-resurser

Med Azure Portal kan du söka efter detaljerad information om resurser i dina Azure-prenumerationer. Den här artikeln visar hur du filtrerar information och använder olika vyer för att bättre förstå dina resurser.

Artikeln fokuserar på skärmen **alla resurser** som visas på följande skärm bild. Skärmar för enskilda resurs typer, till exempel virtuella datorer, har olika alternativ, till exempel starta och stoppa en virtuell dator.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Azure Portal vy över alla resurser":::

## <a name="filter-resources"></a>Filtrera resurser

Börja utforska **alla resurser** med filter för att fokusera på en delmängd av dina resurser. Följande skärm bild visar filtrering på resurs grupper och väljer två av de sex resurs grupperna i en prenumeration.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Filtrera vy baserat på resurs grupper":::

Du kan kombinera filter, inklusive de som baseras på texts ökningar, som du ser i följande skärm bild. I det här fallet är resultaten begränsade till resurser som innehåller "SimpleWinVM" i någon av de två resurs grupperna som redan har valts.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Filtrera vy baserat på text inmatning":::

Om du vill ändra vilka kolumner som ska ingå i en vy väljer du **Hantera vy** och sedan **Redigera kolumner**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Redigera kolumner som visas i vyn":::

## <a name="save-use-and-delete-views"></a>Spara, använda och ta bort vyer

Du kan spara vyer som innehåller de filter och kolumner som du har valt. Så här sparar och använder du en vy:

1. Välj **Hantera vy** och sedan **Spara vy**.

1. Ange ett namn för vyn och välj sedan **OK**. Den sparade vyn visas nu i menyn **Hantera vy** .

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Sparad vy":::

1. Om du vill använda en vy växlar du mellan **standard** och en av dina egna vyer för att se hur den påverkar listan över resurser som visas.

Ta bort en vy:

1. Välj **Hantera vy** och **Bläddra sedan till alla vyer**.

1. I fönstret **sparade vyer för alla resurser** väljer du vyn och sedan **ta bort** ikonen ![ ta bort vy ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="export-information-from-a-view"></a>Exportera information från en vy

Du kan exportera resurs informationen från en vy. Exportera information i CSV-format:

1. Välj **Exportera till CSV**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Skärm bild av export till CSV-format":::

1. Spara filen lokalt och öppna sedan i Excel eller något annat program som stöder CSV-formatet. 

När du flyttar runt portalen ser du andra områden där du kan exportera information, till exempel en enskild resurs grupp.

## <a name="summarize-resources-with-visuals"></a>Sammanfatta resurser med visuella objekt

Vyerna som vi har tittat på hittills har varit _listvyer_, men det finns också _sammanfattnings vyer_ som innehåller visuella objekt. Du kan spara och använda dessa vyer precis som du kan visa vyer. Filtren är kvar mellan de två typerna av vyer. Det finns standardvyer, som vyn **plats** som visas nedan, samt vyer som är relevanta för vissa tjänster, till exempel vyn **status** för Azure Storage.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Sammanfattning av resurser i en kart visning":::

Så här sparar och använder du en sammanfattningsvy:

1. I menyn Visa väljer du **sammanfattningsvy**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Sammanfattnings Visa-menyn":::

1. I vyn sammanfattning kan du sammanfatta med olika attribut, inklusive **plats** och **typ**. Välj en **sammanfatta efter** -alternativ och ett lämpligt visuellt objekt. Följande skärm bild visar **typ sammanfattningen** med ett **stapeldiagram** visuellt.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Typ Sammanfattning visar ett stapeldiagram":::

1. Välj **Hantera vy** och sedan **Spara** för att spara vyn som du gjorde med listvyn.

1. Välj en stapel i diagrammet under **typ Sammanfattning** i vyn Sammanfattning. Om du markerar fältet visas en lista som filtrerats ned till en typ av resurs.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Alla resurser filtrerade efter typ":::

## <a name="run-queries-in-azure-resource-graph"></a>Köra frågor i Azure Resource Graph

Azure Resource Graph ger effektiv och genomförd resurs utforskning med möjlighet att fråga i skala över en uppsättning prenumerationer. Skärmen **alla resurser** i Azure Portal innehåller en länk för att öppna en resurs diagram fråga som är begränsad till den aktuella filtrerade vyn.

Så här kör du en resurs diagram fråga:

1. Välj **Öppna fråga**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Öppna Azures resurs diagram fråga":::

1. I **Azure Resource Graph Explorer** väljer du **Kör fråga** för att visa resultatet.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Kör Azure Resource Graph-fråga":::

    Mer information finns i [köra din första resurs diagram fråga med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Nästa steg

[Översikt över Azure Portal](azure-portal-overview.md)

[Skapa och dela instrumentpaneler i Azure-portalen](azure-portal-dashboards.md)
