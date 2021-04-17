---
title: Metodtips för att skapa luis-appen
description: Lär dig metodtipsen för att få bästa möjliga resultat från LUIS-appens modell.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: d5fa2a1e865a4f54de268e7ad756d1d4363f3b78
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500217"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Metodtips för att skapa en LUIS-app (Language Understanding)
Använd app-redigeringsprocessen för att skapa din LUIS-app:

* Skapa språkmodeller (avsikter och entiteter)
* Lägg till några yttranden med träningsexempel (15–30 per avsikt)
* Publicera till slutpunkt
* Testa från slutpunkt

När appen har [publicerats använder](luis-how-to-publish-app.md)du utvecklingslivscykeln för att lägga till funktioner, publicera och testa från slutpunkten. Börja inte nästa redigeringscykel genom att lägga till fler exempelyttranden eftersom luis inte kan lära sig din modell med verkliga användaryttranden.

Expandera inte yttrandena förrän den aktuella uppsättningen med både exempel- och slutpunktsyttranden returnerar säkra, höga förutsägelsepoäng. Förbättra poängen med aktiv [inlärning](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Gör och gör inte
Följande lista innehåller metodtips för LUIS-appar:

|Gör följande|Gör inte följande|
|--|--|
|[Planera ditt schema](#do-plan-your-schema)|[Skapa och publicera utan en plan](#dont-publish-too-quickly)|
|[Definiera distinkta avsikter](#do-define-distinct-intents)<br>[Lägga till funktioner i avsikter](#do-add-features-to-intents)<br>
[Använda maskininlärda entiteter](#do-use-machine-learned-entities) |[Lägga till många exempelyttranden i avsikter](#dont-add-many-example-utterances-to-intents)<br>[Använda några eller enkla entiteter](#dont-use-few-or-simple-entities) |
|[Hitta en god punkt mellan för generisk och för specifik för varje avsikt](#do-find-sweet-spot-for-intents)|[Använda LUIS som utbildningsplattform](#dont-use-luis-as-a-training-platform)|
|[Skapa din app iterativt med versioner](#do-build-your-app-iteratively-with-versions)<br>[Skapa entiteter för modelldepositionering](#do-build-for-model-decomposition)|[Lägg till många exempelyttranden med samma format och ignorera andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägga till mönster i senare iterationer](#do-add-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Balansera dina yttranden över alla avsikter utom](#balance-your-utterances-across-all-intents) avsikten None (Ingen).<br>[Lägga till exempelyttranden till avsikten None (Ingen)](#do-add-example-utterances-to-none-intent)|[Skapa fraslistor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Använd funktionen föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägga till för många mönster](#dont-add-many-patterns)|
|[Övervaka appens prestanda med batchtestning](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskilt exempelyttrande som lagts till](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Planera schemat

Innan du börjar skapa appens schema bör du identifiera vad och var du planerar att använda den här appen. Ju mer omfattande och specifik din planering är, desto bättre blir din app.

* Undersöka målanvändare
* Definiera personas från start till slut som representerar din app – röst, avatar, problemhantering (proaktiv, reaktiv)
* Identifiera användarinteraktioner (text, tal) genom vilka kanaler, lämna över till befintliga lösningar eller skapa en ny lösning för den här appen
* Användarresa från slutanvändare till slutanvändare
    * Vad bör du förvänta dig att den här appen ska göra och inte göra? * Vilka är prioriteringarna för vad den ska göra?
    * Vilka är de viktigaste användningsfallen?
* Samla in data – [lär dig](data-collection.md) mer om att samla in och förbereda data

## <a name="do-define-distinct-intents"></a>Definiera distinkta avsikter
Kontrollera att vokabulären för varje avsikt bara gäller för den avsikten och att den inte överlappar med en annan avsikt. Om du till exempel vill ha en app som hanterar reseskisser, till exempel flygresor och hotell, kan du välja att ha dessa ämnesområden som separata avsikter eller samma avsikt med entiteter för specifika data i yttrandena.

Om vokabulären mellan två avsikter är densamma kombinerar du avsikten och använder entiteter.

Överväg följande exempelyttranden:

|Exempel på yttranden|
|--|
|Boka en flygresa|
|Boka ett hotell|

`Book a flight` och `Book a hotel` använder samma vokabulär för `book a ` . Det här formatet är samma så det bör vara samma avsikt med de olika orden i och som `flight` `hotel` extraherade entiteter.

## <a name="do-add-features-to-intents"></a>Lägga till funktioner i avsikter

Funktioner beskriver begrepp för en avsikt. En funktion kan vara en fraslista med ord som är viktiga för den avsikten eller en entitet som är viktig för den avsikten.

## <a name="do-find-sweet-spot-for-intents"></a>Hitta god punkt för avsikter
Använd förutsägelsedata från LUIS för att avgöra om dina avsikter överlappar varandra. Överlappande avsikter förvirrar LUIS. Resultatet är att avsikten med högst poäng är för nära en annan avsikt. Eftersom LUIS inte använder exakt samma väg genom data för träning varje gång, har en överlappande avsikt en chans att vara den första eller andra i träningen. Du vill att yttrandenas poäng för varje avsikt ska vara längre ifrån varandra så att det här inte händer. Bra skillnad för avsikter bör resultera i den förväntade högsta avsikten varje gång.

## <a name="do-use-machine-learned-entities"></a>Använda maskininlärda entiteter

Maskininlärda entiteter skräddarsys efter din app och kräver etiketter för att lyckas. Om du inte använder maskininlärda entiteter kanske du använder fel verktyg.

Maskininlärda entiteter kan använda andra entiteter som funktioner. Dessa andra entiteter kan vara anpassade entiteter som entiteter för reguljära uttryck eller lista entiteter, eller så kan du använda fördefinierade entiteter som funktioner.

Lär dig mer om [effektiva maskininlärda entiteter](luis-concept-entity-types.md#machine-learned-ml-entity).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Skapa din app iterativt med versioner

Varje redigeringscykel ska finnas i en ny [version](./luis-concept-app-iteration.md), klonad från en befintlig version.

## <a name="do-build-for-model-decomposition"></a>Skapa för modelldekomposition

Modelldekomposition har en typisk process som:

* skapa **avsikt** baserat på klientappens användarvsikter
* lägga till 15–30 exempelyttranden baserat på verkliga användarindata
* etikett för datakoncept på den översta nivån i exempelyttrande
* dela upp datakonceptet i underentiteter
* lägga till funktioner i underentiteter
* lägga till funktioner i avsikter

När du har skapat avsikten och lagt till exempelyttranden beskriver följande exempel entitetsdeposition.

Börja med att identifiera fullständiga databegrepp som du vill extrahera i ett uttryck. Det här är din maskininlärningsentitet. Sedan delar du upp frasen i dess delar. Detta omfattar att identifiera underentiteter och funktioner.

Om du till exempel vill extrahera en adress kan den översta maskininlärningsentiteten kallas `Address` . När du skapar adressen identifierar du några av dess underentiteter, till exempel gatuadress, ort, delstat och postnummer.

Fortsätt att dekomprimera dessa element genom att:
* Lägga till en obligatorisk funktion i postnumret som en entitet för reguljära uttryck.
* Dekomprimera gatuadressen i delar:
    * Ett **gatunummer** med en obligatorisk funktion i en förbyggd entitet med nummer.
    * Ett **gatunamn**.
    * En **gatutyp** med en obligatorisk funktion i en listentitet, inklusive ord som väg, cirkel, väg och band.

V3-redigerings-API:et tillåter modelldepositionering.

## <a name="do-add-patterns-in-later-iterations"></a>Lägg till mönster i senare iterationer

Du bör förstå hur appen [](luis-concept-patterns.md) beter sig innan du lägger till mönster eftersom mönstren viktas mer än exempelyttrande och kommer att förvränga konfidensen.

När du förstår hur din app beter sig kan du lägga till mönster som de gäller för din app. Du behöver inte lägga till dem med varje [iteration](luis-concept-app-iteration.md).

Det skadar inte att lägga till dem i början av modelldesignen, men det är lättare att se hur varje mönster ändrar modellen när modellen har testats med yttranden.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Balansera dina yttranden över alla avsikter

För att LUIS-förutsägelserna ska vara korrekta måste antalet exempelyttranden i varje avsikt (med undantag för avsikten Ingen) vara relativt lika.

Om du har en avsikt med 100 exempelyttranden och en avsikt med 20 exempelyttranden har avsikten 100-yttranden en högre förutsägelsefrekvens.

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempelyttranden till avsikten Ingen

Den här avsikten är reserv avsikt, som anger allt utanför ditt program. Lägg till ett exempelyttrande till avsikten None (Ingen) för varje 10 exempelyttrande i resten av LUIS-appen.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Använd funktionen föreslå för aktiv inlärning

Använd [regelbundet granskning](luis-how-to-review-endpoint-utterances.md)av **slutpunktsyttranden** för aktiv inlärning i stället för att lägga till fler exempelyttranden till avsikter. Eftersom appen hela tiden får ta emot ta emot slutpunktsyttranden växer och ändras den här listan.

## <a name="do-monitor-the-performance-of-your-app"></a>Övervaka appens prestanda

Övervaka förutsägelsen med hjälp av en [batchtestuppsättning.](./luis-how-to-batch-test.md)

Behåll en separat uppsättning yttranden som inte används som [exempelyttranden](luis-concept-utterance.md) eller slutpunktsyttranden. Fortsätt att förbättra appen för testuppsättningen. Anpassa testuppsättningen så att den återspeglar verkliga användaryttranden. Använd den här testuppsättningen för att utvärdera varje iteration eller version av appen.

## <a name="dont-publish-too-quickly"></a>Publicera inte för snabbt

Om du publicerar din app för snabbt, [utan korrekt](#do-plan-your-schema)planering, kan det leda till flera problem, till exempel:

* Din app fungerar inte i ditt faktiska scenario på en acceptabel prestandanivå.
* Schemat (avsikter och entiteter) skulle inte vara lämpligt, och om du har utvecklat klientapplogik efter schemat kan du behöva skriva om det från grunden. Detta skulle orsaka oväntade fördröjningar och en extra kostnad för projektet som du arbetar med.
* Yttranden som du lägger till i modellen kan orsaka bias mot exempelyttrandeuppsättningen som är svår att felsöka och identifiera. Det gör det också svårt att ta bort tvetydigheter när du har bestämt dig för ett visst schema.

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempelyttranden till avsikter

När appen har publicerats lägger du bara till yttranden från aktiv inlärning i utvecklingsprocessen. Om yttrandena är för lika lägger du till ett mönster.

## <a name="dont-use-few-or-simple-entities"></a>Använd inte några få eller enkla entiteter

Entiteter har skapats för extrahering och förutsägelse av data. Det är viktigt att varje avsikt har maskininlärningsentiteter som beskriver data i avsikten. Detta hjälper LUIS att förutsäga avsikten, även om klientprogrammet inte behöver använda den extraherade entiteten.

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som utbildningsplattform

LUIS är specifikt för en språkmodells domän. Det är inte avsett att fungera som en allmän utbildningsplattform för naturligt språk.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempelyttranden i samma format, ignorera andra format

LUIS förväntar sig variationer i en avsikts yttranden. Yttrandena kan variera samtidigt som de har samma övergripande betydelse. Variationer kan omfatta ta upp yttranden, ordval och ordplacering.

|Använd inte samma format|Använd olika format|
|--|--|
|Köpa ett ärende till Seattle<br>Köpa en biljett till Paris<br>Köpa ett ärende till Orlando|Köp 1 biljett till Seattle<br>Boka två platser på det röda ögat mot Paris nästa måndag<br>Jag vill boka 3 biljetter till Orlando för vår paus|

Den andra kolumnen använder olika verb (köp, reservera, boka), olika kvantiteter (1, två, 3) och olika ordordningar, men alla har samma avsikt att köpa flygbiljetter för resor.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av avsikter och entiteter

Skapa en avsikt för alla åtgärder som din robot ska vidta. Använd entiteter som parametrar som gör åtgärden möjlig.

För en robot som ska boka flygresor skapar du en **BookFlight-avsikt.** Skapa inte en avsikt för alla flygbolag eller alla mål. Använd dessa datadelar som [entiteter](luis-concept-entity-types.md) och markera dem i exempelyttrandena.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Skapa inte fraslistor med alla möjliga värden

Ange några exempel i [fraslistorna, men](luis-concept-feature.md) inte alla ord eller fraser. LUIS generaliserar och tar hänsyn till kontexten.

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster

Lägg inte till för [många mönster](luis-concept-patterns.md). LUIS är avsedd att lära sig snabbt med färre exempel. Överbelasta inte systemet i onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Träna och publicera inte med varje enskilt exempelyttrande

Lägg till 10 eller 15 yttranden innan du tränar och publicerar. På så sätt kan du se hur förutsägelsernas noggrannhet påverkas. Att lägga till ett enskilt uttryck kanske inte har någon synlig inverkan på poängen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur [du planerar din app](luis-how-plan-your-app.md) i luis-appen.