---
title: Fel söknings fel vid körning av ett anpassat kommando program
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig att felsöka körnings fel i ett program för anpassade kommandon.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196915"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Fel söknings fel vid körning av ett anpassat kommando program

Den här artikeln beskriver hur du felsöker när du ser fel när du kör programmet för anpassade kommandon. 

## <a name="connection-failed"></a>Anslutningen misslyckades

Om programmet kör anpassade kommandon från [klient programmet (med tal-SDK)](./how-to-custom-commands-setup-speech-sdk.md) eller [Windows Voice Assistant-klienten](./how-to-custom-commands-developer-flow-test.md), kan det uppstå anslutnings fel på det sätt som anges nedan:

| Felkod | Information |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: WebSocket-uppgraderingen misslyckades med ett autentiseringsfel |
| [1002](#error-1002) | Servern returnerade status koden 404 när status koden 101 förväntades. |

### <a name="error-401"></a>Fel 401
- Den region som anges i klient programmet överensstämmer inte med regionen för det anpassade kommando programmet

- Tal resurs nyckeln är ogiltig
    
    Kontrol lera att din tal resurs nyckel är korrekt.

### <a name="error-1002"></a>Fel 1002 
- Det anpassade kommando programmet har inte publicerats
    
    Publicera ditt program i portalen.

- Det anpassade kommandot applicationId är inte giltigt

    Kontrol lera att ditt anpassade kommando program-ID är korrekt.
 anpassat kommando program utanför din tal resurs

    Kontrol lera att det anpassade kommando programmet har skapats under din tal resurs.

Mer information om hur du felsöker anslutnings problem finns i [fel sökning av Windows Voice Assistant-klienten](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>Dialog rutan har avbrutits

När du kör programmet för anpassade kommandon avbryts dialog rutan när några fel inträffar.

- Om du testar programmet i portalen visar det direkt avbrotts beskrivningen och spelar upp ett fel Earcon. 

- Om du kör programmet med [Windows Voice Assistant-klienten](./how-to-custom-commands-developer-flow-test.md)skulle det leda till ett fel Earcon. Du kan hitta **händelsen: CancelledDialog** under **aktivitets loggarna**.

- Om du följer vårt exempel [klient program för klient program (med tal-SDK)](./how-to-custom-commands-setup-speech-sdk.md)skulle det leda till ett fel Earcon. Du kan hitta **händelsen: CancelledDialog** under **status**.

- Om du skapar ett eget klient program kan du alltid utforma dina önskade logiker för att hantera CancelledDialog-händelserna.

CancelledDialog-händelsen består av avbrotts kod och beskrivning, enligt listan nedan:

| Avbrotts kod | Beskrivning av annullering |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Ingen förloppet gjordes efter det högsta antalet tillåtna tillåtna |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Tolkning av användnings kvot har överskridits |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Det gick inte att ansluta till tolken |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Det går inte att komma åt det här programmet med den aktuella prenumerationen |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | Indatamängden överskrider den maximala längd som stöds för tolken |
| [RecognizerNotFound](#recognizer-not-found) | Tolken hittades inte |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Ogiltig fråga för tolken |
| [RecognizerError](#recognizer-return-an-error) | Tolken returnerar ett fel |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Ingen förloppet gjordes efter det högsta antalet tillåtna tillåtna
Dialog rutan avbryts när en begärd plats inte har uppdaterats efter ett visst antal varv. Build-Max numret är 3.

### <a name="recognizer-usage-quota-exceeded"></a>Tolkning av användnings kvot har överskridits
Language Understanding (LUIS) har gränser för resursanvändning. Vanligen kan fel meddelandet "tolkning av användnings kvoten överskridas av: 
- Din LUIS-redigering överskrider gränsen

    Lägg till en förutsägelse resurs till dina anpassade kommandon-program: 
    1. Gå till **Inställningar**, Luis-resurs
    1. Välj en förutsägelse resurs från **förutsägelse resurs** eller klicka på **Skapa ny resurs** 

- Din LUIS förutsägelse resurs överskrider gränsen

    Om du använder en F0 förutsägelse resurs har den gränsen 10 000 per månad, 5 frågor/sekund.

Mer information om LUIS resurs gränser finns Language Understanding resursanvändning [och gräns](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Det gick inte att ansluta till tolken
Det innebär vanligt vis att det tillfälliga anslutnings försöket till Language Understanding (LUIS) identifieras. Försök igen och problemet bör lösas.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Det går inte att komma åt det här programmet med den aktuella prenumerationen
Din prenumeration har inte behörighet att komma åt LUIS-programmet. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Indatamängden överskrider den maximala längd som stöds
Indatatypen har överskridit 500 tecken. Vi tillåter bara högst 500 tecken för inuttryck.

### <a name="invalid-query-for-the-recognizer"></a>Ogiltig fråga för tolken
Indatatypen har överskridit 500 tecken. Vi tillåter bara högst 500 tecken för inuttryck.

### <a name="recognizer-return-an-error"></a>Tolken returnerar ett fel
LUIS-tolken returnerade ett fel när du försökte identifiera dina inaktuella ininformation.

### <a name="recognizer-not-found"></a>Tolken hittades inte
Det går inte att hitta tolknings typen som anges i dialog modellen med anpassade kommandon. För närvarande stöder vi bara [language Understanding-tolken (Luis)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Andra vanliga fel
### <a name="unexpected-response"></a>Oväntat svar
Oväntade svar kan bero på flera saker. Några kontroller att börja med:
- Ja/nej-avsikt i exempel meningar

    Eftersom vi för närvarande inte har stöd för ja/nej-avsikt förutom när du använder med bekräftelse funktionen. Alla Ja/Nej-avsikter som definieras i exemplen i exempel meningar identifieras inte.

- Liknande avsikter och exempel meningar mellan kommandon

    LUISs igenkännings precisionen kan påverkas när två kommandon delar liknande intentor och exempel meningar. Du kan prova att göra kommandon och exempel meningar så distinkta som möjligt.

    Bästa praxis för att förbättra igenkännings precisionen finns i [bästa praxis för Luis](../luis/luis-concept-best-practices.md).

- Dialog rutan har avbrutits
    
    Kontrol lera orsakerna till annullering i avsnittet ovan.

### <a name="error-while-rendering-the-template"></a>Fel vid åter givning av mallen
En odefinierad parameter används i tal svaret. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Objekt referensen har inte angetts till en instans av ett objekt
Du har en tom parameter i JSON-nyttolasten definierad i **Skicka aktivitet till klient** åtgärd.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Se exempel på GitHub](https://aka.ms/speech/cc-samples)
