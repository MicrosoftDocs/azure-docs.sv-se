---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: trbye
ms.openlocfilehash: 4725a1a9cf2cb74655a37ac27a0a86f10d7f4bb9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052987"
---
I den här snabb starten använder du tjänsten [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) och language Understanding (Luis) för att identifiera avsikter från ljuddata som fångats från en mikrofon. Mer specifikt använder du talet SDK för att avbilda tal och en fördefinierad domän från LUIS för att identifiera avsikter för start automatisering, som att aktivera och inaktivera ett ljus. 

Efter att ha uppfyllt några krav, tar det bara några steg att känna igen tal och identifiera syften från en mikrofon:

> [!div class="checklist"]
>
> * Skapa ett `SpeechConfig` objekt från din prenumerations nyckel och region.
> * Skapa ett `IntentRecognizer` objekt med hjälp av `SpeechConfig` objektet ovan.
> * `IntentRecognizer`Starta igenkännings processen för en enda uttryck med hjälp av objektet.
> * Granska den `IntentRecognitionResult` returnerade.

