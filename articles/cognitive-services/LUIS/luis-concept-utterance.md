---
title: Användbart exempel yttranden – LUIS
description: Yttranden är indata från användaren som appen behöver tolka. Samla in fraser som du tror att användarna kommer att ange. Inkludera yttranden som betyder samma sak, men som har utformats annorlunda i ord-längd och ord placering.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 46004d81756809958e359c2a0b72c143599c2853
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706766"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Förstå vilka bra yttranden är för din LUIS-app

**Yttranden** är indata från användaren som appen behöver tolka. För att träna LUIS att extrahera avsikter och entiteter från dem är det viktigt att samla in en mängd olika exempel yttranden för varje avsikt. Aktiv inlärning, eller processen att fortsätta träna på nya yttranden, är nödvändig för maskin inlärnings information som LUIS tillhandahåller.

Samla in yttranden som du tror att användarna kommer att ange. Ta med yttranden, vilket innebär att samma sak är konstruerad på olika sätt:

* Uttryck length – Short, medium och Long för ditt klient program
* Ord och fras längd
* Ord placering – entitet i början, mitten och slutet av uttryck
* Grammatik
* Pluralization
* Följd
* Val av Substantiv och verb
* [Interpunktion](luis-reference-application-settings.md#punctuation-normalization) – en lämplig mängd som använder korrekt, felaktig och ingen grammatik

## <a name="how-to-choose-varied-utterances"></a>Så här väljer du varierande yttranden

När du först börjar med att [lägga till exempel yttranden](./luis-how-to-add-entities.md) i din Luis-modell finns det några principer som du bör tänka på.

### <a name="utterances-arent-always-well-formed"></a>Yttranden är inte alltid korrekt utformade

Det kan vara en mening, t. ex. "boka en biljett till Paris för mig" eller ett fragment i en mening, t. ex. "bokning" eller "Paris flygning".  Användare gör ofta stavfel. När du planerar din app bör du överväga om du använder [stavningskontroll i Bing](luis-tutorial-bing-spellcheck.md) för att korrigera användarindata innan du skickar den till Luis.

Om du inte stavningskontrollerar användar yttranden bör du träna LUIS på yttranden som innehåller skrivfel och fel stavningar.

### <a name="use-the-representative-language-of-the-user"></a>Använd användarens representativa språk

När du väljer yttranden bör du tänka på att vad du tycker är en vanlig term eller fras kanske inte är korrekt för den typiska användaren av klient programmet. De har kanske inte domän erfarenhet. Var försiktig när du använder termer eller fraser som en användare bara skulle säga om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj varierande terminologi och formuleringen

Du kommer att se att även om du gör ansträngningar för att skapa olika menings mönster, kommer du fortfarande att upprepa vissa ord listor.

Gör följande exempel yttranden:

|Exempel på yttranden|
|--|
|Hur får jag en dator?|
|Var får jag en dator?|
|Jag vill skaffa en dator, hur gör jag?|
|När kan jag ha en dator?|

Kärn termen här, *dator*, är inte varierande. Använd alternativ som stationär dator, bärbar dator, arbets Station eller till och med bara dator. LUIS kan på ett intelligent sätt härleda synonymer från kontexten, men när du skapar yttranden för utbildning är det alltid bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel på yttranden i varje avsikt

Varje avsikt måste ha exempel yttranden, minst 15. Om du har en avsikt som inte har något exempel yttranden, kan du inte träna LUIS. Om du har en avsikt med ett eller väldigt få exempel yttranden kan LUIS inte förutse avsikten korrekt.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Lägg till små grupper på 15 yttranden för varje redigering av iterationer

I varje iteration av modellen ska du inte lägga till en stor mängd yttranden. Lägg till yttranden i kvantiteter på 15. [Träna](luis-how-to-train.md), [publicera](luis-how-to-publish-app.md)och [testa](luis-interactive-test.md) igen.

LUIS skapar effektiva modeller med yttranden som väljs noggrant av LUIS Model-författaren. Att lägga till för många yttranden är inte värdefullt eftersom det introducerar förvirring.

Det är bättre att börja med några få yttranden och sedan [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för korrekt matchning av avsikts förutsägelse och enhets extrahering.

## <a name="utterance-normalization"></a>Uttryck-normalisering

Uttryck normalisering är processen att ignorera effekterna av text typer, till exempel interpunktion och dia kritiska tecken, under utbildning och förutsägelse.

Inställningarna för uttryck-normalisering är inaktiverade som standard. Några exempel på inställningar är:

* Ord former
* Dia kritiska tecken
* Skiljetecken

Om du aktiverar en normaliserings inställning ändras poängen i **test** fönstret, batch-test och slut punkts frågor för alla yttranden för den normaliserings inställningen.

När du klonar en version i LUIS-portalen fortsätter versions inställningarna till den nya klonade versionen.

Ange versions inställningar via LUIS-portalen, gå till avsnittet **Hantera** på sidan **program inställningar** eller API för [uppdaterings versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Läs mer om de här normaliserings ändringarna i [referensen](luis-reference-application-settings.md).

### <a name="word-forms"></a>Ord former

Normaliserade **ord former** ignorerar skillnaderna i ord som expanderar bortom roten.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Dia kritiska tecken

Dia kritiska tecken markeras eller signeras i texten, t. ex.:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Skiljetecken
Normalisera **interpunktion** innebär att innan dina modeller blir utbildade och innan dina slut punkts frågor hämtas, tas skiljetecken bort från yttranden.

Interpunktion är en separat token i LUIS. En uttryck som innehåller en period i slutet jämfört med en uttryck som inte innehåller en period i slutet är två separata yttranden och kan få två olika förutsägelser.

Om interpunktion inte är normaliserad ignorerar LUIS inte skiljetecken som standard eftersom vissa klient program kan placera signifikans på dessa märken. Se till att ditt exempel yttranden använder både skiljetecken och ingen interpunktion för att båda formaten ska returnera samma relativa resultat.

Se till att modellen hanterar skiljetecken antingen i exemplet yttranden (med och utan interpunktion) eller i [mönstren](luis-concept-patterns.md) där det är enklare att ignorera interpunktion med den särskilda syntaxen: `I am applying for the {Job} position[.]`

Om interpunktionen inte har någon specifik betydelse i klient programmet, bör du överväga att [Ignorera interpunktion](#utterance-normalization) genom normalisera interpunktion.

### <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och interpunktion

Om du vill ignorera vissa ord eller interpunktion i mönster, använder du ett [mönster](luis-concept-patterns.md#pattern-syntax) med kommandot _Ignore_ för hakparenteser `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Utbildning med alla yttranden

Träning är vanligt vis icke-deterministiskt: uttryck förutsägelse kan variera något mellan versioner eller appar.
Du kan ta bort icke-deterministisk utbildning genom att uppdatera API för [versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` namn/värde-paret för att använda alla tränings data.

## <a name="testing-utterances"></a>Testa yttranden

Utvecklare bör börja testa sitt LUIS-program med verklig trafik genom att skicka yttranden till [förutsägelse slut punktens](luis-how-to-azure-subscription.md) URL. Dessa yttranden används för att förbättra prestanda för avsikter och entiteter med [gransknings yttranden](luis-how-to-review-endpoint-utterances.md). Tester som skickats med LUIS webbplats test panel skickas inte via slut punkten och bidrar därför inte till aktiv inlärning.

## <a name="review-utterances"></a>Granska yttranden

När din modell har tränat, publicerat och tagit emot [slut punkts](luis-glossary.md#endpoint) frågor, [granskar du yttranden](luis-how-to-review-endpoint-utterances.md) som föreslås av Luis. LUIS väljer slut punkts yttranden som har låga Poäng för antingen avsikten eller entiteten.

## <a name="best-practices"></a>Bästa praxis

Granska [metod tips](luis-concept-best-practices.md) och Använd dem som en del av din vanliga redigerings cykel.

## <a name="label-for-word-meaning"></a>Etikett för ord betydelse

Om Word-valet eller ord ordningen är detsamma, men inte samma sak, ska du inte märka det med entiteten.

I följande yttranden `fair` är ordet ett homograph. Den har stavats likadan men har en annan betydelse:

|Yttrande|
|--|
|Vilken typ av regions mässor sker i Seattle-arean på sommaren?|
|Är det aktuella omdömet för översynen i Stockholm?|

Om du vill att en händelse entitet ska hitta alla händelse data kan du märka ordet `fair` i den första uttryck, men inte i den andra.


## <a name="next-steps"></a>Nästa steg
Se [Lägg till exempel yttranden](./luis-how-to-add-entities.md) för information om hur du tränar en Luis-app för att förstå användar yttranden.