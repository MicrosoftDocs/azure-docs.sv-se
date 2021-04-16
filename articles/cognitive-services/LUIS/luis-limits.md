---
title: Gränser – LUIS
description: Den här artikeln innehåller kända begränsningar för Azure Cognitive Services Language Understanding (LUIS). LUIS har flera begränsningar. Modellgräns styr avsikter, entiteter och funktioner i LUIS. Kvotgränser baserat på nyckeltyp. Tangentbordskombinationen styr LUIS-webbplatsen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497208"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Begränsningar för din LUIS-modell och nycklar
LUIS har flera gränsområden. Den första är [modellgränsen](#model-limits), som styr avsikter, entiteter och funktioner i LUIS. Det andra området är [kvotgränser baserat](#key-limits) på nyckeltyp. Ett tredje område med gränser är [tangentbordskombinationen för](#keyboard-controls) att styra LUIS-webbplatsen. Ett fjärde område är [världsregionsmappningen mellan](luis-reference-regions.md) LUIS-redigeringswebbplatsen och LUIS-slutpunkts-API:erna. [](luis-glossary.md#endpoint)

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Modellbegränsningar

Om din app överskrider LUIS-modellgränserna bör du överväga att använda en [LUIS-dispatchapp](luis-concept-enterprise.md#dispatch-tool-and-model) eller att använda en [LUIS-container.](luis-container-howto.md)

|Område|Gräns|
|--|:--|
| [Appens namn][luis-get-started-create-app] | *Max för standardtecken |
| Program| 500 program per Azure-redigeringsresurs |
| [Batch-testning][batch-testing]| 10 datauppsättningar, 1 000 yttranden per datauppsättning|
| Explicit lista | 50 per program|
| Externa entiteter | inga gränser |
| [Avsikter][intents]|500 per program: 499 anpassade avsikter och avsikten _None (Ingen)._<br>[Det dispatchbaserade](https://aka.ms/dispatch-tool) programmet har motsvarande 500 dispatchkällor.|
| [Lista entiteter](./luis-concept-entity-types.md) | Överordnad: 50, underordnad: 20 000 objekt. Kanoniskt namn är *standardtecken max. Synonymvärden har ingen längdbegränsning. |
| [maskininlärningsentiteter + roller:](./luis-concept-entity-types.md)<br> Sammansatt<br>Enkel<br>entitetsroll|En gräns på antingen 100 överordnade entiteter eller 330 entiteter, beroende på vilken gräns som användaren når först. En roll räknas som en entitet för den här gränsen. Ett exempel är en sammansatt med en enkel entitet som har 2 roller: 1 sammansatt + 1 enkel + 2 roller = 4 av de 330 entiteterna.<br>Underentiteter kan kapslas upp till 5 nivåer, med högst 20 barn per nivå.|
|Modell som en funktion| Maximalt antal modeller som kan användas som en funktion för en specifik modell för att vara 10 modeller. Det maximala antalet fraslistor som används som en funktion för en specifik modell är 10 fraslistor.|
| [Förhandsversion – Dynamiska listentiteter](./luis-migration-api-v3.md)|2 listor med ~1 000 per begäran för frågeförutsägelseslutpunkt|
| [Mönster](luis-concept-patterns.md)|500 mönster per program.<br>Maximal längd på mönstret är 400 tecken.<br>3 Pattern.any-entiteter per mönster<br>Högst 2 kapslade valfria texter i mönster|
| [Pattern.any](./luis-concept-entity-types.md)|100 per program, 3 pattern.any-entiteter per mönster |
| [Fraslista][phrase-list]|500 fraslistor. 10 globala fraslistor på grund av modellen som en funktionsgräns. Icke-utbytbar fraslista innehåller högst 5 000 fraser. Den utbytbara fraslistan innehåller högst 50 000 fraser. Maximalt antal fraser per program med 500 000 fraser.|
| [Fördefinierade entiteter](./howto-add-prebuilt-models.md) | ingen gräns|
| [Lägga till entiteter för reguljära uttryck](./luis-concept-entity-types.md)|20 entiteter<br>Max 500 tecken. entitetsmönster per reguljärt uttryck|
| [Roller](./luis-concept-entity-types.md)|300 roller per program. 10 roller per entitet|
| [Yttrande][utterances] | 500 tecken<br><br>Om du har text som är längre än den här teckengränsen måste du segmentera yttrandena före indata till LUIS och du får enskilda avsiktssvar per segment. Det finns uppenbara avbrott som du kan arbeta med, till exempel skiljetecken och långa pauser i tal.|
| [Exempel på yttranden][utterances] | 15 000 per program – det finns ingen gräns för antalet yttranden per avsikt<br><br>Om du behöver träna programmet med fler exempel kan du använda [en](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) leveransmodell. Du tränar enskilda LUIS-appar (så kallade underordnade appar till den överordnade dispatch-appen) med en eller flera avsikter och tränar sedan en dispatch-app som exempel från varje underordnad LUIS-apps yttranden för att dirigera förutsägelsebegäran till rätt underordnad app. |
| [Versioner](./luis-concept-app-iteration.md)| 100 versioner per program |
| [Versionsnamn][luis-how-to-manage-versions] | 128 tecken |

*Max standardtecken är 50 tecken.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Namnets unika egenskaper

Objektnamn måste vara unika jämfört med andra objekt på samma nivå.

|Objekt|Begränsningar|
|--|--|
|Avsikt, entitet|Alla avsikts- och entitetsnamn måste vara unika i en version av en app.|
|ML-entitetskomponenter|Alla maskininlärningsentitetskomponenter (underordnade entiteter) måste vara unika inom den entiteten för komponenter på samma nivå.|
|Funktioner | Alla namngivna funktioner, till exempel fraslistor, måste vara unika i en version av en app.|
|Entitetsroller|Alla roller på en entitet eller entitetskomponent måste vara unika när de är på samma entitetsnivå (överordnad, underordnad, grandchild osv.).|

## <a name="object-naming"></a>Namngivning av objekt

Använd inte följande tecken i följande namn.

|Objekt|Exkludera tecken|
|--|--|
|Avsikts-, entitets- och rollnamn|`:`<br>`$` <br> `&`|
|Versionsnamn|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Resursanvändning och resursbegränsningar

Language Understand har separata resurser, en typ för redigering och en typ för att fråga förutsägelseslutpunkten. Mer information om skillnaderna mellan nyckeltyper finns i Slutpunktsnycklar [för redigering och frågeförutsägelse i LUIS.](luis-how-to-azure-subscription.md)

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Redigera resursbegränsningar

Använd _typen_, `LUIS.Authoring` , när du filtrerar resurser i Azure Portal. LUIS begränsar 500 program per Azure-redigeringsresurs.

|Redigeringsresurs|Redigera TPS|
|--|--|
|Starter|1 miljon per månad, 5 per sekund|
|F0 – kostnadsfri nivå |1 miljon per månad, 5 per sekund|

* TPS = transaktioner per sekund

[Läs mer om prissättning.][pricing]

### <a name="query-prediction-resource-limits"></a>Resursbegränsningar för frågeförutsägelse

Använd _typen_, `LUIS` , när du filtrerar resurser i Azure Portal. Slutpunktsresursen för LUIS-frågeförutsägelse, som används i körningen, är endast giltig för slutpunktsfrågor.

|Resurs för frågeförutsägelse|Fråga TPS|
|--|--|
|F0 – kostnadsfri nivå |10 tusen per månad, 5 per sekund|
|S0 – Standardnivå|50 per sekund|

### <a name="sentiment-analysis"></a>Attitydanalys

[Integrering av attitydanalys](luis-how-to-publish-app.md#enable-sentiment-analysis), som tillhandahåller sentimentinformation, tillhandahålls utan att det krävs någon annan Azure-resurs.

### <a name="speech-integration"></a>Talintegrering

[Talintegrering ger](../speech-service/how-to-recognize-intents-from-speech-csharp.md) 1 000 slutpunktsbegäranden per enhetskostnad.

[Läs mer om prissättning.][pricing]

## <a name="keyboard-controls"></a>Tangentbordskontroller

|Tangentbordsinmatning | Beskrivning |
|--|--|
|Kontroll + E|växlar mellan token och entiteter i listan med yttranden|

## <a name="website-sign-in-time-period"></a>Tidsperiod för webbplats-inloggning

Din inloggningsåtkomst är i **60 minuter.** Efter den här tidsperioden visas det här felet. Du måste logga in igen.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
