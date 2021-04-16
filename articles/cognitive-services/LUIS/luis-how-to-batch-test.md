---
title: Så här utför du ett batchtest – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) batchtestningsuppsättningar för att hitta yttranden med felaktiga avsikter och entiteter.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501798"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batchtestning med en uppsättning exempelyttranden

Batchtestning verifierar din aktiva tränade version för att mäta dess förutsägelsenoggrannhet. Ett batchtest hjälper dig att visa noggrannheten för varje avsikt och entitet i din aktiva version. Granska batchtestresultatet för att vidta lämpliga åtgärder för att förbättra noggrannheten, till exempel att lägga till fler exempelyttranden till en avsikt om din app ofta inte kan identifiera rätt avsikt eller märka entiteter i yttrandena.

## <a name="group-data-for-batch-test"></a>Gruppera data för batchtest

Det är viktigt att yttranden som används för batchtestning är nya för LUIS. Om du har en datauppsättning med yttranden delar du upp yttrandena i tre uppsättningar: exempelyttranden som läggs till i en avsikt, yttranden som tas emot från den publicerade slutpunkten och yttranden som används för att batchtesta LUIS när den har tränats.

Batch-JSON-filen som du använder bör innehålla yttranden med maskininlärningsentiteter på den översta nivån märkta, inklusive start- och slutposition. Yttrandena bör inte ingå i exemplen som redan finns i appen. De bör vara yttranden som du vill förutsäga positivt för avsikter och entiteter.

Du kan separera tester efter avsikt och/eller entitet eller ha alla tester (upp till 1 000 yttranden) i samma fil. 

### <a name="common-errors-importing-a-batch"></a>Vanliga fel vid import av en batch

Om du får fel när du laddar upp batchfilen till LUIS kontrollerar du om det finns följande vanliga problem:

* Mer än 1 000 yttranden i en batchfil
* Ett JSON-objekt för yttranden som inte har en entitetsegenskap. Egenskapen kan vara en tom matris.
* Ord som är märkta i flera entiteter
* Entitetsetiketter som börjar eller slutar på ett blanksteg.

## <a name="fixing-batch-errors"></a>Åtgärda batchfel

Om det finns fel i batchtestningen kan du antingen lägga till fler yttranden till en avsikt och/eller märka fler yttranden med entiteten för att hjälpa LUIS att göra förhållandet mellan avsikter. Om du har lagt till yttranden och etiketterat dem och fortfarande får förutsägelsefel vid batchtestning bör du överväga att lägga till en fraslista med domänspecifik vokabulär för att hjälpa LUIS att lära sig snabbare. [](luis-concept-feature.md)


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batchtestning med LUIS-portalen 

### <a name="import-and-train-an-example-app"></a>Importera och träna en exempelapp

Importera en app som tar en pizzabeställning, till exempel `1 pepperoni pizza on thin crust` .

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Logga in på [LUIS-portalen](https://www.luis.ai)och välj din prenumeration **och** **redigeringsresurs** för att se de appar som tilldelats till den redigeringsresursen.
1. Välj pilen bredvid Ny app **och klicka** på Importera **som JSON för** att importera JSON till en ny app. Ge appen namnet `Pizza app` .


1. Välj **Träna** i det övre högra hörnet i navigeringen för att träna appen.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batch-testfil

JSON-exemplet innehåller ett uttryck med en märkt entitet som illustrerar hur en testfil ser ut. I dina egna tester bör du ha många yttranden med rätt avsikt och entitet för maskininlärning märkta.

1. Skapa `pizza-with-machine-learned-entity-test.json` i en textredigerare eller ladda [ned](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) den.

2. I den JSON-formaterade batchfilen lägger du till ett uttryck med **den** avsikt som du vill förutsäga i testet.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Köra batchen

1. Välj **Test** i det övre navigeringsfältet.

2. Välj **Batch-testpanelen** på den högra panelen.

    ![Länk för batchtestning](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Välj **Importera**. I dialogrutan som visas  väljer du Välj fil och letar upp en JSON-fil med rätt JSON-format som inte innehåller fler än *1 000* yttranden att testa.

    Importfel rapporteras i ett rött meddelandefält överst i webbläsaren. När en import innehåller fel skapas ingen datauppsättning. Mer information finns i [Vanliga fel.](#common-errors-importing-a-batch)

4. Välj filplatsen för `pizza-with-machine-learned-entity-test.json` filen.

5. Ge datauppsättningen `pizza test` ett namn och välj **Klar.**

6. Klicka på knappen **Kör**. När batchtestet har körs väljer du **Se resultat**. 

    > [!TIP]
    > * Om **du väljer** Ladda ned hämtas samma fil som du laddade upp.
    > * Om batchtestet misslyckades matchar inte minst en avsikt för yttranden förutsägelsen.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Granska batchresultat för avsikter

Om du vill granska batchtestresultaten väljer du **Visa resultat.** Testresultaten visar grafiskt hur testyttrandena förutsades mot den aktiva versionen.

Batchdiagrammet visar fyra kvadranter med resultat. Till höger om diagrammet finns ett filter. Filtret innehåller avsikter och entiteter. När du väljer [ett avsnitt i diagrammet](#review-batch-results-for-intents) eller en punkt i diagrammet visas de associerade yttrandena under diagrammet.

När du hovrar över diagrammet kan ett mushjul förstora eller minska visningen i diagrammet. Detta är användbart när det finns många punkter i diagrammet grupperade nära varandra.

Diagrammet är i fyra kvadranter, där två av avsnitten visas i rött.

1. Välj avsikten **ModifyOrder** i filterlistan. Tayttrande förutsägs som en **sann** positiv, vilket innebär att tamanten har matchat sin positiva förutsägelse som anges i batchfilen.

    > [!div class="mx-imgBorder"]
    > ![Ta ett uttryck matchade dess positiva förutsägelse](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    De gröna bockmarkeringarna i filterlistan visar också om testet lyckades för varje avsikt. Alla andra avsikter visas med en positiv poäng på 1/1 eftersom yttrandena testades mot varje avsikt, som ett negativt test för avsikter som inte anges i batchtestet.

1. Välj avsikten **Bekräftelse.** Den här avsikten visas inte i batchtestet, så det här är ett negativt test av de yttranden som anges i batchtestet.

    > [!div class="mx-imgBorder"]
    > ![Uttryck förutsade negativt för olistad avsikt i batchfil](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Det negativa testet lyckades, vilket anges med den gröna texten i filtret och rutnätet.

### <a name="review-batch-test-results-for-entities"></a>Granska batchtestresultat för entiteter

Entiteten ModifyOrder, som en datorentitet med underentiteter, visar om entiteten på den översta nivån matchar och hur underentiteterna förutsägs.

1. Välj **entiteten ModifyOrder** i filterlistan och välj sedan cirkeln i rutnätet.

1. Entitetsförutsägelsen visas under diagrammet. Visningen innehåller heldragna linjer för förutsägelser som matchar förväntan och prickade linjer för förutsägelser som inte matchar förväntan.

    > [!div class="mx-imgBorder"]
    > ![Entitetens överordnade har förutsagts i batchfilen](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrera diagramresultat

Om du vill filtrera diagrammet efter en specifik avsikt eller entitet väljer du avsikten eller entiteten i filtreringspanelen till höger. Datapunkterna och deras distributionsuppdatering i diagrammet enligt ditt val.

![Visualiserat batchtestresultat](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Diagramresultatexempel

Diagrammet i LUIS-portalen kan du utföra följande åtgärder:
 
#### <a name="view-single-point-utterance-data"></a>Visa yttranden med en enda punkt

Hovra över en datapunkt i diagrammet för att se förutsägelseresultatet. Välj en datapunkt för att hämta dess motsvarande yttranden i listan med yttranden längst ned på sidan.

![Valt uttryck](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Visa avsnittsdata

I diagrammet med fyra avsnitt väljer du avsnittsnamnet, till exempel **False Positive** (Falsk positiv) längst upp till höger i diagrammet. Under diagrammet visas alla yttranden i det avsnittet under diagrammet i en lista.

![Valda yttranden efter avsnitt](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

I den här föregående bilden märks yttranden med avsikten TurnAllOn, men avsikten None (Ingen) `switch on` har tagits emot. Detta är en indikation på att avsikten TurnAllOn behöver fler exempelyttranden för att kunna göra den förväntade förutsägelsen.

De två avsnitten i diagrammet i rött visar yttranden som inte matchade den förväntade förutsägelsen. Dessa anger yttranden som LUIS behöver mer utbildning.

De två avsnitten i diagrammet i grönt matchade den förväntade förutsägelsen.

## <a name="batch-testing-using-the-rest-api"></a>Batchtestning med hjälp av REST API 

MED LUIS kan du batchtesta med HJÄLP av LUIS-portalen och REST API. Slutpunkterna för REST API visas nedan. Information om batchtestning med LUIS-portalen finns i [Självstudie: batchtestdatauppsättningar.]() Använd de fullständiga URL:erna nedan och ersätt platshållarvärdena med din egen LUIS-förutsägelsenyckel och slutpunkt. 

Kom ihåg att lägga till DIN `Ocp-Apim-Subscription-Key` LUIS-nyckel i -huvudet och ange `Content-Type` till `application/json` .

### <a name="start-a-batch-test"></a>Starta ett batchtest

Starta ett batchtest med antingen ett appversions-ID eller ett publiceringsfack. Skicka en **POST-begäran** till något av följande slutpunktsformat. Inkludera batchfilen i brödtexten i begäran.

Publiceringsplats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Appversions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Dessa slutpunkter returnerar ett åtgärds-ID som du använder för att kontrollera status och hämta resultat. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Hämta status för ett pågående batchtest

Använd åtgärds-ID:t från batchtestet som du började för att hämta dess status från följande slutpunktsformat: 

Publiceringsplats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Appversions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Hämta resultaten från ett batchtest

Använd åtgärds-ID:t från batchtestet som du började för att hämta resultatet från följande slutpunktsformat: 

Publiceringsplats
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Appversions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Batch-fil med yttranden

Skicka en batchfil med yttranden, som kallas en *datauppsättning, för* batchtestning. Datauppsättningen är en JSON-formaterad fil som innehåller högst 1 000 märkta yttranden. Du kan testa upp till 10 datauppsättningar i en app. Om du behöver testa mer tar du bort en datauppsättning och lägger sedan till en ny. Alla anpassade entiteter i modellen visas i batchtestentitetsfiltret även om det inte finns några motsvarande entiteter i batchfildata.

Batchfilen består av yttranden. Varje yttranden måste ha en förväntad avsiktsförutsägelse tillsammans med eventuella [maskininlärningsentiteter](luis-concept-entity-types.md#machine-learned-ml-entity) som du förväntar dig att identifieras.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Batch-syntaxmall för avsikter med entiteter

Använd följande mall för att starta batchfilen:

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

Batchfilen använder egenskaperna **startPos och** **endPos för att** notera början och slutet av en entitet. Värdena är nollbaserade och bör inte börja eller sluta på ett blanksteg. Detta skiljer sig från frågeloggarna som använder egenskaperna startIndex och endIndex.

Om du inte vill testa entiteter inkluderar du `entities` egenskapen och anger värdet som en tom matris, `[]` .

### <a name="rest-api-batch-test-results"></a>REST API batchtestresultat

Det finns flera objekt som returneras av API:et:

* Information om avsikterna och entitetsmodellerna, till exempel precision, träffsäkerhet och F-poäng.
* Information om entitetsmodellerna, till exempel precision, träffsäkerhet och F-poäng) för varje entitet 
  * Med hjälp `verbose` av flaggan kan du få mer information om entiteten, till exempel och `entityTextFScore` `entityTypeFScore` .
* Tillhandahållna yttranden med de förutsagda och märkta avsiktsnamnen
* En lista över falska positiva entiteter och en lista över falska negativa entiteter.

## <a name="next-steps"></a>Nästa steg

Om testningen indikerar att LUIS-appen inte känner igen rätt avsikter och entiteter kan du arbeta för att förbättra LUIS-appens prestanda genom att märka fler yttranden eller lägga till funktioner.

* [Märka föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md)
* [Använda funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md)
