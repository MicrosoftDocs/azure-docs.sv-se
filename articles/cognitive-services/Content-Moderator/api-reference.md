---
title: API-referens – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Lär dig mer om de olika innehålls moderatorn och se över API: er för Content Moderator.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "72757383"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API-referens

Du kan komma igång med Azure Content Moderator-API: er på följande sätt:

- I Azure Portal [prenumererar du på Content moderator API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Se [testa Content moderator på webben](quick-start.md) för att registrera dig med [verktyget Content moderator granskning](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>API:er för moderering

Du kan använda följande Content Moderator-API: er för att ställa in dina arbets flöden för efter redigering.

| Beskrivning | Referens |
| -------------------- |-------------|
| **API för bildmoderering**<br /><br />Skanna bilder och identifiera potentiellt vuxna och vågat innehåll genom att använda taggar, konfidens resultat och annan extraherad information. <br /><br />Använd den här informationen för att publicera, avvisa eller granska innehållet i ditt arbets flöde efter redigering. <br /><br />| [API-referens för bild moderator](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "API-referens för bild moderator")   |
| **API för textmoderering**<br /><br />Skanna text innehåll. Svordomar och person uppgifter returneras. <br /><br />Använd den här informationen för att publicera, avvisa eller granska innehållet i ditt arbets flöde efter redigering.<br /><br /> | [API-referens för text redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "API-referens för text redigering")   |
| **API för video moderator**<br /><br />Sök igenom videor och identifiera potentiellt vuxna och vågat innehåll. <br /><br />Använd den här informationen för att publicera, avvisa eller granska innehållet i ditt arbets flöde efter redigering.<br /><br /> | [Översikt över video moderator API](video-moderation-api.md "Översikt över video moderator API")   |
| **API för List hantering**<br /><br />Skapa och hantera anpassade exkluderings-eller inkluderings listor med bilder och text. Om det här alternativet är aktiverat gör **bild matchnings-** och **text skärms** åtgärderna suddig matchning av det skickade innehållet mot dina anpassade listor. <br /><br />För att få effektivitet kan du hoppa över det Machine Learning-baserade redigerings steget.<br /><br /> | [API-referens för List hantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "API-referens för List hantering")   |

## <a name="review-apis"></a>Omdömes-API:er

API: erna för granskning har följande komponenter:

| Beskrivning | Referens |
| -------------------- |-------------|
| **Jobb**<br /><br /> Starta skannings-och-granska redigerings arbets flöden för både bild-och text innehåll. Ett redigerings jobb skannar ditt innehåll med hjälp av bild redigerings-API: et och API: et för text redigering. Redigerings jobb använder definierade och standard arbets flöden för att skapa recensioner. <br /><br />När en mänsklig moderator har granskat de automatiskt tilldelade taggarna och förutsägelse data och skickat ett beslut om innehålls kontroll, skickar gransknings-API: n all information till API-slutpunkten.<br /><br /> | [Jobb referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Jobb referens")   |
| **Omdömen**<br /><br />Använd gransknings verktyget för att direkt skapa bilder eller text granskningar för mänskliga moderatorer.<br /><br /> När en mänsklig moderator har granskat de automatiskt tilldelade taggarna och förutsägelse data och skickat ett beslut om innehålls kontroll, skickar gransknings-API: n all information till API-slutpunkten.<br /><br /> | [Granska referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Granska referens")   |
| **Arbetsflöden**<br /><br />Skapa, uppdatera och få information om de anpassade arbets flöden som ditt team skapar. Du definierar arbets flöden med hjälp av gransknings verktyget. <br /> <br />Arbets flöden använder vanligt vis Content Moderator, men kan också använda vissa andra API: er som är tillgängliga som kopplingar i gransknings verktyget.<br /><br /> | [Arbets flödes referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Arbets flödes referens")   |