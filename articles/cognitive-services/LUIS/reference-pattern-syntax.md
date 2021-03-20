---
title: Referens för mönster-syntax – LUIS
description: Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade data används av klient programmet.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542197"
---
# <a name="pattern-syntax"></a>Mönstersyntax

Pattern-syntax är en mall för en uttryck. Mallen ska innehålla ord och entiteter som du vill matcha samt de ord och [skiljetecken](luis-reference-application-settings.md#punctuation-normalization) som du vill ignorera. Det är **inte** ett reguljärt uttryck.

> [!CAUTION]
> Mönster inkluderar bara föräldrar med Machine Learning-entiteter, inte underentiteter.

Entiteter i mönster omges av klammerparenteser, `{}` . Mönster kan innehålla entiteter och entiteter med roller. [Mönster. alla](luis-concept-entity-types.md#patternany-entity) är en entitet som endast används i mönster.

Pattern-syntaxen stöder följande syntax:

|Funktion|Syntax|Kapslings nivå|Exempel|
|--|--|--|--|
|entitetsrelation| {} – typografiska hakparenteser|2|Var finns formatet {Entity-Name}?|
|valfri|[]-hakparenteser<BR><BR>Det finns en gräns på 3 på kapslings nivåer för valfri kombination av valfria och gruppering |2|Frågetecknet är valfritt [?]|
|baserat|() – parenteser|2|är (a \| b)|
|eller| \| – lodrätt streck (pipe)<br><br>Det finns en gräns på 2 på de lodräta staplarna (eller) i en grupp |-|Var form ({form-Name-Short} &#x7c; {form-Name-Long} &#x7c; {form-Number})|
|början och/eller slutet av uttryck|^-cirkumflex|-|^ Starta uttryck<br>uttryck är slutförd ^<br>^ Strict literal matchning av hela uttryck med {Number} Entity ^|

## <a name="nesting-syntax-in-patterns"></a>Kapsla syntax i mönster

Den **valfria** syntaxen, med hakparenteser, kan kapslas två nivåer. Exempel: `[[this]is] a new form`. I det här exemplet används följande yttranden:

|Exempel på kapslade valfria uttryck|Förklaring|
|--|--|
|Det här är ett nytt formulär|matchar alla ord i mönstret|
|är ett nytt formulär|matchar yttre valfria ord och icke-valfria ord i mönstret|
|ett nytt formulär|matchar endast obligatoriska ord|

**Grupperingstypen** , med parenteser, kan kapslas två nivåer. Exempel: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Med den här funktionen kan alla de tre entiteterna matchas.

Om Entity1 är en plats med roller som t. ex. ursprung (Seattle) och destination (Cairo) och entitet 2 är ett känt byggnads namn från en List-entitet (RedWest-C) mappar följande yttranden till det här mönstret:

|Exempel på kapslad gruppering uttryck|Förklaring|
|--|--|
|RedWest-C|matchar Outer Grouping-entitet|
|Seattle|matchar en av de inre gruppiska enheterna|
|Cairo|matchar en av de inre gruppiska enheterna|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Kapslade gränser för grupper med valfri syntax

En kombination av **gruppering** med **valfri** syntax har en gräns på 3 kapslings nivåer.

|Tillåts|Exempel|
|--|--|
|Ja|([(TEST1 &#x7c; TEST2)] &#x7c; test3)|
|Inga|([([TEST1] &#x7c; TEST2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Kapslade gränser för grupper med eller-ing-syntax

En kombination av **gruppering** med **eller-ing-** syntaxen har en gräns på 2 lodräta staplar.

|Tillåts|Exempel|
|--|--|
|Ja|(TEST1 &#x7c; TEST2 &#x7c; (test3 &#x7c; test4))|
|Inga|(TEST1 &#x7c; TEST2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntax för att lägga till en entitet i en mönster mall
Om du vill lägga till en entitet i mönster mal len omger du entitetsnamnet med klammerparenteser, till exempel `Who does {Employee} manage?` .

|Mönster med entitet|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntax för att lägga till en entitet och roll i en mönster mall
En Entity-roll anges som `{entity:role}` med entitetsnamnet följt av ett kolon, sedan roll namnet. Om du vill lägga till en entitet med en roll i mönster mal len omger du entitetsnamnet och roll namnet med klammerparenteser, till exempel `Book a ticket from {Location:Origin} to {Location:Destination}` .

|Mönster med enhets roller|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntax för att lägga till ett mönster. mall för mönster
Mönstret. med valfri entitet kan du lägga till en entitet med varierande längd på mönstret. Så länge mönster mal len följs, kan mönstret. det kan vara vilken längd som helst.

Om du vill lägga till ett **mönster.** en entitet i mönster mal len omger du mönstret. alla entiteter med klammerparenteser, till exempel `How much does {Booktitle} cost and what format is it available in?` .

|Mönster med mönster. valfri entitet|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Bok titlar i mönstret|
|--|
|Hur mycket kan **stjäla den här bokens** kostnad och vilket format är det?|
|Hur **mycket kostar det** och vilket format är det?|
|Hur mycket är den infallande **incidenten av hund under natt tiden** och vilket format är den tillgänglig i?|

Texten i bok rubriken är inte förvirrande för LUIS eftersom LUIS vet var bok rubriken slutar, baserat på mönstret. alla entiteter.

## <a name="explicit-lists"></a>Explicita listor

skapa en [explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via redigerings-API: n för att tillåta undantaget när:

* Ditt mönster innehåller ett [mönster. alla](luis-concept-entity-types.md#patternany-entity)
* Och den mönstermatchningssyntax ger möjlighet till en felaktig enhets extrahering baserat på uttryck.

Anta till exempel att du har ett mönster som innehåller både valfri syntax, `[]` och Entity, `{}` kombinerat på ett sätt att extrahera data på ett felaktigt sätt.

Överväg mönstret [find] e-mail om {subject} [från {person}].

I följande yttranden extraheras **ämnes** -och **person** entiteten korrekt och felaktigt:

|Yttrande|Entitet|Korrekt extrahering|
|--|--|:--:|
|e-postmeddelande om hundar från Christer|ämne = hundar<br>person = Christer|✔|
|e-postmeddelande om människan från La Mancha|ämne = man<br>person = La Mancha|X|

I den föregående tabellen ska ämnet vara `the man from La Mancha` (en bok titel) men eftersom ämnet innehåller det valfria ordet `from` är rubriken felaktigt förväntad.

Om du vill åtgärda det här undantaget till mönstret lägger du till `the man from la mancha` som en explicit lista matchning för entiteten {subject} med hjälp av [redigerings-API: et för en explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntax för att markera valfri text i en mall uttryck
Markera valfri text i uttryck med det reguljära uttrycket hak paren tes syntax, `[]` . Den valfria texten kan kapsla hakparenteser upp till två hakparenteser.

|Mönster med valfri text|Innebörd|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` och `from {person}` är valfria|
|' Kan du hjälpa mig [?]|Interpunktion-tecknet är valfritt|

Skiljetecken ( `?` , `!` , `.` ) ska ignoreras och du måste ignorera dem med hjälp av klammer i mönster.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om mönster:

* [Lägga till mönster](luis-how-to-model-intent-pattern.md)
* [Hur du lägger till mönster. valfri entitet](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Mönster koncept](luis-concept-patterns.md)

Förstå hur [sentiment](luis-reference-prebuilt-sentiment.md) returneras i. JSON-svaret.