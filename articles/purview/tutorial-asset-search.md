---
title: 'Självstudie: gå till start sidan för Azure-avdelningens kontroll och Sök efter en till gång'
description: I den här självstudien beskrivs hur du använder funktioner på Start sidan för Azure-avdelningens kontroll och söker i katalogen.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677839"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Självstudie: gå till start sidan för Azure-avdelningens kontroll (för hands version) och Sök efter en till gång

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här självstudien får du bekanta dig med Azure-avdelningens kontroll genom att navigera bland funktionerna på Start sidan och söka efter en till gång i katalogen.

Den här artikeln är *del 2 av en själv studie serie i fem delar* där du får lära dig grunderna i hur du hanterar data styrning över en datafastighet med Azure avdelningens kontroll. Den här självstudien bygger på det arbete du slutförde i [del 1: Genomsök data med Azure avdelningens kontroll](tutorial-scan-data.md)

I de här självstudierna får du:

> [!div class="checklist"]
>
> * Navigera till start sidan för Azure-avdelningens kontroll.
> * Sök efter en till gång.
> * Lägg till en till gångs ägare.

## <a name="prerequisites"></a>Förutsättningar

* Fullständig [självstudie: Genomsök data med Azure avdelningens kontroll](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navigera till start sidan för Azure-avdelningens kontroll

Följande steg beskriver hur du går igenom start sidan för Azure-avdelningens kontroll.

1. Gå till Azure avdelningens kontroll-resursen i Azure Portal och välj **Öppna avdelningens kontroll Studio**. Du kommer automatiskt till avdelningens kontroll Studios start sida.

   Den övre delen av start sidan visar namnet på din katalog och en uppsättning katalog analyser. Här ingår antalet källor, data till gångar och ord listans villkor. I sammanfattningen kan du se att det finns över 200-tillgångar i total-och 113 ord lista. Den här siffran uppdateras allteftersom organisationen skannar och lägger till fler villkor i Azure avdelningens kontroll.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Skärm bild som visar start sidan för Azure-avdelningens kontroll.":::

1. Välj **Bläddra till gångar** för att se alla dina till gångar.

## <a name="search-for-an-asset"></a>Sök efter en till gång

Innan du börjar får du en snabb uppdatering av en grundläggande terminologi:

* **Till gång**: ett enskilt objekt i katalogen som är koncist och som innehåller definitionen av en entitet i dataegendomen. Exempel på entiteter är en SQL-tabell, en Power BI rapport och din Data Factory-aktivitet.
  
* **Schema**: kallas även för en kolumn eller ett attribut, ett schema representerar strukturen för en till gång eller en resurs uppsättning.

* **Resurs uppsättning**: ett enda objekt i katalogen som representerar många fysiska objekt i lagringen. Dessa objekt delar vanligt vis ett gemensamt schema, och i de flesta fall, en namn konvention eller mappstruktur. Datum formatet är till exempel *åååå/mm/dd*. Mer information finns i [förstå resurs uppsättningar](concept-resource-sets.md).

* **Till gångs typ**: en gruppering av till gångar och resurs uppsättningar under ett logiskt namn som vanligt vis mappas till namnet på data plattformen.

Följ dessa steg om du vill söka efter en till gång och markera dig själv som ägare:

1. I rutan **Sök katalog** på Start sidan anger du namnet på den resurs grupp som innehåller din datafastighet (resurs gruppen som du skapade i föregående självstudie). En lista med förslag visas.

1. Välj **Visa Sök Resultat**. Eftersom alla dina till gångar börjar med namnet på din resurs grupp, visas alla i Sök resultaten. Utanför den här kursen kan du söka efter vissa till gångs namn, inte resurs grupper.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Skärm bild som visar listan över Sök förslag för kataloger.":::

1. Du kan använda filtren i det vänstra navigerings fältet för att filtrera efter till gångs typ, klassificering, kontakt, etikett och ord lista.

1. Om du vill söka efter en resurs uppsättning börjar du med att skriva namnet på uppsättningen. En lista med Sök förslag med rätt nyckelord visas. Du kan också söka efter absoluta namn genom att placera dem inom citat tecken.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Sök efter nyckelords till gång i Azure avdelningens kontroll":::

## <a name="edit-an-asset"></a>Redigera en till gång

1. Välj en av resurserna från Sök resultaten. Välj sedan **Redigera**

1. På fliken **Översikt** kan du lägga till en beskrivning.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Skärm bild som visar fältet Beskrivning på fliken Översikt.":::

1. Välj fliken **kontakter** . Bredvid **ägare**, i **Välj användare eller grupp**, börjar du skriva företagets e-postadress.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Skärm bild som visar ifyllda fält.":::

    Ett namn förslag visas automatiskt.

1. Bredvid **experter**, i **Välj användare eller grupp**, anger du ett namn (till exempel din chefs namn) och väljer sedan **Spara**.

    Fälten Beskrivning, ägarnamn och expert namn fylls i nu.

## <a name="next-steps"></a>Nästa steg

Innan du går vidare till nästa självstudie i den här serien bör du ta lite extra tid att utforska på Start sidan för Azure-avdelningens kontroll på egen hand. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Navigera till start sidan för Azure-avdelningens kontroll.
> * Sök efter en till gång.
> * Lägg till en till gångs ägare.

Gå vidare till nästa självstudie om du vill lära dig att söka efter till gångar i Azure avdelningens kontroll och identifiera till gångs härkomst.

> [!div class="nextstepaction"]
> [Bläddra till till gångar och visa deras härkomst](tutorial-browse-and-view-lineage.md)
