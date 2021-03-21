---
title: Lämna data feedback till Azure Maps | Microsoft Azure Maps
description: Ge feedback om data med hjälp av Microsoft Azure Maps-verktyget.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905340"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Ge data feedback till Azure Maps

Azure Maps har varit tillgängligt sedan maj 2018. Azure Maps har fått nya kart data, lättanvända REST API: er och kraftfulla SDK: er som stöder våra företags kunder med olika typer av affärs användnings fall. Den verkliga världen ändras varje sekund och det är viktigt att vi tillhandahåller en riktig digital representation av våra kunder. Våra kunder som planerar att öppna eller stänga anläggningar behöver våra kartor för att snabbt kunna uppdatera dem. Därför kan de effektivt planera för leverans-, underhålls-eller kund tjänster på rätt sätt. Vi har skapat webbplatsen för Azure Maps data feedback för att hjälpa våra kunder att tillhandahålla direkt data feedback. Kundernas data feedback skickas direkt till våra data leverantörer och deras kart redigerare. De kan snabbt utvärdera och införliva feedback i våra mappnings produkter.  

[Azure Maps data (för hands version) feedback-webbplatsen](https://feedback.azuremaps.com) är ett enkelt sätt för våra kunder att tillhandahålla feedback från kart data, särskilt på affärs punkter av intresse-och bostads adresser. Den här artikeln vägleder dig hur du ger olika typer av feedback på webbplatsen för Azure Maps feedback.

## <a name="add-a-business-place-or-a-residential-address"></a>Lägg till en företags plats eller en bostads adress 

Du kanske vill ge feedback om en intresse rad som saknas eller en bostads adress. Det finns två sätt att göra detta på. Öppna webbplatsen för Azure Map data feedback, Sök efter den saknade platsens koordinater och klicka sedan på Lägg till en plats

  ![Sök efter saknad plats](./media/how-to-use-feedback-tool/search-poi.png)

Eller så kan du interagera med kartan. Klicka på platsen för att släppa en PIN-kod på koordinaten och klicka på Lägg till en plats.

  ![Lägg till PIN-kod](./media/how-to-use-feedback-tool/add-poi.png)

När du klickar på dirigeras du till ett formulär för att ange motsvarande information för platsen.

  ![Lägg till en plats](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Åtgärda en affärs plats eller en bostads adress 

På feedback-webbplatsen kan du också söka efter och hitta en affärs plats eller en adress. Du kan ge feedback för att åtgärda adressen eller PIN-platsen om de inte är korrekta. Om du vill ge feedback för att åtgärda adressen använder du Sök fältet för att söka efter en företags plats eller en lokal adress. Klicka på platsen för ditt intresse från resultat listan. Klicka på "åtgärda den här platsen".

  ![Sök plats för att åtgärda](./media/how-to-use-feedback-tool/fix-place.png)

Om du vill ge feedback för att åtgärda adressen, fyller du i formuläret "korrigera en plats" och klickar sedan på knappen Skicka.

  ![korrigera formulär](./media/how-to-use-feedback-tool/fix-form.png)

Om PIN-koden för PIN-koden är fel markerar du kryss rutan på "korrigera en plats"-form som säger "PIN-platsen är felaktig". Flytta PIN-koden till rätt plats och klicka sedan på knappen Skicka.

  ![flytta PIN-plats](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Lägg till en kommentar 

Förutom att du kan söka efter en plats kan du också med hjälp av feedback-verktyget lägga till en fritext kommentar för information som är relaterad till platsen. Om du vill lägga till en kommentar söker du efter platsen eller klickar på platsen. Klicka på Lägg till en kommentar, Skriv en kommentar och klicka sedan på Skicka.

  ![Lägg till kommentar](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Spåra status 

Du kan också spåra statusen för din begäran genom att markera rutan "Jag vill spåra status" och ange din e-postadress när du gör en begäran. Du får en uppföljnings länk i e-postmeddelandet som innehåller en uppdaterad status för din begäran. 

  ![feedback-status](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Nästa steg

Om du vill publicera tekniska frågor som rör Azure Maps går du till:

* [Microsoft Q & A](/answers/topics/azure-maps.html)