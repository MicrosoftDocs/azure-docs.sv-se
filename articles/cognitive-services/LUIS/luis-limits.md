---
title: Gränser – LUIS
description: Den här artikeln innehåller de kända gränserna för Azure Cognitive Services Language Understanding (LUIS). LUIS har flera gräns områden. Modell gräns kontroller för avsikter, entiteter och funktioner i LUIS. Kvot gränser baserat på nyckel typ. Tangent bords kombinationen styr LUIS-webbplatsen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976800"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Begränsningar för din LUIS-modell och nycklar
LUIS har flera gräns områden. Den första är [modell gränsen](#model-limits), som styr avsikter, entiteter och funktioner i Luis. Det andra avsnittet är [kvot gränser](#key-limits) baserat på nyckel typ. Ett tredje begränsat utrymme är [tangentkombinationen](#keyboard-controls) för att kontrol lera Luis-webbplatsen. Ett fjärde område är [Mappning av världs regionen](luis-reference-regions.md) mellan Luis Authoring-webbplatsen och API: erna för Luis- [slutpunkt](luis-glossary.md#endpoint) .

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Modell gränser

Om din app överskrider LUIS-modellens gränser bör du överväga att använda en [Luis sändnings](luis-concept-enterprise.md#dispatch-tool-and-model) app eller en [Luis-behållare](luis-container-howto.md).

|Område|Gräns|
|--|:--|
| [Appens namn][luis-get-started-create-app] | * Standard-största antal bokstäver |
| Program| 500 program per Azure Authoring-resurs |
| [Batch-testning][batch-testing]| 10 data uppsättningar, 1000 yttranden per data uppsättning|
| Explicit lista | 50 per program|
| Externa entiteter | inga gränser |
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och det krävs _ingen_ avsikt.<br>[Skickat](https://aka.ms/dispatch-tool) program har motsvarande 500-sändnings källor.|
| [Lista entiteter](./luis-concept-entity-types.md) | Överordnad: 50, underordnad: 20 000 objekt. Kanoniskt namn är * standard tecknen max. Synonym värden har ingen längd begränsning. |
| [enheter för maskin inlärning + roller](./luis-concept-entity-types.md):<br> sammansättning<br>gång<br>enhets roll|En gräns på antingen 100 överordnade entiteter eller 330 entiteter, beroende på vilken gräns användaren träffar först. En roll räknas som en entitet för den här begränsningen. Ett exempel är ett sammansatt objekt med en enkel entitet, som har två roller: 1 sammansatt + 1 enkla + 2 roller = 4 av 330-enheterna.<br>Underentiteter kan kapslas upp till 5 nivåer, med högst 10 underordnade per nivå.|
|Modell som en funktion| Det maximala antalet modeller som kan användas som en funktion för en speciell modell som är 10 modeller. Det maximala antalet fras listor som används som en funktion för en speciell modell som är 10 fras listor.|
| [Förhands granskning-dynamiska List enheter](./luis-migration-api-v3.md)|2 listor med ~ 1 kB per förfrågan för förutsägelse slut punkt|
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Den maximala längden för mönstret är 400 tecken.<br>3 mönster. alla entiteter per mönster<br>Högst 2 kapslade valfria texter i mönstret|
| [Mönster. alla](./luis-concept-entity-types.md)|100 per program, 3 mönster. alla entiteter per mönster |
| [Fras lista][phrase-list]|500 fras listor. 10 global fras listor på grund av modellen som en funktions gräns. Listan över icke-utbytbara fraser har högst 5 000 fraser. Listan över utbytbara fraser har högst 50 000 fraser. Maximalt antal fraser per program 500 000-fraser.|
| [Fördefinierade entiteter](./howto-add-prebuilt-models.md) | ingen gräns|
| [Lägga till entiteter för reguljära uttryck](./luis-concept-entity-types.md)|20 entiteter<br>500-Character max. per reguljärt uttryck enhets mönster|
| [Roller](./luis-concept-entity-types.md)|300 roller per program. 10 roller per entitet|
| [Yttrande][utterances] | 500 tecken<br><br>Om du har text längre än den här tecken gränsen måste du segmentera uttryck innan indatamängden till LUIS och du får enskilda svar per segment. Det finns uppenbara raster som du kan arbeta med, till exempel skiljetecken och långa pauser i tal.|
| [Uttryck-exempel][utterances] | 15 000 per program-det finns ingen gräns för antalet yttranden per avsikt<br><br>Om du behöver träna programmet med fler exempel kan du använda en metod för [sändnings](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) modellen. Du tränar enskilda LUIS-appar (kallade underordnade appar till den överordnade sändnings appen) med en eller flera avsikter och sedan tränar du en sändnings app som innehåller exempel från varje underordnad LUIS Apps yttranden för att dirigera förutsägelsen till rätt underordnad app. |
| [Versioner](./luis-concept-app-iteration.md)| 100-versioner per program |
| [Versions namn][luis-how-to-manage-versions] | 128 tecken |

* Standard tecken Max är 50 tecken.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Namn unikhet

Objekt namn måste vara unika jämfört med andra objekt på samma nivå.

|Objekt|Begränsningar|
|--|--|
|Avsikt, entitet|Alla namn på avsikt och entiteter måste vara unika i en version av en app.|
|ML-enhets komponenter|Alla enhets komponenter för maskin inlärning (underordnade entiteter) måste vara unika inom entiteten för komponenter på samma nivå.|
|Funktioner | Alla namngivna funktioner, till exempel fras listor, måste vara unika inom en version av en app.|
|Entitetsroller|Alla roller på en entitet eller enhets komponent måste vara unika när de befinner sig på samma enhets nivå (överordnad, underordnad, grandchild osv.).|

## <a name="object-naming"></a>Objekt namn

Använd inte följande tecken i följande namn.

|Objekt|Uteslut tecken|
|--|--|
|Namn på avsikt, entitet och roll|`:`<br>`$` <br> `&`|
|Versions namn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Resursanvändning och begränsningar

Språk förståelsen har separata resurser, en typ för redigering och en typ för att skicka frågor till förutsägelse slut punkten. Mer information om skillnaderna mellan nyckel typer finns i [Redigera och köra slut punkts nycklar för förutsägelse i Luis](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Redigera resurs gränser

Använd _typen_, `LUIS.Authoring` när du filtrerar resurser i Azure Portal. LUIS begränsar 500 program per Azure Authoring-resurs.

|Skapar resurs|Redigera TPS|
|--|--|
|Starter|1 miljon/månad, 5/sekund|
|F0 – kostnads fri nivå |1 miljon/månad, 5/sekund|

* TPS = transaktioner per sekund

[Läs mer om priser.][pricing]

### <a name="query-prediction-resource-limits"></a>Resurs gränser för fråga förutsägelse

Använd _typen_, `LUIS` när du filtrerar resurser i Azure Portal. Slut punkts resursen för LUIS-som används i körnings miljön är bara giltig för slut punkts frågor.

|Fråga förutsägelse resurs|Fråga TPS|
|--|--|
|F0 – kostnads fri nivå |10 000 per månad, 5/sekund|
|S0 – standard nivå|50/sekund|

### <a name="sentiment-analysis"></a>Attitydanalys

[Sentiment analys integrering](luis-how-to-publish-app.md#enable-sentiment-analysis), som innehåller information om sentiment, tillhandahålls utan någon annan Azure-resurs.

### <a name="speech-integration"></a>Tal integrering

[Tal integrering](../speech-service/how-to-recognize-intents-from-speech-csharp.md) ger 1000 slut punkts förfrågningar per enhets kostnad.

[Läs mer om priser.][pricing]

## <a name="keyboard-controls"></a>Tangent bords kontroller

|Tangent bords inmatare | Beskrivning |
|--|--|
|Kontroll + E|växlar mellan token och entiteter i yttranden-listan|

## <a name="website-sign-in-time-period"></a>Inloggnings tids period för webbplats

Din inloggnings åtkomst är i **60 minuter**. Efter den här tids perioden får du det här felet. Du måste logga in igen.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
