---
title: Så här utför du en batch-test – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) batch test uppsättningar för att hitta yttranden med felaktiga intentor och entiteter.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787020"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch-testning med en uppsättning exempel yttranden

Batch-testning verifierar din aktiva utbildade version för att mäta dess förutsägelse noggrannhet. Ett batch-test hjälper dig att Visa precisionen för varje avsikt och enhet i din aktiva version. Granska resultaten av batch-testet för att vidta lämpliga åtgärder för att förbättra noggrannheten, till exempel lägga till fler exempel yttranden till en avsikt om din app ofta inte kan identifiera rätt avsikts-eller märknings enheter i uttryck.

## <a name="group-data-for-batch-test"></a>Gruppera data för batch-test

Det är viktigt att yttranden som används för batch-testning är nya för LUIS. Om du har en data uppsättning med yttranden kan du dela upp yttranden i tre mängder: exempel på yttranden som har lagts till i avsikt, yttranden som tagits emot från den publicerade slut punkten och yttranden som används för batch-test LUIS efter att det har tränats.

Den batch-JSON-fil som du använder måste innehålla yttranden med enheter på den högsta nivån som är märkta inklusive start-och slut position. Yttranden ska inte ingå i exemplen som redan finns i appen. De bör vara yttrandena att du vill förutsäga för avsikt och entiteter positivt.

Du kan separera tester efter avsikt och/eller entitet eller ha alla test (upp till 1000 yttranden) i samma fil. 

### <a name="common-errors-importing-a-batch"></a>Vanliga fel vid import av en batch

Om du stöter på fel vid överföring av kommando filen till LUIS kontrollerar du följande vanliga problem:

* Mer än 1 000 yttranden i en kommando fil
* Ett uttryck JSON-objekt som inte har någon entitets-egenskap. Egenskapen kan vara en tom matris.
* Ord som är märkta i flera entiteter
* Enhets etiketter startar eller slutar med ett blank steg.

## <a name="fixing-batch-errors"></a>Korrigera batch-fel

Om det finns fel i batch-testning kan du antingen lägga till fler yttranden till ett avsikts sätt och/eller lägga till etiketten mer yttranden med entiteten för att hjälpa LUIS att göra diskrimineringen mellan olika syften. Om du har lagt till yttranden och märkt dem och fortfarande får förutsägelse fel i batch-testning, bör du överväga att lägga till en [fras lista](luis-concept-feature.md) med en domänbaserad vokabulär för att hjälpa Luis att lära dig snabbare.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batch-testning med LUIS-portalen 

### <a name="import-and-train-an-example-app"></a>Importera och träna en exempel-App

Importera en app som tar en pizza ordning, till exempel `1 pepperoni pizza on thin crust` .

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Välj pilen bredvid **ny app** och klicka på **Importera som JSON** för att importera JSON till en ny app. Namnge appen `Pizza app` .


1. Välj **träna** i det övre högra hörnet i navigeringen för att träna appen.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batch-testfil

JSON-exemplet innehåller en uttryck med en etikettad entitet som illustrerar vad en testfil ser ut. I dina egna tester bör du ha många yttranden med rätt avsikts-och maskin inlärnings enhet med etiketten.

1. Skapa `pizza-with-machine-learned-entity-test.json` i en text redigerare eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) den.

2. I den JSON-formaterade kommando filen lägger du till en uttryck med den **avsikt** som du vill förutsäga i testet.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Kör batchen

1. Välj **test** i det övre navigerings fältet.

2. Välj **panelen för batch-testning** på den högra panelen.

    ![Batch-testning, länk](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Välj **Importera**. I dialog rutan som visas väljer du **Välj fil** och letar upp en JSON-fil med rätt JSON-format som *inte innehåller fler än 1 000* yttranden för att testa.

    Import fel rapporteras i ett rött meddelande fält överst i webbläsaren. När en import innehåller fel skapas ingen data uppsättning. Mer information finns i [vanliga fel](#common-errors-importing-a-batch).

4. Välj filens fil placering `pizza-with-machine-learned-entity-test.json` .

5. Ge data uppsättningen ett namn `pizza test` och välj **färdig**.

6. Klicka på knappen **Kör**. När batch-testet har körts väljer du **se resultat**. 

    > [!TIP]
    > * Om du väljer **Hämta** hämtas samma fil som du laddade upp.
    > * Om batch-testet misslyckades visas minst en uttryck-avsikt som inte matchade förutsägelsen.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Granska resultat från batch

Om du vill granska resultaten för batch-testet väljer du **se resultat**. Test resultaten visar grafiskt hur test yttranden förutsägdes mot den aktiva versionen.

I batch-diagrammet visas fyra kvadranter av resultat. Till höger om diagrammet är ett filter. Filtret innehåller avsikter och entiteter. När du väljer en [del av diagrammet](#review-batch-results-for-intents) eller en punkt i diagrammet visas tillhör ande uttryck under diagrammet.

Vid hovring över diagrammet kan ett mushjul förstora eller minska visningen i diagrammet. Detta är användbart när det finns många punkter i diagrammet grupperade nära varandra.

Diagrammet är i fyra kvadranter, med två av avsnitten som visas i rött.

1. Välj **ModifyOrder** avsikt i filter listan. Uttryck förutsägs som en **sann positiv** betydelse för uttryck som har matchat sin positiva förutsägelse i kommando filen.

    > [!div class="mx-imgBorder"]
    > ![Uttryck har matchat sin positiva förutsägelse](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De gröna bockarna i filter listan visar också testets framgång för varje avsikt. Alla andra avsikter visas med en 1/1 positiv Poäng eftersom uttryck har testats för varje avsikt, som ett negativt test för alla syften som inte listas i batch-testet.

1. Välj **bekräftelse** avsikt. Avsikten visas inte i batch-testet, så det är ett negativt test av uttryck som anges i batch-testet.

    > [!div class="mx-imgBorder"]
    > ![Uttryck har förutsägt negativ för en listaad avsikt i kommando filen](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Det negativa testet lyckades, som det skrevs med den gröna texten i filtret och rutnätet.

### <a name="review-batch-test-results-for-entities"></a>Granska resultat för batch-test för entiteter

ModifyOrder-entiteten, som en dator entitet med underentiteter, visar om den översta entiteten matchar och hur underentiteterna förutsägs.

1. Välj entiteten **ModifyOrder** i filter listan och välj sedan cirkeln i rutnätet.

1. Entitetens förutsägelse visas under diagrammet. Skärmen innehåller heldragna linjer för förutsägelser som matchar förväntad och prickade rader för förutsägelser som inte matchar förväntat.

    > [!div class="mx-imgBorder"]
    > ![Den överordnade enheten har förutsägts i kommando filen](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrera diagram resultat

Om du vill filtrera diagrammet efter ett särskilt syfte eller en entitet väljer du avsikten eller entiteten i filter panelen på höger sida. Data punkterna och deras distributions uppdatering i diagrammet efter ditt val.

![Visuellt batch test resultat](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Resultat exempel för diagram

Diagrammet i LUIS-portalen kan du utföra följande åtgärder:
 
#### <a name="view-single-point-utterance-data"></a>Visa uttryck-data med en punkt

Hovra över en data punkt i diagrammet för att se den aktuella uppskattningens resultat. Välj en data punkt för att hämta motsvarande uttryck i listan yttranden längst ned på sidan.

![Valda uttryck](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Visa avsnitts data

I det fyra avsnitts diagrammet väljer du avsnittets namn, till exempel **falskt positivt** längst upp till höger i diagrammet. Under diagrammet visas alla yttranden i avsnittet nedanför diagrammet i en lista.

![Valda yttranden efter avsnitt](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I den här bilden `switch on` är uttryck märkt med TurnAllOn-avsikten men fick förutsägelsen ingen avsikt. Detta är en indikation på att TurnAllOn-avsikten kräver fler exempel yttranden för att göra den förväntade förutsägelsen.

De två avsnitten i diagrammet i rött indikerar yttranden som inte matchar förväntade förutsägelser. Dessa indikerar yttranden som LUIS behöver mer utbildning.

De två avsnitten i diagrammet i grönt matchade förväntade förutsägelse.

## <a name="batch-testing-using-the-rest-api"></a>Batch-testning med hjälp av REST API 

Med LUIS kan du köra batch-test med LUIS-portalen och REST API. Slut punkterna för REST API visas nedan. Information om batch-testning med LUIS-portalen finns i [Självstudier: batch-testdata uppsättningar](). Använd de fullständiga URL: erna nedan och ersätt plats hållarnas värden med din egen LUIS-förutsägelse nyckel och slut punkt. 

Kom ihåg att lägga till din LUIS `Ocp-Apim-Subscription-Key` -nyckel i rubriken och ange `Content-Type` till `application/json` .

### <a name="start-a-batch-test"></a>Starta ett batch-test

Starta ett batch-test med antingen ett versions-ID för appen eller ett publicerings fack. Skicka en **post** -begäran till något av följande slut punkts format. Inkludera din batch-fil i bröd texten i begäran.

Publicerings plats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID för app-version
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Dessa slut punkter returnerar ett åtgärds-ID som du kommer att använda för att kontrol lera status och få resultat. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Hämta status för ett pågående batch-test

Använd åtgärds-ID: t från batch-testet som du startade för att hämta statusen från följande slut punkts format: 

Publicerings plats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID för app-version
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Hämta resultatet från ett batch-test

Använd åtgärds-ID: t från batch-testet som du började för att hämta resultatet från följande slut punkt format: 

Publicerings plats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID för app-version
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Kommando fil för yttranden

Skicka en batch-fil med yttranden, som kallas för en *data uppsättning*, för batch-testning. Data uppsättningen är en JSON-formaterad fil som innehåller högst 1 000 med etiketten yttranden. Du kan testa upp till 10 data uppsättningar i en app. Om du behöver testa mer tar du bort en data uppsättning och lägger till en ny. Alla anpassade entiteter i modellen visas i filtret batch-test-entiteter även om det inte finns några motsvarande entiteter i batch-fildata.

Kommando filen består av yttranden. Varje uttryck måste ha en förväntad förutsägelse för matchning tillsammans med alla [Machine Learning-enheter](luis-concept-entity-types.md#types-of-entities) som du förväntar dig att identifiera.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Mall för satsvis syntax för avsikter med entiteter

Använd följande mall för att starta kommando filen:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Kommando filen använder egenskaperna **startPos** och **endPos** för att notera början och slutet på en entitet. Värdena är noll-baserade och får inte börja eller sluta med ett blank steg. Det skiljer sig från frågans loggar, som använder start egenskaper och endIndex-egenskaper.

Om du inte vill testa entiteter, inkluderar du `entities` egenskapen och anger värdet som en tom matris `[]` .

### <a name="rest-api-batch-test-results"></a>REST API batch test resultat

Det finns flera objekt som returneras av API: et:

* Information om avsikter och entiteter modeller, till exempel precision, åter kallelse och F-poäng.
* Information om entiteternas modeller, till exempel precision, åter kallelse och F-Poäng för varje entitet 
  * Med hjälp av `verbose` flaggan kan du få mer information om entiteten, till exempel `entityTextFScore` och `entityTypeFScore` .
* Tillhandahöll yttranden med förväntade och namngivna namn
* En lista med falska positiva entiteter och en lista över falska negativa entiteter.

## <a name="next-steps"></a>Nästa steg

Om testningen visar att LUIS-appen inte känner igen rätt avsikter och entiteter kan du arbeta för att förbättra LUIS-appens prestanda genom att märka fler yttranden eller lägga till funktioner.

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md)
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md)
