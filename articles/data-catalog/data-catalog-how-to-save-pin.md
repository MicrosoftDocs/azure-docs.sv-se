---
title: Spara sökningar och fäst data till gångar i Azure Data Catalog
description: Instruktions artikel för att markera funktioner i Azure Data Catalog för att spara data källor och data till gångar för senare användning.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674843"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Spara sökningar och fäst data till gångar i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion
Azure Data Catalog tillhandahåller funktioner för identifiering av data källor. Du kan snabbt söka efter och filtrera katalogen för att hitta data källor och förstå deras avsedda syfte, vilket gör det enklare att hitta rätt data för jobbet i handen.

Men vad händer om du ofta behöver arbeta med samma data? Och vad händer om du och andra användare regelbundet bidrar med dina kunskaper till samma data källor i katalogen? I dessa fall kan det vara ineffektivt att behöva utfärda samma sökningar upprepade gånger. Det är här som sparade sökningar och fästa data till gångar kan hjälpa dig.

## <a name="saved-searches"></a>Sparade sökningar
En sparad sökning i Data Catalog är en återanvändbar Sök definition per användare. Du kan definiera en sökning, inklusive Sök termer, taggar och andra filter och sedan spara den. Du kan köra den sparade Sök definitionen igen senare för att returnera data till gångar som matchar Sök villkoren.

### <a name="create-a-saved-search"></a>Skapa en sparad sökning
Gör så här om du vill skapa en sparad sökning:
1. Klicka på **Spara** i fönstret **aktuell sökning** i Azure Data Catalog Portal. 

    ![Den aktuella Sök inställningen Spara länk](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Ange Sök villkoren som du vill återanvända och klicka sedan på **Spara**.

    ![Aktuella Sök inställningar sparade Söknamn](./media/data-catalog-how-to-save-pin/02-name.png)

3. När du uppmanas till det anger du ett namn för den sparade sökningen. Välj ett namn som är meningsfullt och som beskriver de data till gångar som ska returneras av sökningen.

### <a name="manage-saved-searches"></a>Hantera sparade sökningar
När du har sparat en eller flera sökningar visas alternativet **sparade sökningar** under den **aktuella** sökrutan. När listan expanderas visas alla sparade sökningar.

 ![Data Catalog lista över sparade sökningar](./media/data-catalog-how-to-save-pin/03-list.png)

Gör något av följande:

* Om du vill köra en sökning väljer du en sparad sökning i listan.

* Om du vill visa en lista över hanterings alternativ för en sparad sökning klickar du på nedåtpilen bredvid Sök namnet.

    ![Alternativ för att hantera sparade sökningar](./media/data-catalog-how-to-save-pin/04-managing.png)

* Om du vill ange ett nytt namn för den sparade sökningen väljer du **Byt namn**. Sök definitionen har inte ändrats.

* Om du vill ta bort den sparade sökningen från listan väljer du **ta bort** och bekräftar sedan borttagningen.

* Om du vill markera den sparade sökningen som standard Sök väljer du **Spara som standard**. Om du utför en "Tom" sökning från Azure Data Catalog start sida körs din standard sökning. Dessutom visas den sökning som är markerad som standard Sök högst upp i listan över **sparade sökningar** .

### <a name="organizational-saved-searches"></a>Sparade sökningar i organisationen
Alla användare i din organisation kan spara sökningar efter eget bruk. Data Catalog-administratörer kan också spara sökningar för alla användare i organisationen. När administratörer sparar en sökning visas en **resurs i företags** alternativet. Om du väljer det här alternativet delas den sparade sökningen för alla användare i organisationen.

 ![Data Catalog-organisatoriska sparade sökningar](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Fästa data till gångar
Med sparade sökningar kan du spara och återanvända Sök definitioner. Data till gångar som returneras av sökningarna kan ändras med tiden som innehållet i katalog ändringen. När du fäster data till gångar kan du uttryckligen identifiera enskilda data till gångar så att de blir enklare att komma åt utan att du behöver använda en sökning.

Det är enkelt att fästa en data till gång. Om du vill lägga till data till gången i den fästa listan klickar du bara på ikonen **Fäst** . Ikonen visas i hörnet av till gångs panelen i vyn sida vid sida och i kolumnen längst till vänster i listvyn på Azure Data Catalogs portalen.

![Data Catalog – PIN-ikonen för data till gång](./media/data-catalog-how-to-save-pin/05-pinning.png)

Det är lika enkelt att avfästa en data till gång. Klicka bara på ikonen för att **ta bort** för att växla inställningen för den valda till gången.

![Data Catalog-ikonen för att ta bort data till gångar](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Avsnittet Mina till gångar
Start sidan för Data Catalog Portal innehåller avsnittet **Mina till gångar** som visar till gångar av intresse för den aktuella användaren. Det här avsnittet innehåller både fästa till gångar och sparade sökningar.

![Avsnittet Mina till gångar på Start Sidan](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Sammanfattning
Azure Data Catalog tillhandahåller funktioner som gör det lättare att identifiera de data källor du behöver, så att du och andra organisations medlemmar kan spendera mindre tid på att söka efter data och mer tid på att arbeta med den. Sparade sökningar och fästa data till gångar bygger på dessa kärn funktioner så att användarna enkelt kan identifiera data källor som de arbetar med upprepade gånger.
