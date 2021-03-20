---
title: Data ändring – LUIS
description: Lär dig hur data kan ändras innan förutsägelser i Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026018"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Ändra uttryck-data före eller under förutsägelse
LUIS tillhandahåller sätt att ändra uttryck före eller under förutsägelsen. Detta inkluderar att [åtgärda stavnings](luis-tutorial-bing-spellcheck.md)-och åtgärda tids zons problem för färdiga [datetimeV2](luis-reference-prebuilt-datetimev2.md).

## <a name="correct-spelling-errors-in-utterance"></a>Korrigera stavfel i uttryck


### <a name="v3-runtime"></a>V3-körning

Förbearbeta text för stavnings korrigeringar innan du skickar uttryck till LUIS. Använd exempel yttranden med rätt stavning för att se till att du får rätt förutsägelser.

Använd [stavningskontroll i Bing](../bing-spell-check/overview.md) för att korrigera text innan du skickar den till Luis.

### <a name="prior-to-v3-runtime"></a>Före v3-körning

LUIS använder [API för stavningskontroll i Bing v7](../Bing-Spell-Check/overview.md) för att korrigera stavfel i uttryck. LUIS behöver nyckeln som är kopplad till tjänsten. Skapa nyckeln och Lägg sedan till nyckeln som en QueryString-parameter i [slut punkten](https://go.microsoft.com/fwlink/?linkid=2092356).

Slut punkten kräver två parametrar för att stavnings korrigeringar ska fungera:

|Param|Värde|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[API för stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) slut punkts nyckel för v7|

När [API för stavningskontroll i Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) identifierar ett fel returneras den ursprungliga uttryck och de korrigerade uttryck returneras tillsammans med förutsägelser från slut punkten.

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Lista över tillåtna ord
Stavnings kontrolls-API: t för Bing som används i LUIS stöder inte en lista över ord som ska ignoreras under stavnings kontrollen. Om du behöver tillåta en lista över ord eller akronymer, bearbetar du uttryck i klient programmet innan du skickar uttryck till LUIS för avsikts förutsägelse.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Ändra tidszon för en fördefinierad datetimeV2-entitet
När en LUIS-app använder den fördefinierade [datetimeV2](luis-reference-prebuilt-datetimev2.md) -entiteten kan ett datetime-värde returneras i förutsägelse svaret. Tids zonen för begäran används för att fastställa korrekt datum/tid för att returnera. Om begäran kommer från en robot eller ett annat centraliserat program innan du kommer till LUIS, korrigerar du LUIS för timezone.

### <a name="v3-prediction-api-to-alter-timezone"></a>V3 förutsägelse API för att ändra tidszon

I v3 `datetimeReference` bestämmer timezone-förskjutningen. Läs mer om [v3-förutsägelser](luis-migration-api-v3.md#v3-post-body).

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 förutsägelse-API för att ändra tidszon
Tids zonen korrigeras genom att lägga till användarens tidszon till slut punkten med hjälp av `timezoneOffset` parametern som baseras på API-versionen. Värdet för parametern ska vara ett positivt eller negativt tal, i minuter, för att ändra tiden.

#### <a name="v2-prediction-daylight-savings-example"></a>Exempel på v2-förutsägelse, sommar
Om du behöver den returnerade förskapade datetimeV2 för att justera för sommar tid, bör du använda parametern QueryString med ett +/-värde i minuter för [slut punkts](https://go.microsoft.com/fwlink/?linkid=2092356) frågan.

Lägg till 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

Ta bort 60 minuter:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>V2-förutsägelse C#-kod fastställer korrekt värde för parametern

I följande C#-kod används [TimeZoneInfo](/dotnet/api/system.timezoneinfo) -klassens [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) -metod för att fastställa rätt förskjutnings värde baserat på system tid:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Korrigera stavfel i den här självstudien](luis-tutorial-bing-spellcheck.md)