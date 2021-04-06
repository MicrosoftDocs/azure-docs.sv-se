---
title: Metod tips för att skapa en LUIS-app
description: Lär dig mer om bästa praxis för att få bästa möjliga resultat från LUIS-appens modell.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2f6ed85416cc5d7c3c2baba2b2cfe489e301d7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788493"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Metod tips för att skapa en LUIS-app (Language förståelseing)
Använd redigerings processen för appar för att skapa LUIS-appen:

* Bygg språks modeller (avsikter och entiteter)
* Lägg till några utbildnings exempel yttranden (15-30 per avsikt)
* Publicera till slut punkt
* Testa från slut punkt

När din app har [publicerats](luis-how-to-publish-app.md)använder du utvecklings livs cykeln för att lägga till funktioner, publicera och testa från slut punkten. Påbörja inte nästa redigerings cykel genom att lägga till fler exempel yttranden eftersom det inte tillåter LUIS att lära sig din modell med verkliga användar yttranden.

Expandera inte yttranden förrän den aktuella uppsättningen av både exempel-och slut punkts yttranden returnerar trygg, hög förutsägelse poäng. Förbättra poängen med hjälp av [aktiv inlärning](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Gör och inte
I följande lista finns metod tips för LUIS-appar:

|Gör följande|Gör inte följande|
|--|--|
|[Planera ditt schema](#do-plan-your-schema)|[Bygg och publicera utan en plan](#dont-publish-too-quickly)|
|[Definiera distinkta avsikter](#do-define-distinct-intents)<br>[Lägg till funktioner till avsikter](#do-add-features-to-intents)<br>
[Använda enheter som har lärts till enheten](#do-use-machine-learned-entities) |[Lägg till många exempel yttranden till avsikter](#dont-add-many-example-utterances-to-intents)<br>[Använd få eller enkla entiteter](#dont-use-few-or-simple-entities) |
|[Hitta en söt punkt mellan för allmän och för varje avsikt](#do-find-sweet-spot-for-intents)|[Använd LUIS som utbildnings plattform](#dont-use-luis-as-a-training-platform)|
|[Bygg din app iterativt med versioner](#do-build-your-app-iteratively-with-versions)<br>[Bygg entiteter för modell nedbrytning](#do-build-for-model-decomposition)|[Lägg till många exempel yttranden i samma format, ignorera andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägga till mönster i senare iterationer](#do-add-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Balansera din yttranden för alla syften](#balance-your-utterances-across-all-intents) förutom ingen avsikt.<br>[Lägg till exempel yttranden i ingen avsikt](#do-add-example-utterances-to-none-intent)|[Skapa fras listor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Dra nytta av funktionen föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägg till för många mönster](#dont-add-many-patterns)|
|[Övervaka appens prestanda med batch-testning](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enda exempel-uttryck tillagt](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Planera ditt schema

Innan du börjar skapa appens schema bör du identifiera vad och var du planerar att använda den här appen. Desto mer grundlig och mer detaljerad planering, desto bättre blir appen.

* Referensinformation riktade användare
* Definiera personer från slut punkt till slut punkt som representerar din app – röst, avatar, ärende hantering (proaktivt, reaktiv)
* Identifiera användar interaktioner (text, tal) genom vilka kanaler, lämna ut till befintliga lösningar eller skapa en ny lösning för den här appen
* Slut punkt till slut punkt för användar resa
    * Vad bör du förvänta dig att den här appen inte gör det? * Vilka är prioriteringarna för vad det ska göra?
    * Vilka är de viktigaste användnings fallen?
* Samla in data – [Lär dig](data-collection.md) att samla in och förbereda data

## <a name="do-define-distinct-intents"></a>Definiera distinkta avsikter
Se till att ord listan för varje avsikt är just för den avsikten och inte överlappar med en annan avsikt. Om du till exempel vill ha en app som hanterar rese arrangemang som flyg bolags flygningar och hotell kan du välja att ha dessa ämnes områden som separata avsikter eller samma avsikt med entiteter för särskilda data inuti uttryck.

Om ord listan mellan två avsikter är densamma kombinerar du avsikten och använder entiteter.

Överväg följande exempel yttranden:

|Exempel på yttranden|
|--|
|Boka en flygning|
|Boka ett hotell|

`Book a flight` och `Book a hotel` Använd samma vokabulär i `book a ` . Det här formatet är detsamma, så det bör vara samma avsikt med de olika orden av `flight` och `hotel` som extraherade entiteter.

## <a name="do-add-features-to-intents"></a>Lägg till funktioner till avsikter

Funktioner beskriver begrepp för ett avsikts alternativ. En funktion kan vara en fras lista med ord som är viktiga för avsikten eller en enhet som är viktig för avsikten.

## <a name="do-find-sweet-spot-for-intents"></a>Hittar du söt för avsikter
Använd förutsägelse data från LUIS för att avgöra om dina avsikter överlappar. Överlappande avsikter förvirrar LUIS. Resultatet är att den översta bedömnings avsikten är för nära en annan avsikt. Eftersom LUIS inte använder exakt samma sökväg via data för utbildning varje gång, har en överlappande avsikt möjlighet att vara första eller andra i utbildningen. Du vill att uttryck-poängen för varje avsikt att ligga längre bort, så att den här flip/vippa inte sker. Bra distinktion för avsikter bör resultera i förväntat topp-avsikt varje gång.

## <a name="do-use-machine-learned-entities"></a>Använd enheter som lärts av enheten

Enheter som lärts in är anpassade efter din app och kräver att etiketter lyckas. Om du inte använder enheter som har lärts av enheten kan du använda fel verktyg.

Enheter som har lärts in kan använda andra entiteter som funktioner. Dessa andra entiteter kan vara anpassade entiteter som reguljära uttryck entiteter eller List enheter, eller så kan du använda färdiga entiteter som funktioner.

Lär dig mer om de [inlärda enheterna på datorn](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Skapa en app iterativt med versioner

Varje redigerings cykel bör vara i en ny [version](./luis-concept-app-iteration.md)som klonas från en befintlig version.

## <a name="do-build-for-model-decomposition"></a>Skapa modell sammansättning

Modell dispositionen har en typisk process för:

* skapa **avsikt** baserat på klientens användar avsikter
* Lägg till 15-30-exempel yttranden baserat på verkliga indata från användaren
* Märk data koncept på översta nivån i exemplet uttryck
* Bryt data koncept i underentiteter
* Lägg till funktioner i underentiteter
* Lägg till funktioner till avsikter

När du har skapat avsikten och lagt till exempel yttranden, beskriver följande exempel enhets diskompositionen.

Börja med att identifiera fullständiga data koncept som du vill extrahera i en uttryck. Det här är din dator inlärnings enhet. Dela sedan upp frasen i dess delar. Detta omfattar att identifiera underentiteter och funktioner.

Om du till exempel vill extrahera en adress kan den översta enheten för maskin inlärning anropas `Address` . När du skapar adressen identifierar du några av dess underentiteter, till exempel gatuadress, stad, delstat och post nummer.

Fortsätt sammanställningen av dessa element genom att:
* Lägga till en nödvändig funktion i post numret som en entitet för reguljära uttryck.
* Dela upp gatuadressen i delar:
    * Ett **gatu nummer** med en obligatorisk funktion i en fördefinierad entitet med nummer.
    * Ett **gatu namn**.
    * En **gatu typ** med en obligatorisk funktion i en List-entitet, inklusive ord som minimering, cirkel, väg och Lane.

V3-redigerings-API: n möjliggör modell dekomposition.

## <a name="do-add-patterns-in-later-iterations"></a>Lägg till mönster i senare iterationer

Du bör förstå hur appen beter sig innan du lägger till [mönster](luis-concept-patterns.md) eftersom mönstren viktas mer kraftigt än yttranden och kommer att skeva förtroendet.

När du förstår hur appen beter sig lägger du till mönster som de gäller för din app. Du behöver inte lägga till dem med varje [iteration](luis-concept-app-iteration.md).

Det går inte att lägga till dem i början av din modell design, men det är enklare att se hur varje mönster ändrar modellen när modellen har testats med yttranden.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Balansera din yttranden för alla avsikter

För att LUIS-förutsägelserna ska vara korrekta, måste antalet exempel yttranden i varje avsikt (utom för ingen avsikt) vara relativt lika.

Om du har en avsikt med 100-exempel yttranden och en avsikt med 20 exempel yttranden, har 100-uttryck-avsikten en högre frekvens av förutsägelse.

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden i ingen avsikt

Avsikten är återställnings avsikten, vilket indikerar allt utanför ditt program. Lägg till ett exempel uttryck till ingen avsikt för varje 10-exempel yttranden i resten av din LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Använd funktionen föreslå funktion för aktiv inlärning

Använd den [aktiva utbildnings](luis-how-to-review-endpoint-utterances.md) **slut punkten yttranden** regelbundet, i stället för att lägga till fler exempel yttranden till avsikter. Eftersom appen ständigt tar emot slut punkts yttranden, växer och ändras den här listan.

## <a name="do-monitor-the-performance-of-your-app"></a>Övervaka appens prestanda

Övervaka förutsägelse noggrannheten med hjälp av [batch](./luis-how-to-batch-test.md) -testuppsättningen.

Behåll en separat uppsättning yttranden som inte används som [exempel yttranden](luis-concept-utterance.md) eller slut punkts yttranden. Fortsätt att förbättra appen för din test uppsättning. Anpassa test uppsättningen så att den återspeglar Real User yttranden. Använd den här test uppsättningen för att utvärdera varje iteration eller version av appen.

## <a name="dont-publish-too-quickly"></a>Publicera inte för snabbt

Att publicera din app för snabbt, utan [korrekt planering](#do-plan-your-schema), kan leda till flera problem som:

* Din app kommer inte att fungera i det faktiska scenariot med en acceptabel prestanda nivå.
* Schemat (avsikter och entiteter) är inte lämpligt, och om du har utvecklat klient program logiken efter schemat kan du behöva skriva om från början. Detta skulle leda till oväntade fördröjningar och en extra kostnad för det projekt som du arbetar med.
* Yttranden som du lägger till i modellen kan orsaka en förskjutning mot exempel uttryck uppsättning som är svår att felsöka och identifiera. Den kommer också att göra det svårt att ta bort tvetydighet när du har gjort ett visst schema.

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel yttranden till avsikter

När appen har publicerats kan du bara lägga till yttranden från Active Learning i utvecklings livs cykel processen. Om yttranden är för likartade lägger du till ett mönster.

## <a name="dont-use-few-or-simple-entities"></a>Använd inte några få eller enkla entiteter

Entiteter skapas för data extrahering och förutsägelser. Det är viktigt att varje avsikt har enheter för maskin inlärning som beskriver data i avsikten. Det hjälper LUIS att förutsäga avsikten, även om klient programmet inte behöver använda den extraherade entiteten.

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som utbildnings plattform

LUIS är specifik för en språk modells domän. Den är inte avsedd att fungera som en allmän utbildnings plattform för naturliga språk.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempel yttranden i samma format, ignorera andra format

LUIS förväntar sig variationer i ett avsikts yttranden. Yttranden kan variera med samma övergripande betydelse. Variationer kan vara uttryck längd, Word-val och ord placering.

|Använd inte samma format|Använd varierande format|
|--|--|
|Köp ett ärende till Seattle<br>Köp ett ärende till Paris<br>Köp ett ärende till Orlando|Köp 1-biljett till Seattle<br>Reservera två platser på det röda ögat till Paris nästa måndag<br>Jag vill boka 3 biljetter till Orlando för fjädrad rast|

Den andra kolumnen använder olika verb (Köp, reservera, bok), olika kvantiteter (1, 2, 3) och olika ordningar av ord, men alla har samma avsikt att köpa flyg biljetter för resor.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av avsikter och entiteter

Skapa ett avsikts sätt för alla åtgärder som din robot tar. Använd entiteter som parametrar som gör åtgärden möjlig.

För en robot som ska boka flyg Plans flygningar skapar du en **BookFlight** avsikt. Skapa inte en avsikt för varje flyg bolag eller varje mål. Använd dessa delar av data som [entiteter](luis-concept-entity-types.md) och markera dem i exemplet yttranden.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Skapa inte fras listor med alla möjliga värden

Ange några exempel i [fras listorna](luis-concept-feature.md) , men inte varje ord eller fras. LUIS generaliserar och tar hänsyn till sammanhang i kontot.

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster

Lägg inte till för många [mönster](luis-concept-patterns.md). LUIS är tänkt att lära sig snabbt med färre exempel. Överbelasta inte systemet i onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Träna och publicera inte med alla enkla exempel uttryck

Lägg till 10 eller 15 yttranden innan du tränar och publicerar. Det gör att du kan se påverkan på förutsägelse noggrannhet. Att lägga till en enskild uttryck kanske inte har en synlig inverkan på poängen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planerar din app](luis-how-plan-your-app.md) i Luis-appen.