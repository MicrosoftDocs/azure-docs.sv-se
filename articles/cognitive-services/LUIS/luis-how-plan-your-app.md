---
title: Planera din app-LUIS
description: Disponera relevanta appar och entiteter och skapa sedan dina program planer i Language Understanding intelligenta tjänster (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018964"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planera ditt LUIS-AppData med ämnes domän och data extrahering

Ett LUIS app-schema innehåller [intentor](luis-glossary.md#intent) och [entiteter](luis-glossary.md#entity) som är relevanta för din ämnes [domän](luis-glossary.md#domain). Avsikten klassificerar användarens [yttranden](luis-glossary.md#utterance)och enheterna extraherar data från användaren yttranden.

## <a name="identify-your-domain"></a>Identifiera din domän

En LUIS-app är centrerad runt en ämnes domän. Du kan till exempel ha en rese-app som hanterar bokning av biljetter, flygningar, hotell och hyr bilar. En annan app kan ge innehåll som handlar om hur man utövar, spårar lämplighet och ställer in mål. Att identifiera domänen hjälper dig att hitta ord eller fraser som är relevanta för din domän.

> [!TIP]
> LUIS erbjuder [färdiga domäner](./howto-add-prebuilt-models.md) för många vanliga scenarier. Kontrol lera om du kan använda en fördefinierad domän som start punkt för din app.

## <a name="identify-your-intents"></a>Identifiera dina avsikter

Tänk på vilka [avsikter](luis-concept-intent.md) som är viktiga för programmets uppgift.

Låt oss ta exemplet på en rese app, med funktioner för att boka en flygning och kontrol lera väder på användarens mål. Du kan definiera `BookFlight` och `GetWeather` avsikten för dessa åtgärder.

I en mer komplex app med fler funktioner har du fler syften och du bör definiera dem noggrant så att avsikterna inte är för speciella. Till exempel `BookFlight` och kan `BookHotel` behöva vara separata avsikter, men `BookInternationalFlight` `BookDomesticFlight` det kan vara för likt.

> [!NOTE]
> Vi rekommenderar att du bara använder så många syften som du behöver för att utföra funktionerna i din app. Om du definierar för många avsikter blir det svårare för LUIS att klassificera yttranden korrekt. Om du definierar för få kan de vara så generella att de överlappar varandra.

Om du inte behöver identifiera övergripande användar avsikt lägger du till alla exempel på användarens yttranden till avsikten `None` . Om din app växer för att kräva fler avsikter kan du skapa dem senare.

## <a name="create-example-utterances-for-each-intent"></a>Skapa exempel-yttranden för varje avsikt

Undvik att skapa för många yttranden för varje avsikt att börja med. När du har fastställt avsikterna skapar du 15 till 30 exempel yttranden per avsikt. Varje uttryck bör skilja sig från den tidigare angivna yttranden. En bra variation i yttranden omfattar övergripande antal ord, Word-val, verb och [interpunktion](luis-reference-application-settings.md#punctuation-normalization).

Mer information finns i [förstå god yttranden för Luis-appar](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifiera dina entiteter

I exemplet yttranden identifierar du de entiteter som du vill extrahera. Om du vill boka en flygning behöver du information som mål, datum, flyg, biljett kategori och rese klass. Skapa entiteter för dessa data typer och markera sedan [entiteterna](luis-concept-entity-types.md) i exemplet yttranden. Entiteter är viktiga för att kunna utföra en avsikt.

När du bestämmer vilka entiteter som ska användas i appen, Tänk på att det finns olika typer av entiteter för att fånga relationer mellan objekt typer. [Entiteter i Luis](luis-concept-entity-types.md) ger mer information om de olika typerna.

> [!TIP]
> LUIS erbjuder [färdiga entiteter](./howto-add-prebuilt-models.md) för vanliga scenarier med konversations användare. Överväg att använda färdiga entiteter som utgångs punkt för program utvecklingen.

## <a name="resolution-with-intent-or-entity"></a>Matchning med avsikt eller entitet?

I många fall, särskilt när du arbetar med naturlig konversation, tillhandahåller användare en uttryck som kan innehålla mer än en funktion eller avsikt. För att lösa detta är en allmän tumregel att förstå att åter givningen av utdata kan göras i både avsikter och entiteter. Den här presentationen ska mappas till dina klient program åtgärder och behöver inte begränsas till avsikten.

**Int-** överordnad-ties är det begrepp som åtgärder (som vanligt vis betraktas som avsikter) kan också fångas in som entiteter och förlitas i det här formuläret i utdata-JSON där du kan mappa den till en speciell åtgärd. _Negation_ är en vanlig användning för att utnyttja detta beroende av både avsikt och enhet för fullständig extrahering.

Tänk på följande två yttranden som är mycket nära att tänka på när du väljer det, men som har olika resultat:

|Yttrande|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

I stället för att ha två separata intentor skapar du en enda avsikt med en `FlightAction` Machine Learning-entitet. Machine Learning-entiteten bör extrahera information om åtgärden för både en schemaläggning och en avbrotts förfrågan samt antingen en ursprungs plats eller en målplats.

`FlightAction`Entiteten är strukturerad i följande pseudo-schema för Machine Learning-entiteten och-underentiteter:

* FlightAction
    * Action
    * Ursprung
    * Mål

För att hjälpa extraheringen lägga till funktioner i underentiteterna. Du väljer dina funktioner baserat på den vokabulär som du förväntar dig att se i User yttranden och de värden som du vill ska returneras i förutsägelse svaret.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär LUIS Development lifecylce](luis-concept-app-iteration.md)