---
title: Vad är en arbets yta och ett projekt? – Anpassad översättare
titleSuffix: Azure Cognitive Services
description: I den här artikeln förklaras skillnaderna mellan en arbets yta och ett projekt samt projekt kategorier och etiketter för tjänsten Custom Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41586b2ec96d30fd583cbc9a746493ed9206548b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510750"
---
# <a name="what-is-a-custom-translator-workspace"></a>Vad är en anpassad översättare-arbetsyta?

En arbets yta är en arbets yta för att skriva och skapa ett anpassat översättnings system. En arbets yta kan innehålla flera projekt, modeller och dokument. Allt arbete som du gör i en anpassad översättare är inuti en angiven arbets yta.

Arbets ytan är privat för dig och de personer som du bjuder in till din arbets yta. Ej inbjudna personer har inte åtkomst till innehållet i din arbets yta. Du kan bjuda in så många personer som du vill i din arbets yta och ändra eller ta bort åtkomsten när som helst. Du kan också skapa en ny arbets yta. Som standard kommer en arbets yta inte innehålla några projekt eller dokument som finns i dina andra arbets ytor.

## <a name="what-is-a-custom-translator-project"></a>Vad är ett anpassat översättare-projekt?

Ett projekt är ett omslutnings gränssnitt för en modell, dokument och tester. Varje projekt innehåller automatiskt alla dokument som överförs till den arbets ytan som har rätt språk par. Om du till exempel har både ett engelskt till spanska-projekt och ett spanskt till engelskt projekt, kommer samma dokument att inkluderas i båda projekten. Varje projekt har en associerad kategori kategori som används när du frågar [v3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) för översättningar. Kategorinr är en parameter som används för att hämta översättningar från ett anpassat system som skapats med anpassad översättare.

## <a name="project-categories"></a>Projekt kategorier

Kategorin identifierar domänen – det terminologi och den stil som du vill använda – för projektet. Välj den kategori som är mest relevant för dina dokument. I vissa fall påverkar ditt val av kategori direkt beteendet för den anpassade översättare.

Vi har två uppsättningar bas linje modeller. De är allmänna och tekniska. Om kategori **tekniken** väljs används teknik bas linje modeller. För alla andra kategori val används de allmänna bas linje modellerna. Teknik bas linje modellen är väl i teknik domän, men den visar lägre kvalitet om meningarna som används för översättning inte hamnar inom teknik domänen. Vi föreslår att kunderna bara väljer kategori teknik om meningarna är strikta inom teknik domänen.

I samma arbets yta kan du skapa projekt för samma språk par i olika kategorier. Anpassad översättare förhindrar att ett duplicerat projekt skapas med samma språk par och kategori. Genom att använda en etikett i projektet kan du undvika den här begränsningen. Använd inte etiketter om du inte skapar översättnings system för flera klienter, som att lägga till en unik etikett i projektet visas i kategorin projekt.

## <a name="project-labels"></a>Projekt etiketter

Med anpassad översättare kan du tilldela en projekt etikett till projektet. Projekt etiketten skiljer sig mellan flera projekt med samma språk par och kategori. Vi rekommenderar att du inte använder Project-etiketter om det inte behövs.

Projekt etiketten används som en del av kategorin kategori. Om projekt etiketten lämnas kvar eller anges identiskt mellan projekt, kommer projekt med samma kategori och *olika* språk par att dela samma kategori. Den här metoden är fördelaktig eftersom det låter dig eller din kund växla mellan språk när du använder API: et för text översättning utan att oroa dig för en kategori som är unik för varje projekt.

Om jag till exempel ville aktivera översättningar i teknik domänen från engelska till franska och från franska till engelska, skulle jag skapa två projekt: ett för engelska \> och franska, och ett för franska – \> engelska. Jag skulle ange samma kategori (teknik) för båda och lämna projekt etiketten tom. Kategorin för båda projekten skulle matcha, så jag kan fråga API: t för både engelska och franska översättningar utan att behöva ändra min kategori.

Om du är en språk tjänst leverantör och vill betjäna flera kunder med olika modeller som behåller samma kategori-och språk par, är det ett bra beslut att använda en projekt etikett för att skilja mellan kunderna.

## <a name="next-steps"></a>Nästa steg

- Läs om [utbildning och modell](training-and-model.md) för att lära dig hur du effektivt skapar en översättnings modell.
