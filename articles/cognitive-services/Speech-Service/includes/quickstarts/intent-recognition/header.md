---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: a752b723980c45e1488f1471ee8139190853b54c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97966616"
---
I den här snabb starten använder du tjänsten [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och language Understanding (Luis) för att identifiera avsikter från ljuddata som fångats från en mikrofon. Mer specifikt använder du talet SDK för att avbilda tal och en fördefinierad domän från LUIS för att identifiera avsikter för start automatisering, som att aktivera och inaktivera ett ljus. 

Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal och identifiera syften från en mikrofon:

> [!div class="checklist"]
>
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `IntentRecognizer` objekt med hjälp av `SpeechConfig` objektet ovan.
> * `IntentRecognizer`Starta igenkännings processen för en enda uttryck med hjälp av objektet.
> * Granska den `IntentRecognitionResult` returnerade.

> [!NOTE]
> Du kan skapa en LanguageUnderstandingModel genom att skicka en slut punkts-URL till FromEndpoint-metoden.
> Tal-SDK stöder endast LUIS v 2.0-slut punkter och LUIS v 2.0-slutpunkter följer alltid ett av följande två mönster:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
