---
title: Anpassa en person modell med Video Indexer API
titleSuffix: Azure Media Services
description: Lär dig hur du anpassar en person modell med Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: dced3cde1aae082bd64d785212b40e100013e0bf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312900"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassa en person modell med Video Indexer API

Video Indexer stöder ansikts igenkänning och kändis igenkänning för video innehåll. Kändis igenkännings funktionen täcker cirka 1 000 000 ansikten baserat på den begärda data källan, till exempel IMDB-, Wikipedia-och Top LinkedIn-påverkan. Ansikten som inte känns igen av kändis igenkännings funktionen har identifierats men lämnats utan namn. När du har laddat upp videon till Video Indexer och fått tillbaka resultatet kan du gå tillbaka och namnge de ansikten som inte kändes igen. När du har namngett ett ansikte med ett namn läggs ansikte och namn till i kontots person modell. Video Indexer kommer sedan att identifiera den här ytan i dina kommande videor och tidigare videor.

Du kan använda Video Indexer API för att redigera ansikten som upptäcktes i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer webbplats, som beskrivs i [Anpassa person modellen med hjälp av video Indexer webbplats](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Hantera flera person modeller

Video Indexer stöder flera person modeller per konto. Den här funktionen är för närvarande endast tillgänglig via Video Indexer API: er.

Om ditt konto tillgodoser olika användnings fall kan du vilja skapa flera person modeller per konto. Om ditt innehåll till exempel är relaterat till idrotts kan du skapa en separat person modell för varje sport (fotboll, basket boll, fotboll osv.).

När en modell har skapats kan du använda den genom att ange modell-ID: t för en speciell person modell vid överföring/indexering eller Omindexering av en video. Träna en ny ansikte för en video uppdaterar den speciella anpassade modell som videon var kopplad till.

Varje konto har en gräns på 50 person modeller. Om du inte behöver stöd för flera person modeller ska du inte tilldela ett ID för person modeller till videon vid överföring/indexering eller Omindexering. I det här fallet använder Video Indexer standard modellen för anpassade personer i ditt konto.

## <a name="create-a-new-person-model"></a>Skapa en ny person modell

Om du vill skapa en ny person modell i det angivna kontot använder du [skapa en person modell](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) -API.

Svaret innehåller namnet och det genererade modell-ID: t för den person modell som du nyss skapade enligt formatet i exemplet nedan.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Du använder sedan **ID-** värdet för parametern **personModelId** när du [laddar upp en video för att indexera](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) eller [Indexera om en video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video).

## <a name="delete-a-person-model"></a>Ta bort en person modell

Om du vill ta bort en anpassad person modell från det angivna kontot använder du [ta bort en person modell](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) -API.

När person modellen har tagits bort förblir indexet för dina aktuella videor som använde den borttagna modellen oförändrade tills du indexerar om dem. Vid Omindexering identifieras inte de ansikten som beskrevs i den borttagna modellen av Video Indexer i dina aktuella videor som indexerats med modellen, men ansikten kommer fortfarande att identifieras. Dina aktuella videor som indexeras med den borttagna modellen använder nu ditt kontos standard person modell. Om ansikten från den borttagna modellen också är namngivna i ditt kontos standard modell, kommer dessa ansikten att fortsätta att kännas igen i videor.

Det finns inget returnerat innehåll när person modellen har tagits bort.

## <a name="get-all-person-models"></a>Hämta alla person modeller

Om du vill hämta alla person modeller på det angivna kontot använder du [Hämta en person modell](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) -API.

Svaret innehåller en lista över alla person modeller i ditt konto (inklusive standard person modellen i det angivna kontot) och var och en av deras namn och ID: n efter formatet i exemplet nedan.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Du kan välja vilken modell du vill använda för en video genom att använda `id` värdet för person modellen för `personModelId` parametern när [du laddar upp en video för att indexera](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) eller indexera om [en video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video).

## <a name="update-a-face"></a>Uppdatera ett ansikte

Med det här kommandot kan du uppdatera en ansikte i videon med ett namn med ID: t för videon och ID: t för FACET. Den här åtgärden uppdaterar sedan den person modell som videon var kopplad till vid uppladdning/indexering eller Omindexering. Om ingen person modell har tilldelats, uppdaterar den kontots standard person modell.

Systemet identifierar sedan förekomsterna av samma ansikte i dina andra aktuella videor som delar samma person modell. Det kan ta lite tid att identifiera ansikte i dina andra aktuella videor eftersom det är en batch-process.

Du kan uppdatera en ansikte som Video Indexer identifieras som en kändis med ett nytt namn. Det nya namnet som du ger företräde framför den inbyggda kändis-igenkänningen.

Om du vill uppdatera FACET använder du [Uppdatera ett video ansikts](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) -API.

Namnen är unika för person modeller, så om du ger två olika ansikten i samma person modell samma `name` parameter värde video Indexer Visa ansikten som samma person och konvergerar dem när du har indexerat om videon.

## <a name="next-steps"></a>Nästa steg

[Anpassa person modellen med hjälp av Video Indexer webbplats](customize-person-model-with-website.md)
