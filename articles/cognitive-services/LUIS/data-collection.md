---
title: Datainsamling
description: Lär dig mer om vilka exempel data som ska samlas in när du utvecklar din app
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98738092"
---
# <a name="data-collection-for-your-app"></a>Data insamling för din app

En Language Understanding-app (LUIS) behöver data som en del av utveckling av appar.

## <a name="data-used-in-luis"></a>Data som används i LUIS

LUIS använder text som data för att träna och testa din LUIS-app för klassificering av [avsikter](luis-concept-intent.md) och för att extrahera [entiteter](luis-concept-entity-types.md). Du behöver en tillräckligt stor data uppsättning som du har tillräckligt med data för att skapa separata data uppsättningar för både utbildning och testning som har den mångfald och distribution som har benämns nedan.  Data i var och en av dessa uppsättningar ska inte överlappa varandra.

## <a name="training-data-selection-for-example-utterances"></a>Val av tränings data till exempel yttranden

Välj yttranden för din utbildnings uppsättning baserat på följande kriterier:

* **Verkliga data är bäst**:
    * **Verkliga data från klient program**: Välj yttranden som är verkliga data från klient programmet.  Om kunden skickar ett webb formulär med sin förfrågan idag och du bygger en bot, kan du börja med att använda webb formulär data.
    * **Data med full källkod**: om du inte har några befintliga data bör du tänka på yttranden.  Försök att läsa in yttranden från din faktiska användar population för ditt scenario för att få bästa möjliga uppskattning av de verkliga data som programmet kommer att se. Yttranden med hög källkod är bättre än dator-genererade yttranden.  När du skapar en data uppsättning med syntetiska yttranden som genereras för specifika mönster, kommer den att sakna stor del av den naturliga variation som du ser med personer som skapar yttranden och inte kommer att bli allmänt lämpad för produktion.
* **Data variation**:
    * **Regions mångfald**: se till att data för varje avsikt är så stora som möjligt, inklusive _formuleringen_ (Word-val) och _grammatik_.  Se till att du har yttranden som representerar de termer som används för alla regioner som du betjänar om du lär dig att använda HR-principer på semester dagar.  I t. ex. kan man be om `taking a holiday` och i oss kan man be om det `taking vacation days` .
    * **Språkvariation**: om du har användare med olika inbyggda språk som kommunicerar på ett andra språk, måste du se till att ha yttranden som representerar icke-inbyggda högtalare.
    * **Inmatnings mångfald**: Överväg din data inmatnings Sök väg. Om du samlar in data från en person, avdelning eller inmatnings enhet (mikrofon) saknar du förmodligen olikheter som är viktiga för din app för att lära dig om alla inmatnings vägar.
    * **Variation i interpunktion**: Tänk på att människor använder varierande nivåer av interpunktion i text program och se till att du har en mångfald av hur interpunktion används. Om du använder data som kommer från tal har det inte några skiljetecken, så dina data bör inte heller vara det.
* **Data distribution**: kontrol lera att data uppslaget i avsikten representerar samma data spridning som klient programmet tar emot. Om din LUIS-app kommer att klassificera yttranden som är begär Anden att schemalägga en ledighet (50%), men den ser även yttranden om att lämna kvar dagar kvar (20%), godkännande av löv (20%) utan omfattning och CHI2TEST (10%) data uppsättningen bör ha samplings procenten av varje typ av uttryck.
* **Använd alla data formulär**: om din Luis-app tar data i flera formulär ska du se till att ta med dessa formulär i din utbildnings yttranden. Om ditt klient program t. ex. använder både tal-och text inskriven, måste du ha inloggade tal-till-text-yttranden samt skriva yttranden.  Du kommer att se olika variationer i hur människor talar från hur de skriver och olika fel i tal igenkänning och skrivfel.  All denna variation bör representeras i dina utbildnings data.
* **Positiva och negativa exempel**: för att lära en Luis-app, måste den lära sig om vad avsikten är (positiv) och vad det inte är (negativt). I LUIS kan yttranden endast vara positivt för ett enda avsikt. När en uttryck läggs till i ett avsikts sätt gör LUIS automatiskt att samma exempel uttryck ett negativt exempel för alla andra syften.
* **Data utanför program omfånget**: om programmet kommer att se yttranden som faller utanför dina definierade syften ska du se till att tillhandahålla dem. Exemplen som inte har tilldelats till ett visst definierat avsikts sätt märks med **ingen** avsikt.  Det är viktigt att du har realistiska exempel för att **ingen** ska förutsäga yttranden som ligger utanför omfånget för de definierade syftena.

    Om du till exempel skapar en HR-robot som fokuserar på att lämna tid och du har tre avsikter:
    * Schemalägg eller redigera en tjänstledighet
    * fråga om tillgängliga ledighets dagar
    * Godkänn/disapprove lämna

    Du vill se till att du har yttranden som omfattar båda dessa avsikter, men även för att omfatta potentiella yttranden utanför den omfattningen som programmet ska fungera som dessa:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Sällsynta exempel**: appen måste ha ovanliga exempel och vanliga exempel.  Om din app aldrig har sett sällsynta exempel, kan den inte identifiera dem i produktionen. Om du använder verkliga data kommer du att kunna förutse hur din LUIS-app fungerar i produktion.

### <a name="quality-instead-of-quantity"></a>Kvalitet i stället för kvantitet

Överväg kvaliteten på dina befintliga data innan du lägger till mer data.  Med LUIS använder du maskin undervisningar.  Kombinationen av etiketterna och de maskin inlärnings funktioner som du definierar är vad din LUIS-app använder.  Den förlitar sig inte bara på antalet etiketter för att få bästa förutsägelse.  En mångfald av exempel och deras åter givning av vad din LUIS-app kommer att se i produktion är den viktigaste delen.

### <a name="preprocessing-data"></a>Förbearbeta data

Följande förbehandlings steg hjälper dig att bygga en bättre LUIS-app:

* **Ta bort dubbletter**: dubbletter av yttranden kommer inte försämra, men de hjälper inte att ta bort märknings tiden.
* **Tillämpa samma klient-app-förbehandling**: om klient programmet, som anropar Luis förutsägelse slut punkt, använder data bearbetning vid körning innan du skickar texten till Luis bör du träna Luis-appen på data som bearbetas på samma sätt. Om ditt klient program exempelvis använder [stavningskontroll i Bing](../bing-spell-check/overview.md) för att korrigera stavning på indata till Luis, korrigera din utbildning och testa yttranden innan du lägger till i Luis.
* **Använd inte nya rensnings processer som klient appen inte använder**: om din klient app accepterar tal som skapats direkt utan någon rensning, till exempel grammatik eller interpunktion, måste din yttranden Visa samma information som saknade skiljetecken och andra eventuella inläsningar som du behöver ta hänsyn till.
* **Rensa inte data**: ta inte bort några felaktiga indata som du kan få från förvrängd tal igenkänning, oavsiktliga tangenttryckningar eller felstavad text. Om din app kommer att se indata som dessa, är det viktigt att den tränas och testas på dem. Lägg till en _felaktig Indatatyp_ om du inte förväntar dig att din app ska förstå den. Märk dessa data för att hjälpa LUIS-appen att förutsäga rätt svar vid körning. Klient programmet kan välja ett lämpligt svar för att oläsliga yttranden, till exempel `Please try again` .

### <a name="labeling-data"></a>Etikettera data

* **Etikettext som om den var korrekt**: exemplet yttranden bör ha alla former av en entitet som har etiketten. Detta inkluderar text som är felstavad, felstavad och fel översättd.

### <a name="data-review-after-luis-app-is-in-production"></a>Data granskning när LUIS-appen är i produktion

[Granska slut punkts yttranden](luis-concept-review-endpoint-utterances.md) för att övervaka verklig uttryck-trafik när du har distribuerat en app till produktion.  På så sätt kan du uppdatera din utbildnings-yttranden med verkliga data, vilket förbättrar din app. Alla appar som har skapats med data som är med full källkod eller som inte är riktiga måste förbättras, baserat på den faktiska användningen.

## <a name="test-data-selection-for-batch-testing"></a>Testa data urval för batch-testning

Alla principer som anges ovan för utbildning yttranden gäller för yttranden som du bör använda för [test uppsättningen](./luis-how-to-batch-test.md). Se till att fördelningen mellan intentor och entiteter speglar den verkliga distributionen så nära som möjligt.

Återanvänd inte yttranden från din utbildnings uppsättning i din test uppsättning. Detta gör att resultatet är felaktigt och ger dig inte rätt tecken på hur LUIS-appen kommer att utföras i produktionen.

När den första versionen av appen har publicerats bör du uppdatera din test uppsättning med yttranden från verklig trafik för att se till att din test uppsättning visar din produktions distribution och du kan övervaka realistiska prestanda över tid.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur LUIS ändrar dina data före förutsägelse](luis-concept-data-alteration.md)