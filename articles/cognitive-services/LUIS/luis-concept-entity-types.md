---
title: Entitetstyper – LUIS
description: En entitet extraherar data från ett användaryttrande vid förutsägelsekörning. Ett _valfritt,_ sekundärt syfte är att öka förutsägelsen av avsikten eller andra entiteter med hjälp av entiteten som en funktion.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500336"
---
# <a name="entities-in-luis"></a>Entiteter i LUIS

En entitet är ett objekt eller ett element som är relevant för användarens avsikt. Entiteter definierar data som kan extraheras från yttrandena och är nödvändiga för att slutföra en användares åtgärd som krävs. Exempel:

|Yttrande|Förväntad avsikt|Entiteter som extraherats|Förklaring|
|--|--|--|--|
|Hej hur mår du?|Hälsning|-|Inget att extrahera.|
|Jag vill beställa en liten pizza|orderPizza| "liten" | Entiteten "Storlek" extraheras som "liten" .|
|Stäng av sovrumsbelysning|Avfarten| "sovrum" | Entiteten "Rum" extraheras som "sovrum".|
|Kontrollera saldot i mitt sparande som slutar på 4406|checkBalance| "savings", "4406" | "accountType"-entiteten extraheras som "savings" och "accountNumber"-entiteten extraheras som "4406".|
|Köp 3 biljetter till New York|buyTickets| "3", "New York" | Entiteten "ticketsCount" extraheras som "3" och "Destination"-entiteten extraheras som "New York".|

Entiteter är valfria men rekommenderas. Du behöver inte skapa entiteter för varje koncept i din app, bara för dem där:

* Klientprogrammet behöver data, eller 
* Entiteten fungerar som ett tips eller en signal till en annan entitet eller avsikt. Om du vill veta mer om entiteter när funktioner går du [till Entiteter som funktioner](#entities-as-features).

## <a name="entity-types"></a>Entitetstyper

Om du vill skapa en entitet måste du ge den ett namn och en typ. Det finns flera typer av entiteter i LUIS. 

### <a name="list-entity"></a>Lista entitet

En listentitet representerar en fast, stängd uppsättning relaterade ord tillsammans med deras synonymer. Du kan använda listentiteter för att identifiera flera synonymer eller varianter och extrahera normaliserade utdata för dem. Använd det *rekommenderade* alternativet för att se förslag på nya ord baserat på den aktuella listan. 

En listentitet är inte maskininlärd, vilket innebär att LUIS inte identifierar ytterligare värden för listentiteter. LUIS markerar alla matchningar till ett objekt i en lista som en entitet i svaret.

Matchning i listentiteter är både fallkänsligt och måste vara en exakt matchning som ska extraheras. Normaliserade värden används också när du matchar listentiteten. Exempel:

|Normaliserat värde|Synonymer|
|--|--|
|Liten|sm, sml, tiny, smallest|
|Medel|md, mdm, regular, average, middle|
|Stor|lg, lrg, big|

Mer information [finns i referensartikeln lista](reference-entity-list.md) över entiteter.

### <a name="regex-entity"></a>Regex-entitet

En entitet för reguljära uttryck extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger. Den ignorerar fallet och ignorerar kulturell variant. Reguljärt uttryck passar bäst för strukturerad text eller en fördefinierad sekvens med alfanumeriska värden som förväntas i ett visst format. Exempel:

|Entitet|Reguljärt uttryck|Exempel|
|--|--|--|
|Flygnummer|flight [A–Z] {2} [0–9]{4}| flight AS 1234|
|Kreditkortsnummer|[0-9]{16}|5478789865437632|

Mer information [finns i referensartikeln för](reference-entity-regular-expression.md) regex-entiteter.

### <a name="prebuilt-entity"></a>Fördefinierad entitet

LUIS erbjuder en uppsättning fördefinierade entiteter för att identifiera vanliga typer av data som namn, datum, nummer och valuta.  Beteendet för fördefinierade entiteter är fast. Stöd för fördefinierade entiteter varierar beroende på LUIS-appens kultur. Exempel:

|Förbyggd entitet|Exempelvärde|
|--|--|
|PersonName|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2 maj 08:00 den 2 maj 2019|

Mer information [finns i referensartikeln om fördefinierade](./luis-reference-prebuilt-entities.md) entiteter.

### <a name="patternany-entity"></a>Pattern.Any-entitet

Ett mönster. En entitet är en platshållare med variabel längd som endast används i ett mönsters mutterance för att markera var entiteten börjar och slutar. Den följer en specifik regel eller ett visst mönster och används bäst för meningar med fast lexikal struktur. Exempel:

|Exempel på yttrande|Mönster|Entitet|
|--|--|--|
|Kan jag ha en er?|Kan jag ha en {}} [please][?]| Burger
|Kan jag få en pizza?|Kan jag ha en {}} [please][?]| Pizza
|Var hittar jag The Great Gatsby?|Var hittar jag {bookName}?| Den stora Gatsby|

Mer information [finns i referensartikeln Pattern.Any-entiteter.](./reference-entity-pattern-any.md)

### <a name="machine-learned-ml-entity"></a>Maskininlärd entitet (ML)

Entiteten Maskininlärd använder kontext för att extrahera entiteter baserat på märkta exempel. Det är den föredragna entiteten för att skapa LUIS-program. Den förlitar sig på maskininlärningsalgoritmer och kräver att etiketter skräddarsys för ditt program. Använd en ML-entitet för att identifiera data som inte alltid är väl formaterade men har samma betydelse. 

|Exempel på yttrande|Extraherad *produktentitet*|
|--|--|
|Jag vill köpa en bok.|"bok"|
|Kan jag skaffa de här skorna?|"skor"|
|Lägg till shortsen i varukorgen.|"shorts"|

Du kan läsa mer om maskininlärda entiteter [här.](./reference-entity-machine-learned-entity.md)

Mer information [finns i referensartikeln om maskininlärda](./reference-entity-pattern-any.md) entiteter.

#### <a name="ml-entity-with-structure"></a>ML-entitet med struktur

En ML-entitet kan bestå av mindre underentiteter som var och en kan ha sina egna egenskaper. Adress kan *till* exempel ha följande struktur:

* Adress: 4567 Main Street, NY, 98052, USA
    * Byggnummer: 4567
    * Gatunamn: Main Street
    * Tillstånd: NY
    * Postnummer: 98052
    * Land: USA


### <a name="building-effective-ml-entities"></a>Skapa effektiva ML-entiteter

Följ dessa metodtips för att skapa maskininlärda entiteter på ett effektivt sätt:

* Om du har en maskininlärd entitet med underordnade entiteter kontrollerar du att de olika beställningarna och varianterna för entiteten och underentiteterna visas i de märkta yttrandena. Märkta exempelyttranden ska innehålla alla giltiga formulär och innehålla entiteter som visas och som saknas och som också ordnas om i yttrandena.

* Undvik att överanpassa entiteterna till en mycket fast uppsättning. Överanpassas när modellen inte generaliseras väl och är ett vanligt problem i maskininlärningsmodeller. Detta innebär att appen inte skulle fungera på nya typer av exempel på rätt sätt. I sin tur bör du variera de märkta exempelyttrandena så att appen kan generalisera bortom de begränsade exempel som du anger.

* Etiketterna ska vara konsekventa för avsikterna. Detta inkluderar även yttranden som du anger i avsikten *Ingen som innehåller* den här entiteten. Annars kommer modellen inte att kunna fastställa sekvenserna effektivt.

## <a name="entities-as-features"></a>Entiteter som funktioner

En annan viktig funktion för entiteter är att använda dem som funktioner eller särskilja egenskaper för andra avsikter eller entiteter så att systemet observerar och lär sig genom dem.

### <a name="entities-as-features-for-intents"></a>Entiteter som funktioner för avsikter

Du kan använda entiteter som en signal för en avsikt. Förekomsten av en viss entitet i yttrandena kan till exempel skilja vilken avsikt den faller under.

|Exempel på yttrande|Entitet|Avsikt|
|--|--|--|
|Boka en *strid mot New York*.|City|Boka flyg|
|Boka *huvudkonferensrummet*.|Rum|Reservera rum|

### <a name="entities-as-feature-for-entities"></a>Entiteter som funktion för entiteter

Du kan också använda entiteter som en indikator på förekomsten av andra entiteter. Ett vanligt exempel på detta är att använda en förbyggd entitet som en funktion för en annan ML-entitet.
Om du skapar ett bokningssystem för flyg och ditt uttryck ser ut som "Boka en flygresa från Destinations till Seattle" kommer du att ha *Origin City* och Destination *City* som ML-entiteter. En bra idé är att använda den fördefinierade entiteten som `GeographyV2` en funktion för båda entiteterna.

Mer information [finns i referensartikeln geographyV2-entiteter.](./luis-reference-prebuilt-geographyv2.md)

Du kan också använda entiteter som nödvändiga funktioner för andra entiteter. Detta hjälper till att lösa extraherade entiteter. Om du till exempel skapar ett program för pizzabeställning och du har en ML-entitet kan du skapa entiteten lista och använda den som en obligatorisk `Size` `SizeList` funktion för `Size` entiteten. Programmet returnerar det normaliserade värdet som den extraherade entiteten från yttrandena. 

Se [funktioner](luis-concept-feature.md) för mer information och fördefinierade entiteter [för](./luis-reference-prebuilt-entities.md) att lära dig mer om fördefinierade entiteter som är tillgängliga i din kultur. 


## <a name="entity-prediction-status-and-errors"></a>Entitetens förutsägelsestatus och fel

LUIS-portalen visar följande när entiteten har en annan entitetsförutsägelse än den entitet som du har märkt för ett exempelyttrande. Den här olika poängen baseras på den aktuella tränade modellen. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS-portalen visar när entiteten har en annan entitetsförutsägelse än den entitet som du valde för ett exempelyttrande":::

Texten som orsakar felet markeras i exempelyttrandet och exempelyttranderaden har en felindikator till höger, som visas som en röd triangel. 

Prova ett eller flera av följande för att lösa entitetsfel:

* Den markerade texten är felmärkt. Åtgärda problemet genom att granska etiketten, korrigera den och träna om appen. 
* Skapa en [funktion för](luis-concept-feature.md) entiteten som hjälper dig att identifiera entitetens koncept.
* Lägg till [fler exempelyttranden och](luis-concept-utterance.md) etikettera med entiteten.
* [Granska förslag på aktiv inlärning](luis-concept-review-endpoint-utterances.md) för yttranden som tas emot vid förutsägelseslutpunkten och som kan hjälpa dig att identifiera entitetens begrepp.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [bra exempelyttranden.](luis-concept-utterance.md)
* Mer [information om hur](luis-how-to-add-entities.md) du lägger till entiteter i LUIS-appen finns i Lägga till entiteter.
* Läs mer om [luis-programbegränsningar.](./luis-limits.md) 
* Använd en [självstudie för](tutorial-machine-learned-entity.md) att lära dig hur du extraherar strukturerade data från ett uttryck med hjälp av maskininlärningsentiteten.
