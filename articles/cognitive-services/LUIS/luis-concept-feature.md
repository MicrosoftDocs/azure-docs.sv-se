---
title: Funktioner för maskin inlärning med LUIS
description: Lägg till funktioner i en språk modell för att ge tips om hur du identifierar indatatyper som du vill etikettera eller klassificera.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: da85abdff3d1022659f2d4e83fd14c5ae6003fc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100546065"
---
# <a name="machine-learning-features"></a>Funktioner för maskin inlärning

I Machine Learning är en *funktion*   en särskiljande egenskaps-eller dataattribut som systemet ser och lär sig genom.

Funktioner för maskin inlärning ger LUIS viktiga ikoner för var du ska leta efter saker som särskiljer ett koncept. De är tips som LUIS kan använda, men de är inte hårda regler. LUIS använder dessa tips tillsammans med etiketterna för att hitta data.

En funktion kan beskrivas som en funktion, t. ex. f (x) = y. I exemplet uttryck, visar funktionen var du vill leta efter den särskiljande traiten. Använd den här informationen för att skapa ditt schema.

## <a name="types-of-features"></a>Typer av funktioner

Funktioner är en nödvändig del av schema designen. LUIS stöder både fras listor och modeller som funktioner:

* Funktionen fras lista
* Modell (avsikt eller entitet) som en funktion

## <a name="find-features-in-your-example-utterances"></a>Hitta funktioner i ditt exempel yttranden

Eftersom LUIS är ett språk baserat program, är funktionerna textbaserade. Välj text som anger den trait som du vill särskilja. Den minsta enheten är *token* för Luis. För det engelska språket är en token ett sammanhängande intervall med bokstäver och siffror som inte innehåller blank steg eller skiljetecken.

Eftersom blank steg och skiljetecken inte är tokens, fokuserar du på de text LED trådar som du kan använda som funktioner. Kom ihåg att inkludera variationer av ord, till exempel:

* plural-formulär
* verb
* förkortningar
* stavningar och fel stavningar

Ta reda på om texten, eftersom den särskiljer en trait, måste:

* Matcha ett exakt ord eller en fras: Överväg att lägga till en entitet för reguljära uttryck eller en lista entitet som en funktion i entiteten eller avsikten.
* Matcha ett välkänt koncept som datum, tider eller personers namn: Använd en fördefinierad entitet som funktion till entiteten eller avsikten.
* Lär dig nya exempel över tid: Använd en fras lista med några exempel på konceptet som funktion för entiteten eller avsikten.

## <a name="create-a-phrase-list-for-a-concept"></a>Skapa en fras lista för ett koncept

En fras lista är en lista med ord eller fraser som beskriver ett begrepp. En fras lista används som Skift läges känslig matchning på token-nivå.

När du lägger till en fras lista kan du ställa in funktionen som **[Global](#global-features)**. En global funktion gäller hela appen.

### <a name="when-to-use-a-phrase-list"></a>När du ska använda en fras lista

Använd en fras lista när du behöver LUIS-appen för att generalisera och identifiera nya objekt för konceptet. Fras listor är som domänbaserad vokabulär. De förbättrar kvaliteten på förståelse för avsikter och entiteter.

### <a name="how-to-use-a-phrase-list"></a>Så här använder du en fras lista

Med en fras lista anser LUIS kontext och generalizes för att identifiera objekt som liknar, men som inte är en exakt text matchning. Följ dessa steg om du vill använda en fras lista:

1. Börja med en enhet för maskin inlärning:
    1. Lägg till exempel yttranden.
    1. Etikett med en enhet för maskin inlärning.
1. Lägg till en fras lista:
    1. Lägg till ord med liknande betydelse. Lägg inte till några ord eller fraser. Lägg i stället till några ord eller fraser i taget. Sedan träna och publicera.
    1. Granska och Lägg till föreslagna ord.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Ett typiskt scenario för en fras lista

Ett typiskt scenario för en fras lista är att förstärka ord som är relaterade till en bestämd idé.

Medicinska villkor är ett användbart exempel på ord som kan behöva en fras lista för att öka deras betydelse. Dessa villkor kan ha olika fysiska, kemiska, terapeutiska eller abstrakta betydelser. LUIS vet inte villkoren som är viktiga för din ämnes domän utan någon fras lista.

Så här extraherar du de medicinska villkoren:

1. Skapa exempel yttranden och märk medicinska villkor inom dessa yttranden.
2. Skapa en fras lista med exempel på villkoren i ämnes domänen. Den här fras listan ska innehålla den faktiska termen som du har märkt och andra termer som beskriver samma koncept.
3. Lägg till fras listan i entiteten eller underentiteten som extraherar det koncept som används i fras listan. Det vanligaste scenariot är en komponent (underordnad) till en enhet för maskin inlärning. Om fras listan ska användas för alla avsikter eller entiteter markerar du fras listan som en global fras lista. Flaggan **enabledForAllModels** styr det här modell omfånget i API: et.

### <a name="token-matches-for-a-phrase-list"></a>Token-matchningar för en fras lista

En fras lista gäller alltid på token-nivå. I följande tabell visas hur en fras lista som har ordet **Ann** tillämpas på varianter av samma tecken i den ordningen.


| Token variation för **Ann** | Fras lista matchning när token hittas |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Ja-token är **Ann**                  |
| **Ann**                    | Ja-token är **Ann**                  |
| **Anne**                     | No-token är **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>En modell som en funktion hjälper en annan modell

Du kan lägga till en modell (avsikt eller entitet) som en funktion till en annan modell (avsikt eller entitet). Genom att lägga till en befintlig avsikts-eller entitet som funktion lägger du till ett väldefinierat begrepp som har märkta exempel.

När du lägger till en modell som en funktion kan du ställa in funktionen som:
* **[Obligatoriskt](#required-features)**. En nödvändig funktion måste finnas för att modellen ska returneras från förutsägelse slut punkten.
* **[Global](#global-features)**. En global funktion gäller hela appen.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>När du ska använda en entitet som en funktion i avsikten

Lägg till en entitet som en funktion i avsikt när identifieringen av den enheten är signifikant för avsikten.

Exempel: om avsikten är att boka en flygning, t. ex. **BookFlight**, och entiteten är biljett information (till exempel antalet platser, ursprung och mål), ska du söka efter entiteten information om biljetten genom att lägga till en betydande vikt för förutsägelsen av **BookFlight** -avsikten.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>När du ska använda en entitet som en funktion till en annan entitet

En entitet (A) bör läggas till som en funktion till en annan entitet (B) när entitetens identifiering (A) är signifikant för förutsägelsen av entiteten (B).

Om t. ex. en entitet för leverans adress ingår i en underentitet för gatuadress, lägger du till en betydande vikt till förutsägelsen för entiteten leverans adress genom att leta upp underentiteten gata.

* Leverans adress (maskin inlärnings enhet):

    * Gatu nummer (underentitet)
    * Gatuadress (underentitet)
    * Ort (underentitet)
    * Delstat eller provins (underentitet)
    * Land/region (underentitet)
    * Post nummer (underentitet)

## <a name="nested-subentities-with-features"></a>Kapslade underentiteter med funktioner

En underentitet för maskin inlärning indikerar att det finns ett begrepp för den överordnade entiteten. Överordnad kan vara en annan underordnad entitet eller den översta entiteten. Värdet för underentiteten fungerar som en funktion till dess överordnade.

En underentitet kan ha både en fras lista och en modell (en annan entitet) som en funktion.

När underentiteten har en fras lista, ökar den vokabulären i konceptet, men lägger inte till någon information i JSON-svaret för förutsägelsen.

När underentiteten har en funktion i en annan entitet innehåller JSON-svaret de extraherade data för den andra entiteten.


## <a name="required-features"></a>Nödvändiga funktioner

En nödvändig funktion måste finnas för att modellen ska returneras från förutsägelse slut punkten. Använd en nödvändig funktion när du vet att inkommande data måste matcha funktionen.

Om uttryck-texten inte matchar den nödvändiga funktionen extraheras den inte.

En nödvändig funktion använder en entitet som inte är en maskin inlärning:

* Entitet med reguljärt uttryck
* Lista entitet
* Fördefinierad entitet

Om du är säker på att din modell kommer att finnas i datan, ställer du in funktionen efter behov. En nödvändig funktion returnerar inte något om den inte finns.

Du fortsätter med exemplet på leverans adressen:

Leverans adress (enheten har lärts)

 * Gatu nummer (underentitet)
 * Gatuadress (underentitet)
 * Gatu namn (underentitet)
 * Ort (underentitet)
 * Delstat eller provins (underentitet)
 * Land/region (underentitet)
 * Post nummer (underentitet)

### <a name="required-feature-using-prebuilt-entities"></a>Nödvändig funktion med fördefinierade entiteter

Färdiga entiteter som stad, delstat och land/region är vanligt vis en stängd uppsättning listor, vilket innebär att de inte ändras mycket över tid. Dessa entiteter kan ha relevanta rekommenderade funktioner och dessa funktioner kan markeras som obligatoriska. `isRequired`Flaggan är dock bara relaterad till den entitet som den är tilldelad till och påverkar inte hierarkin. Om den fördefinierade underentiteten inte hittas påverkar detta inte identifieringen och återlämningen av den överordnade entiteten.

Som ett exempel på en nödvändig funktion ska du överväga att identifiera adresser. Du kan överväga att göra ett gatu nummer till ett krav. Detta gör att en användare kan ange "1 Microsoft Way" eller "One Microsoft Way", och båda skulle matcha siffran "1" för den underordnade entiteten. Mer information finns i artikeln om [fördefinierad entitet ](luis-reference-prebuilt-entities.md) .

### <a name="required-feature-using-list-entities"></a>Nödvändig funktion med hjälp av list-entiteter

En [lista entitet](reference-entity-list.md) används som en lista över kanoniska namn tillsammans med deras synonymer. Som en nödvändig funktion, om uttryck inte inkluderar det kanoniska namnet eller en synonym, returneras inte entiteten som en del av förutsägelse slut punkten.

Anta att företaget endast levererar till en begränsad uppsättning länder/regioner. Du kan skapa en lista entitet som innehåller flera sätt för kunden att referera till landet/regionen. Om LUIS inte hittar en exakt matchning i texten för uttryck, returneras inte entiteten (som har den nödvändiga funktionen i list entiteten) i förutsägelsen.

|Kanoniskt namn|Synonymer|
|--|--|
|USA|USA<br>U. S. A<br>USA<br>USA<br>0|

Ett klient program, till exempel en chatt-robot, kan ställa en uppföljnings fråga för att hjälpa dig. Detta hjälper kunden att förstå att valet land/region är begränsat och *obligatoriskt*.

### <a name="required-feature-using-regular-expression-entities"></a>Nödvändig funktion med hjälp av reguljära uttrycks enheter

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) som används som en obligatorisk funktion innehåller funktioner för text matchning med RTF-format.

I leverans adress exemplet kan du skapa ett reguljärt uttryck som samlar in insamlings regler för post nummer för land/region.

## <a name="global-features"></a>Globala funktioner

Den vanligaste användningen är att använda en funktion för en specifik modell, men du kan konfigurera funktionen som en **Global funktion** för att tillämpa den på hela programmet.

Den vanligaste användningen av en global funktion är att lägga till en ytterligare ord lista i appen. Om dina kunder till exempel använder ett primärt språk, men förväntar sig att kunna använda ett annat språk inom samma uttryck, kan du lägga till en funktion som innehåller ord från det sekundära språket.

Eftersom användaren förväntar sig att använda det sekundära språket för alla syften eller enheter, lägger du till ord från det sekundära språket till fras listan. Konfigurera fras listan som en global funktion.

## <a name="combine-features-for-added-benefit"></a>Kombinera funktioner för extra förmån

Du kan använda mer än en funktion för att beskriva en trait eller ett begrepp. En gemensam koppling är att använda:

* En fras List funktion: du kan använda flera fras listor som funktioner till samma modell.
* En modell som funktion: [fördefinierad entitet](luis-reference-prebuilt-entities.md), [entitet för reguljära uttryck](reference-entity-regular-expression.md), [lista entitet](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Exempel: enhets funktioner för biljett bokning för en rese app  

Som ett grundläggande exempel bör du överväga en app för att boka en flygning med ett flyg boknings _syfte_ och en _entitet_ för biljett bokning. Entiteten biljett bokning samlar in information för att boka en flyg Plans biljett i ett boknings system. 

Machine Learning-entiteten för biljett bok har två underentiteter som du kan använda för att avbilda ursprung och mål. Funktionerna måste läggas till i varje underentitet, inte entiteten på den översta nivån.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Ticketbooking entitets schema":::

Entiteten för biljett bokning är en enhet för maskin inlärning med underentiteter, inklusive _ursprung_ och _mål_. Dessa underentiteter indikerar en geografisk plats. För att hjälpa till att extrahera platserna och skilja mellan _ursprung_ och _mål_ bör varje underentitet ha funktioner.

|Typ|Underentitet för ursprung |Underentitet för mål|
|--|--|--|
|Modell som en funktion|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) -fördefinierad entitet|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) -fördefinierad entitet|
|Fras lista|**Ursprungs ord**: `start at` , `begin from` , `leave`|**Mål ord**: `to` , `arrive` , `land at` , `go` , `going` , `stay``heading`|
|Fras lista|Flyg plats koder – samma lista för både ursprung och mål|Flyg plats koder – samma lista för både ursprung och mål|
|Fras lista|Flyg plats namn – samma lista för både ursprung och mål|Flyg plats koder – samma lista för både ursprung och mål|

Om du förväntar dig att användare använder flyg plats koder och flyg plats namn, än LUIS bör ha fras listor som använder båda typerna av fraser. Flyg plats koderna kan vara vanligare med text som anges i en chattrobot medan flyg plats namn kan vara vanligare med talade konversationer som till exempel ett tal med chattrobot.

Matchande information om funktionerna returneras bara för modeller, inte för fras listor eftersom endast modeller returneras i förutsägelse-JSON.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Biljett boknings märkning i avsikten

När du har skapat enheten för maskin inlärning måste du lägga till exempel yttranden i ett avsikts syfte och namnge den överordnade entiteten och alla underentiteter.

I exempel på biljett boknings exempel kan du namnge exemplet yttranden i avsikt med `TicketBooking` entiteten och eventuella underentiteter i texten.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Etikett exempel yttranden":::

### <a name="example-pizza-ordering-app"></a>Exempel: pizza order app

I ett andra exempel bör du överväga en app för en pizza restaurang, som tar emot pizza-beställningar, inklusive information om typen av pizza som någon beställer. Varje detalj i pizza bör extraheras, om möjligt, för att slutföra bearbetningen av ordern.

Enheten för maskin inlärning i det här exemplet är mer komplex med kapslade underentiteter, fras listor, fördefinierade entiteter och anpassade entiteter.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Pizza ordning för enhets schema":::

I det här exemplet används funktioner på underenhets nivå och underordnad för underentitets nivå. Vilken nivå får vilken typ av fras lista eller modell som en funktion är en viktig del av din enhets design.

Även om underentiteter kan ha många fras listor som funktioner som hjälper till att identifiera entiteten har varje underentitet bara en modell som funktion. I den här [pizza-appen](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)är dessa modeller främst listor.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Pizza order avsikt med etiketterat exempel yttranden":::

Det märkta exemplet yttranden visas på ett sätt för att visa hur entiteterna är kapslade. 


## <a name="best-practices"></a>Bästa praxis

Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* [Utöka](schema-change-prediction-runtime.md) dina program modeller vid förutsägelse körning.
* Se [Lägg till funktioner](luis-how-to-add-features.md) för att lära dig mer om hur du lägger till funktioner i Luis-appen.
