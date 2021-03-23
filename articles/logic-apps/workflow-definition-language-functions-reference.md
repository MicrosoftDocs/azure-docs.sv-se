---
title: Referens guide för funktioner i uttryck
description: Referens guide till funktioner i uttryck för Azure Logic Apps och energi automatisering
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/12/2021
ms.openlocfilehash: 1414a7b0f17918caa16ccf854d70ea199fb42a47
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870202"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referens guide för att använda funktioner i uttryck för Azure Logic Apps och energi automatisering

För arbets flödes definitioner i [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [energi automatisering](/flow/getting-started)får vissa [uttryck](../logic-apps/logic-apps-workflow-definition-language.md#expressions) sina värden från körnings åtgärder som kanske inte redan finns när arbets flödet börjar köras. Om du vill referera till dessa värden eller bearbeta värdena i dessa uttryck kan du använda *funktioner* som tillhandahålls av [språket för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Den här referens sidan gäller för både Azure Logic Apps och Energis par, men visas i Azure Logic Apps-dokumentationen. Även om den här sidan specifikt refererar till Logic Apps, fungerar dessa funktioner för både flöden och Logic Apps. Mer information om funktioner och uttryck i energi automatisering finns i [använda uttryck i villkor](/flow/use-expressions-in-conditions).

Du kan till exempel beräkna värden med hjälp av matematiska funktioner, till exempel funktionen [Lägg till ()](../logic-apps/workflow-definition-language-functions-reference.md#add) , när du vill beräkna summan från heltal eller flyttal. Här följer några exempel på uppgifter som du kan utföra med Functions:

| Uppgift | Syntax för funktion | Resultat |
| ---- | --------------- | ------ |
| Returnera en sträng med gemener. | toLower (<*text*>) <p>Till exempel: toLower (' hello ') | Hello |
| Returnera en globalt unik identifierare (GUID). | GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Granska följande tabeller för att hitta funktioner [baserat på deras generella syfte](#ordered-by-purpose). Mer detaljerad information om varje funktion finns i [alfabetisk lista](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funktioner i uttryck

För att visa hur du använder en funktion i ett uttryck visar det här exemplet hur du kan hämta värdet från `customerName` parametern och tilldela värdet till `accountName` egenskapen med hjälp av funktionen [Parameters ()](#parameters) i ett uttryck:

```json
"accountName": "@parameters('customerName')"
```

Här följer några andra allmänna sätt som du kan använda funktioner i uttryck:

| Uppgift | Function-syntax i ett uttryck |
| ---- | -------------------------------- |
| Utför arbetet med ett objekt genom att skicka det här objektet till en funktion. | " \@ < *functionname*> (<*objekt*>)" |
| 1. Hämta *parameterName* värde genom att använda den kapslade `parameters()` funktionen. </br>2. utför arbetet med resultatet genom att skicka det värdet till *functionname*. | " \@ < *functionname*> (parametrar (' <*parameterName*> '))" |
| 1. Hämta resultatet från den kapslade inre funktionens *functionname*. </br>2. skicka resultatet till den yttre funktionen *functionName2*. | " \@ < *functionName2*> (<*functionname*> (<*item*>))" |
| 1. Hämta resultatet från *functionname*. </br>2. under förutsättning att resultatet är ett objekt med egenskapen *PropertyName*, Hämta egenskapens värde. | " \@ < *functionname*> (<*objekt*>). <*PropertyName*>" |
|||

Funktionen kan till exempel `concat()` ta två eller fler sträng värden som parametrar. Den här funktionen kombinerar dessa strängar till en sträng. Du kan antingen skicka in sträng litteraler, till exempel "Sophia" och "Owen" så att du får en kombinerad sträng, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Du kan också hämta sträng värden från parametrar. I det här exemplet används `parameters()` funktionen i varje `concat()` parameter och `firstName` `lastName` parametrarna och. Sedan skickar du de resulterande strängarna till `concat()` funktionen så att du får en kombinerad sträng, till exempel "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Båda fallen tilldelar båda exemplen resultatet till `customerName` egenskapen.

Här följer några andra anmärkningar om funktioner i uttryck:

* Funktions parametrarna utvärderas från vänster till höger.

* I syntaxen för parameter definitioner är ett frågetecken (?) som visas efter en parameter att parametern är valfri. Se till exempel [getFutureTime ()](#getFutureTime).

I följande avsnitt organiseras funktioner baserat på deras generella syfte, eller så kan du bläddra bland dessa funktioner i [alfabetisk ordning](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Strängfunktioner

Om du vill arbeta med strängar kan du använda dessa sträng funktioner och även vissa [samlings funktioner](#collection-functions). Sträng funktioner fungerar bara för strängar.

| Funktionen String | Uppgift |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinera två eller fler strängar och returnera den kombinerade strängen. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Kontrol lera om en sträng slutar med den angivna under strängen. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Returnera ett tal som en sträng baserat på det angivna formatet |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Returnera start positionen för en under sträng. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Returnera start positionen för den sista förekomsten av en under sträng. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [bytt](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersätt en under sträng med den angivna strängen och returnera den uppdaterade strängen. |
| [del](../logic-apps/workflow-definition-language-functions-reference.md#split) | Returnera en matris som innehåller del strängar, avgränsade med kommatecken, från en större sträng baserat på ett angivet avgränsnings tecken i den ursprungliga strängen. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontrol lera om en sträng börjar med en speciell under sträng. |
| [under sträng](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Returnera tecken från en sträng, från angiven position. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Returnera en sträng med gemener. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Returnera en sträng i versal format. |
| [reducera](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Ta bort inledande och avslutande blank steg från en sträng och returnera den uppdaterade strängen. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Samlingsfunktioner

Om du vill arbeta med samlingar, vanligt vis matriser, strängar och ibland kan du använda de här samlings funktionerna.

| Samlings funktion | Uppgift |
| ------------------- | ---- |
| [ingår](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontrol lera om en samling har ett angivet objekt. |
| [tomt](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Kontrol lera om en samling är tom. |
| [förstagångskörningen](../logic-apps/workflow-definition-language-functions-reference.md#first) | Returnera det första objektet från en samling. |
| [överlappning](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Returnera en samling som *endast* innehåller gemensamma objekt i de angivna samlingarna. |
| [konfigurationsobjektet](../logic-apps/workflow-definition-language-functions-reference.md#item) | När du är i en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [ansluta](../logic-apps/workflow-definition-language-functions-reference.md#join) | Returnera en sträng som innehåller *alla* objekt från en matris, separerade med det angivna specialtecknet. |
| [pågå](../logic-apps/workflow-definition-language-functions-reference.md#last) | Returnera det sista objektet från en samling. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Ignorera](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Ta bort objekt från början av en samling och returnera *alla andra* objekt. |
| [gå](../logic-apps/workflow-definition-language-functions-reference.md#take) | Returnera objekt från början av en samling. |
| [Union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Returnera en samling som innehåller *alla* objekt från de angivna samlingarna. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funktioner för logisk jämförelse

Om du vill arbeta med villkor, jämföra värden och resultat eller utvärdera olika typer av logik kan du använda dessa logiska jämförelse funktioner. Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Om du använder logiska funktioner eller villkor för att jämföra värden, konverteras NULL-värden till tomma sträng `""` värden (). Beteendet för villkor skiljer sig när du jämför med en tom sträng i stället för ett null-värde. Mer information finns i [funktionen String ()](#string). 

| Funktion för logisk jämförelse | Uppgift |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Kontrol lera om alla uttryck är sanna. |
| [är lika med](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Kontrol lera om båda värdena är likvärdiga. |
| [större än](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Kontrol lera om det första värdet är större än det andra värdet. |
| [större än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Kontrol lera om det första värdet är större än eller lika med det andra värdet. |
| [eventuella](../logic-apps/workflow-definition-language-functions-reference.md#if) | Kontrol lera om ett uttryck är sant eller falskt. Returnera ett angivet värde baserat på resultatet. |
| [minskad](../logic-apps/workflow-definition-language-functions-reference.md#less) | Kontrol lera om det första värdet är mindre än det andra värdet. |
| [mindre än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Kontrol lera om det första värdet är mindre än eller lika med det andra värdet. |
| [Ogiltigt](../logic-apps/workflow-definition-language-functions-reference.md#not) | Kontrol lera om ett uttryck är falskt. |
| [eller](../logic-apps/workflow-definition-language-functions-reference.md#or) | Kontrol lera om minst ett uttryck är sant. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverteringsfunktioner

Om du vill ändra ett värdes typ eller format kan du använda dessa konverterings funktioner. Du kan till exempel ändra ett värde från ett booleskt värde till ett heltal. Mer information om hur Logic Apps hanterar innehålls typer under konverteringen finns i [Hantera innehålls typer](../logic-apps/logic-apps-content-type.md). Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Azure Logic Apps konverterar automatiskt värden mellan vissa data typer, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

| Konverterings funktion | Uppgift |
| ------------------- | ---- |
| [matris](../logic-apps/workflow-definition-language-functions-reference.md#array) | Returnera en matris från en angiven Indatatyp. För flera indata, se [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Returnera Base64-kodad version för en sträng. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Returnera den binära versionen för en Base64-kodad sträng. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Returnera sträng versionen för en Base64-kodad sträng. |
| [binär](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Returnera den binära versionen för ett indatavärde. |
| [boolesk](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Returnera den booleska versionen för ett indatavärde. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Returnera en matris från flera indata. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Returnera data-URI: n för ett indatavärde. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Returnera sträng versionen för en data-URI. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Returnera sträng versionen för en Base64-kodad sträng. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Returnera den binära versionen för en data-URI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Returnera en sträng som ersätter escape-tecken med avkodade versioner. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecken med escape-tecken. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Returnera ett flytt ALS nummer för ett indatavärde. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Returnera heltals versionen för en sträng. |
| [utgör](../logic-apps/workflow-definition-language-functions-reference.md#json) | Returnera värdet för JavaScript Object Notation (JSON) av typen eller objektet för en sträng eller XML. |
| [sträng](../logic-apps/workflow-definition-language-functions-reference.md#string) | Returnera sträng versionen för ett indatavärde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Returnera den URI-kodade versionen för ett indatavärde genom att ersätta URL-osäkra tecken med escape-tecken. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Returnera sträng versionen för en URI-kodad sträng. |
| [fil](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Returnera XML-versionen för en sträng. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Implicita data typs konverteringar

Azure Logic Apps automatiskt eller implicit konvertera mellan vissa data typer, så du behöver inte konvertera de här typerna manuellt. Om du till exempel använder värden som inte är strängar där strängar förväntas som indata, konverterar Logic Apps automatiskt icke-sträng värden till strängar.

Anta till exempel att en utlösare returnerar ett numeriskt värde som utdata:

`triggerBody()?['123']`

Om du använder dessa numeriska utdata där indata förväntas, till exempel en URL, konverterar Logic Apps automatiskt värdet till en sträng med hjälp av klammerparenteser ()- `{}` notation:

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Base64-kodning och avkodning

Logic Apps automatiskt eller implicit utföra base64-kodning eller avkodning, så du behöver inte utföra dessa åtgärder manuellt genom att använda motsvarande uttryck:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Om du lägger till dessa uttryck manuellt i din Logi Kap par, till exempel genom att använda uttrycks redigeraren, navigerar du bort från Logic App Designer och återgår till designern, visar designern bara parameter värden. Uttrycken bevaras endast i kodvyn om du inte redigerar parameter värden. Annars tar Logic Apps bort uttrycken från kodvyn, så att endast parameter värden lämnas kvar. Det här beteendet påverkar inte kodning eller avkodning, bara om uttrycken visas.

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematiska funktioner

Du kan använda dessa matematiska funktioner för att arbeta med heltal och flyttal.
Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Matematik funktion | Uppgift |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Returnera resultatet från att dividera två tal. |
| [bekräftat](../logic-apps/workflow-definition-language-functions-reference.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [rest](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Returnera resten från att dividera två tal. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Returnera produkten från att multiplicera två tal. |
| [slump](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [intervall](../logic-apps/workflow-definition-language-functions-reference.md#range) | Returnera en heltals mat ris som börjar från ett angivet heltal. |
| [Build](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Returnera resultatet från att subtrahera det andra talet från det första talet. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- och tidsfunktioner

Du kan använda dessa datum-och tids funktioner för att arbeta med datum och tider.
Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funktionen datum/tid | Uppgift |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Lägg till ett antal dagar i en tidstämpel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Lägg till ett antal timmar i en tidsstämpel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Lägg till ett antal minuter i en tidsstämpel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Lägg till ett antal sekunder i en tidsstämpel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Lägg till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertera en tidsstämpel från Universal Time Coordinated (UTC) till mål tids zonen. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konvertera en tidsstämpel från käll tids zonen till mål tids zonen. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertera en tidsstämpel från käll tids zonen till koordinerad Universal-tid (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Returnera dag i månads komponenten från en tidsstämpel. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Returnera dag i vecko komponenten från en tidsstämpel. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Returnera dagen på års komponenten från en tidsstämpel. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Returnera datumet från en tidstämpel. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Returnera den aktuella tidsstämpeln och de angivna tidsenheterna. Se även [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Returnera början på dagen för en tidsstämpel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Returnera början på timmen för en tidsstämpel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Returnera början på månaden för en tidsstämpel. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrahera ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Returnera `ticks` egenskap svärdet för en angiven tidstämpel. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Returnera den aktuella tidstämpeln som en sträng. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Arbets flödes funktioner

Dessa arbets flödes funktioner kan hjälpa dig att:

* Hämta information om en arbets flödes instans vid körning.
* Arbeta med de indata som används för att instansiera Logic Apps eller flöden.
* Referera till utdata från utlösare och åtgärder.

Du kan till exempel referera till utdata från en åtgärd och använda dessa data i en senare åtgärd.
Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Arbetsflödesfunktion | Uppgift |
| ----------------- | ---- |
| [tgärd](../logic-apps/workflow-definition-language-functions-reference.md#action) | Returnera den aktuella åtgärdens utdata vid körning eller värden från andra JSON-namn-och värdepar. Se även [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Returnera en åtgärds `body` utdata vid körning. Se även [brödtext](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Returnera en åtgärds utdata vid körning. Se [utdata](../logic-apps/workflow-definition-language-functions-reference.md#outputs) och [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Action](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Returnera en åtgärds utdata vid körning eller värden från andra JSON-namn och värde-par. Se även [åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [brödtext](#body) | Returnera en åtgärds `body` utdata vid körning. Se även [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Skapa en matris med de värden som matchar ett nyckel namn i utdata för *formulär data* eller *formulär-kodade* åtgärder. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Returnera ett enskilt värde som matchar ett nyckel namn i en åtgärds *formulär-data* eller *formulär-kodade utdata*. |
| [konfigurationsobjektet](../logic-apps/workflow-definition-language-functions-reference.md#item) | När du är i en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [objekt](../logic-apps/workflow-definition-language-functions-reference.md#items) | Returnera det aktuella objektet från den angivna slingan i en förgrunds-eller until-slinga.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Returnera indexvärdet för den aktuella iterationen i en-slinga. Du kan använda den här funktionen inuti kapslade tills loopar. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Returnera återanrops-URL: en som anropar en utlösare eller åtgärd. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Returnera bröd texten för en viss del i en åtgärds utdata som har flera delar. |
| [utdata](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Returnera en åtgärds utdata vid körning. |
| [komponentparametrar](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Returnera värdet för en parameter som beskrivs i arbets flödes definitionen. |
| [medför](../logic-apps/workflow-definition-language-functions-reference.md#result) | Returnera indata och utdata från de översta åtgärderna i den angivna åtgärds omfattningen, till exempel, `For_each` `Until` och `Scope` . |
| [utlösare](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Returnera en utlösare utdata vid körning eller från andra JSON-namn-och-värde-par. Se även [triggerOutputs](#triggerOutputs) och [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Returnera en utlösare `body` utdata vid körning. Se [utlösare](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Returnera ett enskilt värde som matchar ett nyckel namn i utlösare för *formulär data* eller *formulär-kodade* utlösare. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Returnera bröd texten för en speciell del i en utlösare multipart-utdata. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Skapa en matris vars värden matchar ett nyckel namn i utlösare för *formulär data* eller *formulär-kodade* utlösare. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Returnera en utlösare utdata vid körning eller värden från andra JSON-namn och värde-par. Se [utlösare](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [användarvariabler](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Returnera värdet för en angiven variabel. |
| [arbets flöde](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Returnera all information om själva arbets flödet under körnings tiden. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-parsing-funktioner

Om du vill arbeta med URI: er (Uniform Resource Identifier) och hämta olika egenskaps värden för dessa URI: er kan du använda dessa URI-parsningsfel.
Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| URI-parsnings funktion | Uppgift |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Returnera `host` värdet för en URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Returnera `path` värdet för en URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Returnera `path` värdena och `query` för en URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Returnera `port` värdet för en URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Returnera `query` värdet för en URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Returnera `scheme` värdet för en URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulerings funktioner: JSON & XML

Du kan använda dessa modifierings funktioner för att arbeta med JSON-objekt och XML-noder.
Fullständig referens för varje funktion finns i [alfabetisk lista](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulations funktion | Uppgift |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Lägg till en egenskap och dess värde, eller namn-värde-par, till ett JSON-objekt och returnera det uppdaterade objektet. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Returnera det första värdet som inte är null från en eller flera parametrar. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Ta bort en egenskap från ett JSON-objekt och returnera det uppdaterade objektet. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ange värdet för ett JSON-objekts egenskap och returnera det uppdaterade objektet. |
| [XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Kontrol lera XML för noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera matchande noder eller värden. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alla funktioner – alfabetisk lista

I det här avsnittet visas alla tillgängliga funktioner i alfabetisk ordning.

<a name="action"></a>

### <a name="action"></a>åtgärd

Returnera den *aktuella* åtgärdens utdata vid körning eller värden från andra JSON-namn-och värdepar, som du kan tilldela till ett uttryck.
Som standard refererar den här funktionen till hela Action-objektet, men du kan också ange en egenskap vars värde du vill ha.
Se även [åtgärder ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Du kan bara använda `action()` funktionen på följande platser:

* `unsubscribe`Egenskapen för en webhook-åtgärd så att du kan komma åt resultatet från den ursprungliga `subscribe` begäran
* `trackedProperties`Egenskapen för en åtgärd
* `do-until`Loop-villkoret för en åtgärd

```
action()
action().outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*immaterialrätt*> | Inga | Sträng | Namnet på åtgärds objektets egenskap vars värde du vill använda: **namn**, **StartTime** **, slut tid,** **indata**, **utdata**, **status**, **kod**, **trackingId** och **clientTrackingId**. I Azure Portal kan du hitta dessa egenskaper genom att granska information om en speciell körnings historik. Mer information finns i [REST API-arbets flödes körnings åtgärder](/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd – utdata*> | Sträng | Utdata från aktuell åtgärd eller egenskap |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Returnera en åtgärds `body` utdata vid körning.
Snabb kort för `actions('<actionName>').outputs.body` .
Se [Body ()](#body) och [åtgärder ()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd-brödtext – utdata*> | Sträng | `body`Utdata från den angivna åtgärden |
||||

*Exempel*

I det här exemplet hämtas `body` utdata från Twitter- `Get user` åtgärden:

```
actionBody('Get_user')
```

Och returnerar följande resultat:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Returnera en åtgärds utdata vid körning.  och är i korthet för `actions('<actionName>').outputs` . Se [åtgärder ()](#actions). `actionOutputs()`Funktionen matchar `outputs()` i Logic App Designer, så Överväg att använda [utdata ()](#outputs)i stället för `actionOutputs()` . Även om båda funktionerna fungerar på samma sätt, `outputs()` är föredra.

```
actionOutputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*utdataparametrar*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

I det här exemplet hämtas utdata från Twitter-åtgärden `Get user` :

```
actionOutputs('Get_user')
```

Och returnerar följande resultat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>åtgärder

Returnera en åtgärds utdata vid körning eller värden från andra JSON-namn och värde-par som du kan tilldela till ett uttryck. Som standard refererar funktionen till hela Action-objektet, men du kan också ange en egenskap vars värde du vill ha.
För stenografiska versioner, se [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)och [Body ()](#body).
För den aktuella åtgärden, se [Action ()](#action).

> [!TIP]
> `actions()`Funktionen returnerar utdata som en sträng. Om du behöver arbeta med ett returnerat värde som ett JSON-objekt måste du först konvertera strängvärdet. Du kan omvandla strängvärdet till ett JSON-objekt med hjälp av [åtgärden parsa JSON](logic-apps-perform-data-operations.md#parse-json-action).

> [!NOTE]
> Tidigare kunde du använda `actions()` funktionen eller `conditions` elementet när du anger att en åtgärd kördes baserat på utdata från en annan åtgärd. Men för att deklarera explicita beroenden mellan åtgärder måste du nu använda den beroende åtgärdens `runAfter` egenskap.
> Om du vill veta mer om `runAfter` egenskapen läser du [fånga in och hantera problem med egenskapen runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på det åtgärds objekt vars utdata du vill ha  |
| <*immaterialrätt*> | Inga | Sträng | Namnet på åtgärds objektets egenskap vars värde du vill använda: **namn**, **StartTime** **, slut tid,** **indata**, **utdata**, **status**, **kod**, **trackingId** och **clientTrackingId**. I Azure Portal kan du hitta dessa egenskaper genom att granska information om en speciell körnings historik. Mer information finns i [REST API-arbets flödes körnings åtgärder](/rest/api/logic/workflowrunactions/get). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd – utdata*> | Sträng | Utdata från angiven åtgärd eller egenskap |
||||

*Exempel*

Det här exemplet hämtar `status` egenskap svärdet från Twitter-åtgärden `Get user` vid körning:

```
actions('Get_user').outputs.body.status
```

Och returnerar följande resultat: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Returnera resultatet från att lägga till två tal.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*summand_1*> <*summand_2*> | Ja | Heltal, float eller Mixed | De siffror som ska läggas till |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*resultat-sum*> | Heltal eller flyttal | Resultatet av att lägga till de angivna talen |
||||

*Exempel*

I det här exemplet läggs de angivna talen till:

```
add(1, 1.5)
```

Och returnerar följande resultat: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Lägg till ett antal dagar i en tidstämpel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*antalet*> | Ja | Integer | Det positiva eller negativa antalet dagar som ska läggas till |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet dagar  |
||||

*Exempel 1*

I det här exemplet läggs 10 dagar till i den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', 10)
```

Och returnerar följande resultat: `"2018-03-25T00:00:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar följande resultat: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Lägg till ett antal timmar i en tidsstämpel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*timmarna*> | Ja | Integer | Det positiva eller negativa antalet timmar som ska läggas till |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet timmar  |
||||

*Exempel 1*

I det här exemplet läggs 10 timmar till i den angivna tidsstämpeln:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Och returnerar följande resultat: "" 2018-03-15T10:00:00.0000000 Z "

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar följande resultat: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Lägg till ett antal minuter i en tidsstämpel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*fördröjning*> | Ja | Integer | Det positiva eller negativa antalet minuter att lägga till |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet minuter |
||||

*Exempel 1*

I det här exemplet läggs 10 minuter till i den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Och returnerar följande resultat: `"2018-03-15T00:20:00.0000000Z"`

*Exempel 2*

I det här exemplet subtraheras fem minuter från den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Och returnerar följande resultat: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Lägg till en egenskap och dess värde, eller namn-värde-par, till ett JSON-objekt och returnera det uppdaterade objektet. Om egenskapen redan finns under körningen Miss lyckas funktionen och genererar ett fel.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*immaterialrätt*> | Ja | Sträng | Namnet på egenskapen som ska läggas till |
| <*värde*> | Ja | Valfri | Egenskapens värde |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat – objekt*> | Objekt | Det uppdaterade JSON-objektet med den angivna egenskapen |
||||

Om du vill lägga till en överordnad egenskap i en befintlig egenskap använder du `setProperty()` funktionen, inte `addProperty()` funktionen. Annars returnerar funktionen bara det underordnade objektet som utdata.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen där du vill lägga till den underordnade egenskapen |
| <*underordnad egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska läggas till |
| <*värde*> | Ja | Valfri | Värdet som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat – objekt*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

I det här exemplet läggs `middleName` egenskapen till i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON ()](#json) . Objektet innehåller redan- `firstName` och- `surName` egenskaperna. Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exempel 2*

Det här exemplet lägger till den `middleName` underordnade egenskapen till den befintliga `customerName` egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av [JSON ()-](#json) funktionen. Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne'))
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Lägg till ett antal sekunder i en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*sekunder*> | Ja | Integer | Det positiva eller negativa antalet sekunder som ska läggas till |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet sekunder  |
||||

*Exempel 1*

I det här exemplet läggs 10 sekunder till i den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Och returnerar följande resultat: `"2018-03-15T00:00:10.0000000Z"`

*Exempel 2*

I det här exemplet subtraheras fem sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Och returnerar följande resultat: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Lägg till ett antal tidsenheter i en tidsstämpel.
Se även [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *Interval*: "sekund", "minut", "timme", "dag", "vecka", "månad", "år" |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet tidsenheter  |
||||

*Exempel 1*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Och returnerar följande resultat: `"2018-01-02T00:00:00.0000000Z"`

*Exempel 2*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet med det valfria "D"-formatet: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>och

Kontrol lera om alla uttryck är sanna.
Returnera true när alla uttryck är sanna, eller returnera falskt när minst ett uttryck är falskt.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*Uttryck2*>,... | Ja | Boolesk | De uttryck som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| sant eller falskt | Boolesk | Returnera true när alla uttryck är sanna. Returnera falskt om minst ett uttryck är falskt. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna booleska värdena är true:

```
and(true, true)
and(false, true)
and(false, false)
```

Och returnerar följande resultat:

* Första exemplet: båda uttrycken är sanna, så returnerar `true` .
* Andra exempel: ett uttryck är falskt, så returnerar `false` .
* Tredje exempel: båda uttrycken är falska, så returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om de angivna uttrycken är sanna:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Första exemplet: båda uttrycken är sanna, så returnerar `true` .
* Andra exempel: ett uttryck är falskt, så returnerar `false` .
* Tredje exempel: båda uttrycken är falska, så returnerar `false` .

<a name="array"></a>

### <a name="array"></a>matris

Returnera en matris från en angiven Indatatyp.
För flera indata, se [createArray ()](#createArray).

```
array('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen för att skapa en matris |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*värde*>] | Matris | En matris som innehåller en angiven Indatatyp |
||||

*Exempel*

I det här exemplet skapas en matris från strängen "Hello":

```
array('hello')
```

Och returnerar följande resultat: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Returnera Base64-kodad version för en sträng.

> [!NOTE]
> Azure Logic Apps utför automatiskt base64-kodning och avkodning, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

```
base64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Indatasträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Base64-sträng*> | Sträng | Base64-kodad version för Indatasträngen |
||||

*Exempel*

I det här exemplet konverteras "Hej"-strängen till en Base64-kodad sträng:

```
base64('hello')
```

Och returnerar följande resultat: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Returnera den binära versionen för en Base64-kodad sträng.

> [!NOTE]
> Azure Logic Apps utför automatiskt base64-kodning och avkodning, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

```
base64ToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den base64-kodade sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Binary-för-base64-sträng*> | Sträng | Den binära versionen för den base64-kodade strängen |
||||

*Exempel*

I det här exemplet konverteras base64-kodade strängen "aGVsbG8 =" till en binär sträng:

```
base64ToBinary('aGVsbG8=')
```

Och returnerar följande resultat:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Returnera sträng versionen för en Base64-kodad sträng, och avkoda den base64-strängen på ett effektivt sätt. Använd den här funktionen i stället för [decodeBase64 ()](#decodeBase64), som är föråldrad.

> [!NOTE]
> Azure Logic Apps utför automatiskt base64-kodning och avkodning, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

```
base64ToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den base64-kodade strängen att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-base64-sträng*> | Sträng | Sträng versionen för en Base64-kodad sträng |
||||

*Exempel*

I det här exemplet konverteras base64-kodade strängen "aGVsbG8 =" till bara en sträng:

```
base64ToString('aGVsbG8=')
```

Och returnerar följande resultat: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Returnera den binära versionen för en sträng.

```
binary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Binary-för-indatamängd-värde*> | Sträng | Den binära versionen för den angivna strängen |
||||

*Exempel*

I det här exemplet konverteras "Hej"-strängen till en binär sträng:

```
binary('hello')
```

Och returnerar följande resultat:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Returnera en åtgärds `body` utdata vid körning.
Snabb kort för `actions('<actionName>').outputs.body` .
Se [actionBody ()](#actionBody) och [åtgärder ()](#actions).

```
body('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*åtgärd-brödtext – utdata*> | Sträng | `body`Utdata från den angivna åtgärden |
||||

*Exempel*

I det här exemplet hämtas `body` utdata från `Get user` Twitter-åtgärden:

```
body('Get_user')
```

Och returnerar följande resultat:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>boolesk

Returnera den booleska versionen av ett värde.

```
bool(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Valfri | Värdet som ska konverteras till Boolean. |
|||||

Om du använder `bool()` med ett objekt måste värdet för objektet vara en sträng eller ett heltal som kan konverteras till Boolean.

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| `true` eller `false` | Boolesk | Den booleska versionen av det angivna värdet. |
||||

*Utdata*

I de här exemplen visas de olika typerna av ininformation som stöds för `bool()` :

| Indatavärde | Typ | Returvärde |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Integer | `true` |
| `bool(0)` | Integer    | `false` |
| `bool(-1)` | Integer | `true` |
| `bool('true')` | Sträng | `true` |
| `bool('false')` | Sträng | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Returnera det första värdet som inte är null från en eller flera parametrar.
Tomma strängar, tomma matriser och tomma objekt är inte null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object_1*> <*object_2*>,... | Ja | Alla, kan blanda typer | Ett eller flera objekt att söka efter null |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*första-icke-null-objekt*> | Valfri | Det första objektet eller värdet som inte är null. Om alla parametrar är null returnerar den här funktionen null. |
||||

*Exempel*

I de här exemplen returneras det första värdet som inte är null från de angivna värdena, eller null när alla värden är null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `"hello"`
* Tredje exempel: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Kombinera två eller fler strängar och returnera den kombinerade strängen.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Text2*>,... | Ja | Sträng | Minst två strängar att kombinera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sträng | Strängen som skapats från kombinerade Indatasträngen |
||||

*Exempel*

I det här exemplet kombineras strängarna "Hello" och "World":

```
concat('Hello', 'World')
```

Och returnerar följande resultat: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>innehåller

Kontrol lera om en samling har ett angivet objekt.
Returnera true när objektet hittas eller returnera falskt när det inte går att hitta.
Den här funktionen är Skift läges känslig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Mer specifikt fungerar den här funktionen på dessa samlings typer:

* En *sträng* för att hitta en under *sträng*
* En *matris* för att hitta ett *värde*
* En *ord lista* för att hitta en *nyckel*

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng, matris eller ord lista | Den samling som ska kontrol leras |
| <*värde*> | Ja | Sträng, matris eller ord lista | Objektet att söka efter |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när objektet hittas. Returnera falskt när det inte går att hitta. |
||||

*Exempel 1*

I det här exemplet kontrol leras strängen "Hello World" för under strängen "världen" och returnerar true:

```
contains('hello world', 'world')
```

*Exempel 2*

I det här exemplet kontrol leras strängen "Hello World" för under strängen "universum" och returnerar falskt:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konvertera en tidsstämpel från Universal Time Coordinated (UTC) till mål tids zonen.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på mål tids zonen. För tids zons namn, se [index värden för Microsoft Time Zone](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tids zonens namn. |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad – tidsstämpel*> | Sträng | Tidsstämpeln konverteras till mål tids zonen |
||||

*Exempel 1*

I det här exemplet konverteras en tidstämpel till angiven tidszon:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Och returnerar följande resultat: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

I det här exemplet konverteras en tidstämpel till angiven tidszon och format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Och returnerar följande resultat: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konvertera en tidsstämpel från käll tids zonen till mål tids zonen.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | Sträng | Namnet på käll tids zonen. För tids zons namn, se [index värden för Microsoft Time Zone](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tids zonens namn. |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på mål tids zonen. För tids zons namn, se [index värden för Microsoft Time Zone](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tids zonens namn. |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad – tidsstämpel*> | Sträng | Tidsstämpeln konverteras till mål tids zonen |
||||

*Exempel 1*

I det här exemplet konverteras käll tids zonen till mål tids zonen:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Och returnerar följande resultat: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

I det här exemplet konverteras en tidszon till angiven tidszon och format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Och returnerar följande resultat: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konvertera en tidsstämpel från käll tids zonen till koordinerad Universal-tid (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | Sträng | Namnet på käll tids zonen. För tids zons namn, se [index värden för Microsoft Time Zone](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), men du kanske måste ta bort eventuella skiljetecken från tids zonens namn. |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad – tidsstämpel*> | Sträng | Tidsstämpeln konverterad till UTC |
||||

*Exempel 1*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Och returnerar följande resultat: `"2018-01-01T08:00:00.0000000Z"`

*Exempel 2*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Och returnerar följande resultat: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Returnera en matris från flera indata.
För enstaka lagringsmatriser, se [matris ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object1*> <*object2*>... | Ja | Alla, men inte blandade | Minst två objekt för att skapa matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*object1*> <*object2*>,...] | Matris | Matrisen som skapas från alla inmatade objekt |
||||

*Exempel*

I det här exemplet skapas en matris med följande indata:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Och returnerar följande resultat: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Returnera en URI (data Uniform Resource Identifier) för en sträng.

```
dataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*data-URI*> | Sträng | Data-URI för Indatasträngen |
||||

*Exempel*

I det här exemplet skapas en data-URI för "Hej"-strängen:

```
dataUri('hello')
```

Och returnerar följande resultat: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Returnera den binära versionen för en URI (data Uniform Resource Identifier).
Använd den här funktionen i stället för [decodeDataUri ()](#decodeDataUri).
Även om båda funktionerna fungerar på samma sätt, `dataUriBinary()` är föredra.

```
dataUriToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den data-URI som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Binary-för-data-URI*> | Sträng | Den binära versionen för data-URI: n |
||||

*Exempel*

I det här exemplet skapas en binär version för denna data-URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar följande resultat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Returnera sträng versionen för en URI (data Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den data-URI som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sträng för data-URI*> | Sträng | Sträng versionen för data-URI: n |
||||

*Exempel*

I det här exemplet skapas en sträng för denna data-URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar följande resultat: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Returnera dagen i månaden från en tidsstämpel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i månaden*> | Integer | Dag i månaden från angiven tidsstämpel |
||||

*Exempel*

Det här exemplet returnerar numret för dagen i månaden från den här tidsstämpeln:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Och returnerar följande resultat: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Returnera vecko dagen från en tidsstämpel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i veckan*> | Integer | Vecko dagen från den angivna tidsstämpeln där söndag är 0, måndag är 1 och så vidare |
||||

*Exempel*

Det här exemplet returnerar numret för vecko dagen från den här tidsstämpeln:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Och returnerar följande resultat: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Returnera dagen på året från en tidsstämpel.

```
dayOfYear('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag – år*> | Integer | Dagen på året från den angivna tidsstämpeln |
||||

*Exempel*

Det här exemplet returnerar antalet dagar på året från den här tidsstämpeln:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Och returnerar följande resultat: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (inaktuell)

Den här funktionen är inaktuell, så Använd [base64ToString ()](#base64ToString) i stället.

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Returnera den binära versionen för en URI (data Uniform Resource Identifier). Överväg att använda [dataUriToBinary ()](#dataUriToBinary)i stället för `decodeDataUri()` . Även om båda funktionerna fungerar på samma sätt, `dataUriToBinary()` är föredra.

> [!NOTE]
> Azure Logic Apps utför automatiskt base64-kodning och avkodning, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

```
decodeDataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den data-URI-sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Binary-för-data-URI*> | Sträng | Den binära versionen för en data URI-sträng |
||||

*Exempel*

Det här exemplet returnerar den binära versionen för denna data-URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar följande resultat:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Returnera en sträng som ersätter escape-tecken med avkodade versioner.

```
decodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen med escape-tecken att avkoda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-URI*> | Sträng | Den uppdaterade strängen med avkodade escape-tecken |
||||

*Exempel*

I det här exemplet ersätts escape-tecknen i den här strängen med avkodade versioner:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

Och returnerar följande resultat: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Returnera resultatet från att dividera två tal. För att få resten av resultatet, se [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*preferens*> | Ja | Heltal eller flyttal | Talet som ska divideras med *divisorn* |
| <*divisor*> | Ja | Heltal eller flyttal | Talet som delar upp *utdelningen*, men kan inte vara 0 |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kvot-resultat*> | Heltal eller flyttal | Resultatet från att dividera det första talet med det andra talet. Om utdelningen eller divisorn har flytt ALS typ, har resultatet flyttal. <p><p>**Obs!** om du vill konvertera flytt ALS resultatet till ett heltal kan du försöka [skapa och anropa en funktion i Azure](../logic-apps/logic-apps-azure-functions.md) från din Logic app. |
||||

*Exempel 1*

Båda exemplen returnerar detta värde med Integer-typ: `2`

```
div(10,5)
div(11,5)
```

*Exempel 2*

Båda exemplen returnerar detta värde med typen Float: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en URI-kodad (Uniform Resource Identifier) version för en sträng genom att ersätta URL-osäkra tecken med escape-tecken. Överväg att använda [uriComponent ()](#uriComponent)i stället för `encodeUriComponent()` . Även om båda funktionerna fungerar på samma sätt, `uriComponent()` är föredra.

> [!NOTE]
> Azure Logic Apps utför automatiskt base64-kodning och avkodning, vilket innebär att du inte behöver utföra dessa konverteringar manuellt. Men om du gör det kan det uppstå oväntade visnings beteenden som inte påverkar de faktiska konverteringarna, bara hur de visas. Mer information finns i [implicita data typs konverteringar](#implicit-data-conversions).

```
encodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-URI*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
encodeUriComponent('https://contoso.com')
```

Och returnerar följande resultat: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>tomt

Kontrol lera om en samling är tom.
Returnera true när samlingen är tom eller returnera falskt om den inte är tom.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng, matris eller objekt | Den samling som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när samlingen är tom. Returnera falskt om det inte är tomt. |
||||

*Exempel*

De här exemplen kontrollerar om de angivna samlingarna är tomma:

```
empty('')
empty('abc')
```

Och returnerar följande resultat:

* Första exemplet: skickar en tom sträng, så funktionen returnerar `true` .
* Andra exempel: skickar strängen "ABC", så funktionen returnerar `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Kontrol lera om en sträng slutar med en angiven under sträng.
Returnera true när under strängen hittas eller returnera falskt när den inte hittas.
Den här funktionen är inte Skift läges känslig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som ska kontrol leras |
| <*searchText*> | Ja | Sträng | Slut under strängen som du hittar |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera true när slut under strängen hittas. Returnera falskt när det inte går att hitta. |
||||

*Exempel 1*

I det här exemplet kontrol leras om strängen "Hello World" slutar med "World"-strängen:

```
endsWith('hello world', 'world')
```

Och returnerar följande resultat: `true`

*Exempel 2*

I det här exemplet kontrol leras om strängen "Hello World" slutar med strängen "universum":

```
endsWith('hello world', 'universe')
```

Och returnerar följande resultat: `false`

<a name="equals"></a>

### <a name="equals"></a>lika med

Kontrol lera om både värden, uttryck eller objekt är likvärdiga.
Returnera true när båda är likvärdiga, eller returnera falskt när de inte är likvärdiga.

```
equals('<object1>', '<object2>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object1*> <*object2*> | Ja | Önskade | Värden, uttryck eller objekt som ska jämföras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när båda är likvärdiga. Returnera falskt om det inte är motsvarande. |
||||

*Exempel*

De här exemplen kontrollerar om angivna indata är likvärdiga.

```
equals(true, 1)
equals('abc', 'abcd')
```

Och returnerar följande resultat:

* Första exemplet: båda värdena är likvärdiga, så funktionen returnerar `true` .
* Andra exempel: båda värdena är inte likvärdiga, så funktionen returnerar `false` .

<a name="first"></a>

### <a name="first"></a>förstagångskörningen

Returnera det första objektet från en sträng eller matris.

```
first('<collection>')
first([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng eller matris | Samlingen där det första objektet ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*första samlings objekt*> | Valfri | Det första objektet i samlingen |
||||

*Exempel*

I de här exemplen hittar du det första objektet i de här samlingarna:

```
first('hello')
first(createArray(0, 1, 2))
```

Och returnerar följande resultat:

* Första exemplet: `"h"`
* Andra exempel: `0`

<a name="float"></a>

### <a name="float"></a>flyt

Konvertera en sträng version för ett flyttal till ett verkligt flytt ALS nummer.
Du kan bara använda den här funktionen när du skickar anpassade parametrar till en app, till exempel en Logic app eller ett flöde.

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den sträng som har ett giltigt flytt ALS nummer att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*float-Value*> | Float | Flytt ALS numret för den angivna strängen |
||||

*Exempel*

I det här exemplet skapas en sträng version för det här flytt ALS numret:

```
float('10.333')
```

Och returnerar följande resultat: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Returnera en tidstämpel i det angivna formatet.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*omformaterad – tidsstämpel*> | Sträng | Den uppdaterade tidsstämpeln i det angivna formatet |
||||

*Exempel*

I det här exemplet konverteras en tidstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar följande resultat: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Returnera en matris med värden som matchar ett nyckel namn i en åtgärds *formulär-data* eller *formulär-kodade* utdata.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Den åtgärd vars utdata har det nyckel värde som du vill använda |
| <*knapp*> | Ja | Sträng | Namnet på nyckeln vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en matris från nyckeln "subject" i den angivna åtgärdens formulär-data eller formulär-kodade utdata:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Och returnerar ämnes texten i en matris, till exempel: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Returnera ett enskilt värde som matchar ett nyckel namn i en åtgärds *formulär-data* eller *formulär-kodade* utdata.
Om funktionen hittar fler än en matchning, genererar funktionen ett fel.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Den åtgärd vars utdata har det nyckel värde som du vill använda |
| <*knapp*> | Ja | Sträng | Namnet på nyckeln vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckel värde*> | Sträng | Värdet i den angivna nyckeln  |
||||

*Exempel*

I det här exemplet skapas en sträng från nyckeln "subject" i den angivna åtgärdens formulär-data eller formulär-kodade utdata:

```
formDataValue('Send_an_email', 'Subject')
```

Och returnerar ämnes texten som en sträng, till exempel: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Returnera ett tal som en sträng baserat på det angivna formatet.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*många*> | Ja | Heltal eller dubbel | Det värde som du vill formatera. |
| <*formatering*> | Ja | Sträng | En sammansatt format sträng som anger det format som du vill använda. För de numeriska format strängar som stöds, se [standard numeriska format strängar](/dotnet/standard/base-types/standard-numeric-format-strings)som stöds av `number.ToString(<format>, <locale>)` . |
| <*språk*> | Inga | Sträng | Det språk som ska användas som stöds av `number.ToString(<format>, <locale>)` . Om inget värde anges är standardvärdet `en-us` . |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterat tal*> | Sträng | Det angivna talet som en sträng i det format som du har angett. Du kan omvandla det här retur värdet till en `int` eller `float` . |
||||

*Exempel 1*

Anta att du vill formatera talet `1234567890` . I det här exemplet formateras talet som strängen "1 234 567 890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

* Exempel 2 "

Anta att du vill formatera talet `1234567890` . I det här exemplet formateras talet till strängen "1.234.567.890, 00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Exempel 3*

Anta att du vill formatera talet `17.35` . I det här exemplet formateras talet till strängen "$17,35".

```
formatNumber(17.35, 'C2')
```

*Exempel 4*

Anta att du vill formatera talet `17.35` . I det här exemplet formateras talet till strängen "17, 35 kr".

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Returnera den aktuella tidsstämpeln och de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *Interval*: "sekund", "minut", "timme", "dag", "vecka", "månad", "år" |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Aktuell tidsstämpel plus angivet antal tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.0000000 Z".
I det här exemplet läggs fem dagar till i tidsstämpeln:

```
getFutureTime(5, 'Day')
```

Och returnerar följande resultat: `"2018-03-06T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.0000000 Z".
Det här exemplet lägger till fem dagar och konverterar resultatet till "D"-format:

```
getFutureTime(5, 'Day', 'D')
```

Och returnerar följande resultat: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska subtraheras |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *Interval*: "sekund", "minut", "timme", "dag", "vecka", "månad", "år" |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Den aktuella tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.0000000 Z".
I det här exemplet subtraheras fem dagar från tidsstämpeln:

```
getPastTime(5, 'Day')
```

Och returnerar följande resultat: `"2018-01-27T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.0000000 Z".
Det här exemplet subtraherar fem dagar och konverterar resultatet till "D"-format:

```
getPastTime(5, 'Day', 'D')
```

Och returnerar följande resultat: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>större än

Kontrol lera om det första värdet är större än det andra värdet.
Returnera true när det första värdet är större, eller returnera falskt om det är mindre.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Heltal, float eller string | Det första värdet för att kontrol lera om det är större än det andra värdet |
| <*compareTo*> | Ja | Heltal, float eller String respektive sträng | Jämförelse värde |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnerar sant när det första värdet är större än det andra värdet. Returnera falskt om det första värdet är lika med eller mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större än det andra värdet:

```
greater(10, 5)
greater('apple', 'banana')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>större än eller lika med

Kontrol lera om det första värdet är större än eller lika med det andra värdet.
Returnera true när det första värdet är större än eller lika med, eller returnera falskt om det första värdet är mindre.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Heltal, float eller string | Det första värdet för att kontrol lera om det är större än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, float eller String respektive sträng | Jämförelse värde |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnerar sant när det första värdet är större än eller lika med det andra värdet. Returnera falskt om det första värdet är mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större än eller lika med det andra värdet:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Generera en globalt unik identifierare (GUID) som en sträng, till exempel "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Du kan också ange ett annat format för GUID förutom standardformat, "D", som är 32 siffror avgränsade med bindestreck.

```
guid('<format>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*formatering*> | Inga | Sträng | En enkel [formats specifikation](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) för det returnerade GUID: t. Som standard är formatet "D", men du kan använda "N", "D", "B", "P" eller "X". |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*GUID-värde*> | Sträng | Ett slumpmässigt genererat GUID |
||||

*Exempel*

Det här exemplet genererar samma GUID, men som 32 siffror, avgränsade med bindestreck och omgivna av parenteser:

```
guid('P')
```

Och returnerar följande resultat: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>om

Kontrol lera om ett uttryck är sant eller falskt. Returnera ett angivet värde baserat på resultatet. Parametrarna utvärderas från vänster till höger.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck*> | Ja | Boolesk | Det uttryck som ska kontrol leras |
| <*valueIfTrue*> | Ja | Valfri | Det värde som ska returneras när uttrycket är sant |
| <*valueIfFalse*> | Ja | Valfri | Värdet som ska returneras när uttrycket är falskt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*angivet-retur värde*> | Valfri | Det angivna värdet som returnerar baserat på om uttrycket är sant eller falskt |
||||

*Exempel*

Det här exemplet returnerar `"yes"` eftersom det angivna uttrycket returnerar true.
Annars returnerar exemplet `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Returnera start positionen eller index värdet för en under sträng.
Den här funktionen är inte Skift läges känslig och index börjar med siffran 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som innehåller under strängen som ska hittas |
| <*searchText*> | Ja | Sträng | Under strängen som ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*index-värde*>| Integer | Start position eller index värde för den angivna under strängen. <p>Returnera talet-1 om strängen inte hittas. |
||||

*Exempel*

I det här exemplet hittas start index svärdet för "World"-del strängen i strängen "Hello World":

```
indexOf('hello world', 'world')
```

Och returnerar följande resultat: `6`

<a name="int"></a>

### <a name="int"></a>int

Returnera heltals versionen för en sträng.

```
int('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*heltal-resultat*> | Integer | Heltals versionen för den angivna strängen |
||||

*Exempel*

I det här exemplet skapas en heltals version för strängen "10":

```
int('10')
```

Och returnerar följande resultat: `10`

<a name="item"></a>

### <a name="item"></a>objekt

När den används i en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration.
Du kan också hämta värdena från det objektets egenskaper.

```
item()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Aktuellt mat ris objekt*> | Valfri | Det aktuella objektet i matrisen för åtgärdens aktuella iteration |
||||

*Exempel*

Det här exemplet hämtar `body` elementet från det aktuella meddelandet för åtgärden "Send_an_email" i en för-varje Loops aktuella iteration:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>objekt

Returnera det aktuella objektet från varje cykel i en for-each-slinga.
Använd den här funktionen inuti for-each-slingan.

```
items('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Ja | Sträng | Namnet på for-each-loopen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konfigurationsobjektet*> | Valfri | Objektet från den aktuella cykeln i angivet for-each-slinga |
||||

*Exempel*

Det här exemplet hämtar det aktuella objektet från angiven for-each-loop:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Returnera indexvärdet för den aktuella iterationen inuti en until-slinga. Du kan använda den här funktionen inuti kapslade tills loopar. 

```
iterationIndexes('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ja | Sträng | Namnet på loopen till | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Tabbindex*> | Integer | Indexvärdet för den aktuella iterationen inuti den angivna tills-slingan | 
|||| 

*Exempel* 

Det här exemplet skapar en Counter-variabel och ökar den variabeln med ett värde under varje iteration i en-loop tills räknarvärdet når fem. Exemplet skapar också en variabel som spårar det aktuella indexet för varje iteration. I exemplet tills, under varje iteration, ökar exemplet räknaren och tilldelar sedan räknaren värdet till det aktuella indexets värde och ökar sedan räknaren. I loopen refererar detta exempel till det aktuella upprepnings indexet med hjälp av `iterationIndexes` funktionen:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Returnera det JavaScript Object Notation (JSON) typ värde, objekt eller matris med objekt för en sträng eller XML.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Om inte ett XML-schema som definierar utdatans struktur anges kan funktionen returnera resultat där strukturen skiljer sig avsevärt från det förväntade formatet, beroende på indata.
>  
> Det här beteendet gör den här funktionen olämplig för scenarier där resultatet måste följa ett väldefinierat kontrakt, till exempel i kritiska affärs system eller lösningar.

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng eller XML | Strängen eller XML som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*JSON-resultat*> | Inbyggd JSON-typ, objekt eller matris | JSON-typens värde, objekt eller matris av objekt från Indatasträngen eller XML. <p><p>-Om du skickar XML som har ett enda underordnat element i rot elementet returnerar funktionen ett enda JSON-objekt för det underordnade elementet. <p> -Om du skickar XML som har flera underordnade element i rot elementet, returnerar funktionen en matris som innehåller JSON-objekt för de underordnade elementen. <p>-Om strängen är null returnerar funktionen ett tomt objekt. |
||||

*Exempel 1*

I det här exemplet konverteras strängen till ett JSON-värde:

```
json('[1, 2, 3]')
```

Och returnerar följande resultat: `[1, 2, 3]`

*Exempel 2*

I det här exemplet konverteras strängen till JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Och returnerar följande resultat:

```json
{
  "fullName": "Sophia Owen"
}
```

*Exempel 3*

I det här exemplet `json()` används `xml()` -och-funktionerna för att konvertera XML som har ett enda underordnat element i rot elementet till ett JSON-objekt med namnet `person` för det underordnade elementet:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

Och returnerar följande resultat:

```json
{
   "?xml": { 
      "@version": "1.0" 
   },
   "root": {
      "person": {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      }
   }
}
```

*Exempel 4*

I det här exemplet `json()` används `xml()` -och-funktionerna för att konvertera XML som har flera underordnade element i rot elementet till en matris med namnet `person` som innehåller JSON-objekt för de underordnade elementen:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

Och returnerar följande resultat:

```json
{
   "?xml": {
      "@version": "1.0"
   },
   "root": {
      "person": [
         {
            "@id": "1",
            "name": "Sophia Owen",
            "occupation": "Engineer"
         },
         {
            "@id": "2",
            "name": "John Doe",
            "occupation": "Engineer"
         }
      ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>överlappning

Returnera en samling som *endast* innehåller gemensamma objekt i de angivna samlingarna.
För att visas i resultatet måste ett objekt visas i alla samlingar som har skickats till den här funktionen.
Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*> <*collection2*>... | Ja | Matris eller objekt, men inte båda | Samlingarna där du *bara* vill ha de gemensamma objekten |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*common-Items*> | Matris eller objekt respektive | En samling som endast har gemensamma objekt i de angivna samlingarna |
||||

*Exempel*

I det här exemplet hittar du gemensamma objekt i dessa matriser:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Och returnerar en matris med *endast* följande objekt: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Returnera en sträng som innehåller alla objekt från en matris och som innehåller varje tecken avgränsat med en *avgränsare*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Matris | Matrisen som innehåller objekten som ska sammanfogas |
| <*avgränsare*> | Ja | Sträng | Avgränsaren som visas mellan varje tecken i den resulterande strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*CHAR1* >< *avgränsare* >< *CHAR2* >< *avgränsare*>... | Sträng | Den resulterande strängen som skapas från alla objekt i den angivna matrisen |
||||

*Exempel*

I det här exemplet skapas en sträng från alla objekt i matrisen med det angivna specialtecknet som avgränsare:

```
join(createArray('a', 'b', 'c'), '.')
```

Och returnerar följande resultat: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>pågå

Returnera det sista objektet från en samling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng eller matris | Den samling där du hittar det sista objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*senaste samlings objekt*> | Sträng eller matris | Det sista objektet i samlingen |
||||

*Exempel*

I de här exemplen hittar du det sista objektet i de här samlingarna:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Och returnerar följande resultat:

* Första exemplet: `"d"`
* Andra exempel: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Returnera start positionen eller index värde för den sista förekomsten av en under sträng. Den här funktionen är inte Skift läges känslig och index börjar med siffran 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som innehåller under strängen som ska hittas |
| <*searchText*> | Ja | Sträng | Under strängen som ska hittas |
|||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slut index-värde*> | Integer | Start position eller index värde för den sista förekomsten av den angivna under strängen. |
|||

Om strängen eller under sträng svärdet är tomt inträffar följande:

* Om endast strängvärdet är tomt returneras funktionen `-1` .

* Om strängen och del Strängs värden är tomma returneras funktionen `0` .

* Om endast del Strängs värde är tomt returnerar funktionen sträng längden minus 1.

*Exempel*

I det här exemplet hittas start indexet för den sista förekomsten av del strängen under `world` sträng i strängen `hello world hello world` . Det returnerade resultatet är `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

Det här exemplet saknar under Strängs parametern och returnerar värdet `22` eftersom värdet för Indatasträngen ( `23` ) minus 1 är större än 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>length

Returnera antalet objekt i en samling.

```
length('<collection>')
length([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng eller matris | Samlingen med objekten som ska räknas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*längd-eller-antal*> | Integer | Antalet objekt i samlingen |
||||

*Exempel*

Följande exempel räknar antalet objekt i dessa samlingar:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Och returnera följande resultat: `4`

<a name="less"></a>

### <a name="less"></a>mindre än

Kontrol lera om det första värdet är mindre än det andra värdet.
Returnera true när det första värdet är mindre, eller returnera falskt om det första värdet är mer.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Heltal, float eller string | Det första värdet för att kontrol lera om färre än det andra värdet |
| <*compareTo*> | Ja | Heltal, float eller String respektive sträng | Jämförelse objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnerar sant när det första värdet är mindre än det andra värdet. Returnera falskt om det första värdet är lika med eller större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre än det andra värdet.

```
less(5, 10)
less('banana', 'apple')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>mindre än eller lika med

Kontrol lera om det första värdet är mindre än eller lika med det andra värdet.
Returnera true när det första värdet är mindre än eller lika med, eller returnera falskt om det första värdet är mer.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Heltal, float eller string | Det första värdet för att kontrol lera om det är mindre än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, float eller String respektive sträng | Jämförelse objektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnerar sant när det första värdet är mindre än eller lika med det andra värdet. Returnera falskt om det första värdet är större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre än eller lika med det andra värdet.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Returnera återanrops-URL: en som anropar en utlösare eller åtgärd.
Den här funktionen fungerar bara med utlösare och åtgärder för anslutnings typerna **HttpWebhook** och **ApiConnectionWebhook** , men inte typerna **manual**, **upprepning**, **http** och **APIConnection** .

```
listCallbackUrl()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Sträng | Återanrops-URL för en utlösare eller åtgärd |
||||

*Exempel*

I det här exemplet visas en exempel-callback-URL som den här funktionen kan returnera:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Returnera det högsta värdet från en lista eller matris med värden som inkluderas i båda ändar.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tal1*>, <*tal2*>,... | Ja | Integer, float eller Both | Den uppsättning tal som du vill ha det högsta värdet från |
| [<*tal1*>, <*tal2*>,...] | Ja | Matris-Integer, float eller Both | Matrisen med tal som du vill ha det högsta värdet från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Max värde*> | Heltal eller flyttal | Det högsta värdet i den angivna matrisen eller uppsättningen med tal |
||||

*Exempel*

Följande exempel hämtar det högsta värdet från uppsättningen med tal och matrisen:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Och returnera följande resultat: `3`

<a name="min"></a>

### <a name="min"></a>min

Returnera det lägsta värdet från en uppsättning tal eller en matris.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tal1*>, <*tal2*>,... | Ja | Integer, float eller Both | Den uppsättning tal som du vill ha det lägsta värdet från |
| [<*tal1*>, <*tal2*>,...] | Ja | Matris-Integer, float eller Both | Matrisen med tal som du vill ha det lägsta värdet från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*minsta värde*> | Heltal eller flyttal | Det lägsta värdet i den angivna uppsättningen tal eller en angiven matris |
||||

*Exempel*

Följande exempel hämtar det lägsta värdet i uppsättningen med tal och matrisen:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Och returnera följande resultat: `1`

<a name="mod"></a>

### <a name="mod"></a>rest

Returnera resten från att dividera två tal.
För att få ett heltals resultat, se [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*preferens*> | Ja | Heltal eller flyttal | Talet som ska divideras med *divisorn* |
| <*divisor*> | Ja | Heltal eller flyttal | Talet som delar upp *utdelningen*, men kan inte vara 0. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*modulo-resultat*> | Heltal eller flyttal | Resten från att dividera det första talet med det andra talet |
||||

*Exempel*

I det här exemplet delas det första talet med det andra talet:

```
mod(3, 2)
```

Och returnera följande resultat: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Returnera produkten från att multiplicera två tal.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Heltal eller flyttal | Talet som ska multipliceras med *multiplicand2* |
| <*multiplicand2*> | Ja | Heltal eller flyttal | Talet som multipler *multiplicand1* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*produkt resultat*> | Heltal eller flyttal | Produkten från att multiplicera det första talet med det andra talet |
||||

*Exempel*

De här exemplen är flera första talet med det andra talet:

```
mul(1, 2)
mul(1.5, 2)
```

Och returnerar följande resultat:

* Första exemplet: `2`
* Andra exempel `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Returnera bröd texten för en viss del i en åtgärds utdata som har flera delar.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärden som har utdata med flera delar |
| <*Tabbindex*> | Ja | Integer | Indexets värde för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*brödtext*> | Sträng | Texten för den angivna delen |
||||

<a name="not"></a>

### <a name="not"></a>inte

Kontrol lera om ett uttryck är falskt.
Returnera true när uttrycket är falskt eller returnera falskt om värdet är sant.

```json
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck*> | Ja | Boolesk | Det uttryck som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnerar sant när uttrycket är falskt. Returnera falskt när uttrycket är sant. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(false)
not(true)
```

Och returnerar följande resultat:

* Första exemplet: uttrycket är falskt, så funktionen returnerar `true` .
* Andra exempel: uttrycket är sant, så funktionen returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Och returnerar följande resultat:

* Första exemplet: uttrycket är falskt, så funktionen returnerar `true` .
* Andra exempel: uttrycket är sant, så funktionen returnerar `false` .

<a name="or"></a>

### <a name="or"></a>eller

Kontrol lera om minst ett uttryck är sant.
Returnera true när minst ett uttryck är sant, eller returnera falskt när alla är falskt.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*Uttryck2*>,... | Ja | Boolesk | De uttryck som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när minst ett uttryck är sant. Returnera falskt när alla uttryck är falskt. |
||||

*Exempel 1*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(true, false)
or(false, false)
```

Och returnerar följande resultat:

* Första exemplet: minst ett uttryck är sant, så funktionen returnerar `true` .
* Andra exempel: båda uttrycken är falska, så funktionen returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Första exemplet: minst ett uttryck är sant, så funktionen returnerar `true` .
* Andra exempel: båda uttrycken är falska, så funktionen returnerar `false` .

<a name="outputs"></a>

### <a name="outputs"></a>utdata

Returnera en åtgärds utdata vid körning. Använd den här funktionen i stället för `actionOutputs()` , som matchar `outputs()` i Logic App Designer. Även om båda funktionerna fungerar på samma sätt, `outputs()` är föredra.

```
outputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*utdataparametrar*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

I det här exemplet hämtas utdata från Twitter-åtgärden `Get user` :

```
outputs('Get_user')
```

Och returnerar följande resultat:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parametrar

Returnera värdet för en parameter som beskrivs i arbets flödes definitionen.

```
parameters('<parameterName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | Sträng | Namnet på den parameter vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*parameter-värde*> | Valfri | Värdet för den angivna parametern |
||||

*Exempel*

Anta att du har det här JSON-värdet:

```json
{
  "fullName": "Sophia Owen"
}
```

I det här exemplet hämtas värdet för den angivna parametern:

```
parameters('fullName')
```

Och returnerar följande resultat: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>slump

Returnera ett slumpmässigt heltal från ett angivet intervall, som endast inkluderas i Start End.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Integer | Det lägsta heltalet i intervallet |
| <*maxValue*> | Ja | Integer | Det heltal som följer det högsta heltalet i intervallet som funktionen kan returnera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slumpmässigt resultat*> | Integer | Det slumpmässiga heltal som returnerades från det angivna intervallet |
||||

*Exempel*

Det här exemplet hämtar ett slumpmässigt heltal från det angivna intervallet, exklusive det maximala värdet:

```
rand(1, 5)
```

Och returnerar ett av dessa tal som resultat: `1` , `2` , `3` eller `4`

<a name="range"></a>

### <a name="range"></a>intervall

Returnera en heltals mat ris som börjar från ett angivet heltal.

```
range(<startIndex>, <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Start*> | Ja | Integer | Ett heltals värde som startar matrisen som det första objektet |
| <*reparationer*> | Ja | Integer | Antalet heltal i matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*intervall-resultat*>] | Matris | Matrisen med heltal som börjar från det angivna indexet |
||||

*Exempel*

I det här exemplet skapas en heltals mat ris som börjar från det angivna indexet och som har det angivna antalet heltal:

```
range(1, 4)
```

Och returnerar följande resultat: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>bytt

Ersätt en under sträng med den angivna strängen och returnera resultat strängen. Den här funktionen är Skift läges känslig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som innehåller under strängen som ska ersättas |
| <*gammal_text*> | Ja | Sträng | Under strängen som ska ersättas |
| <*ny_text*> | Ja | Sträng | Ersättnings strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad – text*> | Sträng | Den uppdaterade strängen efter att under strängen ersatts <p>Returnera den ursprungliga strängen om under strängen inte hittas. |
||||

*Exempel*

Det här exemplet hittar den "gamla" del strängen i "den gamla strängen" och ersätter "Old" med "ny":

```
replace('the old string', 'old', 'new')
```

Och returnerar följande resultat: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Ta bort en egenskap från ett objekt och returnera det uppdaterade objektet. Om egenskapen som du försöker ta bort inte finns, returnerar funktionen det ursprungliga objektet.

```
removeProperty(<object>, '<property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet som du vill ta bort en egenskap från |
| <*immaterialrätt*> | Ja | Sträng | Namnet på egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat – objekt*> | Objekt | Det uppdaterade JSON-objektet utan den angivna egenskapen |
||||

Om du vill ta bort en underordnad egenskap från en befintlig egenskap använder du följande syntax:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet vars egenskap du vill ta bort |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på överordnad egenskap med den underordnade egenskapen som du vill ta bort |
| <*underordnad egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat – objekt*> | Objekt | Det uppdaterade JSON-objektet vars underordnade egenskap du tog bort |
||||

*Exempel 1*

Det här exemplet tar bort `middleName` egenskapen från ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av [JSON ()](#json) -funktionen, och returnerar det uppdaterade objektet:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exempel 2*

I det här exemplet tas den `middleName` underordnade egenskapen bort från en `customerName` överordnad egenskap i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av [JSON ()](#json) -funktionen och returnerar det uppdaterade objektet:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>resultat

Returnera resultaten från de översta åtgärderna i den angivna åtgärds omfattningen, till exempel en `For_each` , `Until` eller `Scope` åtgärd. `result()`Funktionen accepterar en enda parameter, som är omfångets namn och returnerar en matris som innehåller information från de första nivå åtgärderna i det aktuella omfånget. Dessa åtgärds objekt innehåller samma attribut som de som returneras av `actions()` funktionen, till exempel start tid, slut tid, status, indata, korrelations-ID och utdata.

> [!NOTE]
> Den här funktionen returnerar information *enbart* från de första nivå åtgärderna i åtgärdad omfattning och inte från djupare kapslade åtgärder, till exempel switch-eller Condition-åtgärder.

Du kan till exempel använda den här funktionen för att hämta resultatet från misslyckade åtgärder så att du kan diagnostisera och hantera undantag. Mer information finns i [Hämta kontext och resultat för problem](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Sträng | Namnet på den omfångs åtgärd där du vill ha indata och utdata från de översta åtgärderna i det omfånget |
||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*matris-objekt*> | Mat ris objekt | En matris som innehåller matriser med indata och utdata från varje toppnivå åtgärd inom det angivna omfånget |
||||

*Exempel*

I det här exemplet returneras indata och utdata från varje iteration av en HTTP-åtgärd i en `For_each` slinga med hjälp av `result()` funktionen i `Compose` åtgärden:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Så här kan exemplet returnerade matrisen se var det yttre `outputs` objektet innehåller indata och utdata från varje iteration av åtgärderna i `For_each` åtgärden.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Ange värdet för JSON-objektets egenskap och returnera det uppdaterade objektet. Om den egenskap som du försöker ange inte finns läggs egenskapen till i objektet. Om du vill lägga till en ny egenskap använder du funktionen [addProperty ()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet vars egenskap du vill ange |
| <*immaterialrätt*> | Ja | Sträng | Namnet på den befintliga eller nya egenskapen som ska anges |
| <*värde*> | Ja | Valfri | Värdet som ska anges för den angivna egenskapen |
|||||

Använd ett kapslat anrop i stället för att ställa in den underordnade egenskapen i ett underordnat objekt `setProperty()` . Annars returnerar funktionen bara det underordnade objektet som utdata.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*jobbobjektet*> | Ja | Objekt | JSON-objektet vars egenskap du vill ange |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på överordnad egenskap med den underordnade egenskapen som du vill ange |
| <*underordnad egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska anges |
| <*värde*> | Ja | Valfri | Värdet som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterat – objekt*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

I det här exemplet anges `surName` egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen [JSON ()](#json) . Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Här är det aktuella JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exempel 2*

I det här exemplet anges den `surName` underordnade egenskapen för den `customerName` överordnade egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av [JSON ()-](#json) funktionen. Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Här är det aktuella JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Här är det uppdaterade JSON-objektet:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>hoppa över

Ta bort objekt från början av en samling och returnera *alla andra* objekt.

```
skip([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Matris | Den samling vars objekt du vill ta bort |
| <*reparationer*> | Ja | Integer | Ett positivt heltal för antalet objekt som ska tas bort längst fram |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*uppdaterad – samling*>] | Matris | Den uppdaterade samlingen efter att de angivna objekten har tagits bort |
||||

*Exempel*

Det här exemplet tar bort ett objekt, siffran 0, från början av den angivna matrisen:

```
skip(createArray(0, 1, 2, 3), 1)
```

Och returnerar matrisen med återstående objekt: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Returnera en matris som innehåller del strängar, avgränsade med kommatecken, baserat på det angivna avgränsnings tecken i den ursprungliga strängen.

```
split('<text>', '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som ska separeras i del strängar baserat på den angivna avgränsaren i den ursprungliga strängen |
| <*avgränsare*> | Ja | Sträng | Det tecken i den ursprungliga strängen som ska användas som avgränsare |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*substring1*> <*substring2*>,...] | Matris | En matris som innehåller del strängar från den ursprungliga strängen, avgränsade med kommatecken |
||||

*Exempel*

I det här exemplet skapas en matris med del strängar från den angivna strängen baserat på det angivna specialtecknet som avgränsare:

```
split('a_b_c', '_')
```

Och returnerar denna matris som resultat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Returnera början på dagen för en tidsstämpel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln som har angetts men som börjar vid noll-timmarsformat för dagen |
||||

*Exempel*

I det här exemplet hittas början på dagen för den här tidsstämpeln:

```
startOfDay('2018-03-15T13:30:30Z')
```

Och returnerar följande resultat: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Returnera början på timmen för en tidsstämpel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Den angivna tidsstämpeln men från noll-minuten-märket för timmen |
||||

*Exempel*

I det här exemplet hittas början av timmen för den här tidsstämpeln:

```
startOfHour('2018-03-15T13:30:30Z')
```

Och returnerar följande resultat: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Returnera början på månaden för en tidsstämpel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Den angivna tidsstämpeln men från den första dagen i månaden vid noll-timmarsformat |
||||

*Exempel 1*

Det här exemplet returnerar början på månaden för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar följande resultat: `"2018-03-01T00:00:00.0000000Z"`

*Exempel 2*

Det här exemplet returnerar början på månaden i det angivna formatet för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

Och returnerar följande resultat: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontrol lera om en sträng börjar med en speciell under sträng.
Returnera true när under strängen hittas eller returnera falskt när den inte hittas.
Den här funktionen är inte Skift läges känslig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som ska kontrol leras |
| <*searchText*> | Ja | Sträng | Start strängen som ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera true när start under strängen hittas. Returnera falskt när det inte går att hitta. |
||||

*Exempel 1*

I det här exemplet kontrol leras om strängen "Hello World" börjar med under strängen "Hello":

```
startsWith('hello world', 'hello')
```

Och returnerar följande resultat: `true`

*Exempel 2*

I det här exemplet kontrol leras om strängen "Hello World" börjar med under strängen "Greetings":

```
startsWith('hello world', 'greetings')
```

Och returnerar följande resultat: `false`

<a name="string"></a>

### <a name="string"></a>sträng

Returnera sträng versionen för ett värde.

```
string(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Valfri | Det värde som ska konverteras. Om det här värdet är null eller utvärderas till null, konverteras värdet till ett tomt sträng `""` värde (). <p><p>Om du till exempel tilldelar en sträng variabel till en icke-befintlig egenskap, som du kan komma åt med `?` operatorn, konverteras värdet null till en tom sträng. Att jämföra ett null-värde är dock inte detsamma som att jämföra en tom sträng. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sträng-värde*> | Sträng | Sträng versionen för det angivna värdet. Om *värde* parametern är null eller utvärderas till null returneras det här värdet som ett tomt sträng `""` värde (). |
||||





*Exempel 1*

I det här exemplet skapas sträng versionen för det här talet:

```
string(10)
```

Och returnerar följande resultat: `"10"`

*Exempel 2*

Det här exemplet skapar en sträng för det angivna JSON-objektet och använder omvänt snedstreck ( \\ ) som ett escape-tecken för dubbla citat tecken (").

```
string( { "name": "Sophie Owen" } )
```

Och returnerar följande resultat: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Build

Returnera resultatet från att subtrahera det andra talet från det första talet.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*MINUEND*> | Ja | Heltal eller flyttal | Talet som *SUBTRAHEND* ska subtraheras från |
| <*SUBTRAHEND*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras från *MINUEND* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*medför*> | Heltal eller flyttal | Resultatet från att subtrahera det andra talet från det första talet |
||||

*Exempel*

I det här exemplet subtraheras den andra siffran från det första talet:

```
sub(10.3, .3)
```

Och returnerar följande resultat: `10`

<a name="substring"></a>

### <a name="substring"></a>under sträng

Returnera tecken från en sträng, från angiven position eller index. Index värden börjar med siffran 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen vars tecken du vill ha |
| <*Start*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som start position eller index värde |
| <*krävande*> | Inga | Integer | Ett positivt antal tecken som du vill ha i under strängen |
|||||

> [!NOTE]
> Se till att summan för att lägga till värdena *Start* värden och *längd* parameter är mindre än längden på strängen som du anger för *text* parametern.
> Annars visas ett fel, till skillnad från liknande funktioner på andra språk där resultatet är del strängen från *Start indexet* till slutet av strängen. Parametern *length* är valfri och om den inte anges tar funktionen **substring ()** alla tecken som börjar från *Start index* till slutet av strängen.

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*del sträng – resultat*> | Sträng | En under sträng med det angivna antalet tecken, med början vid den angivna index positionen i käll strängen |
||||

*Exempel*

I det här exemplet skapas en del sträng med fem tecken från den angivna strängen, med början från index värde 6:

```
substring('hello world', 6, 5)
```

Och returnerar följande resultat: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrahera ett antal tidsenheter från en tidsstämpel.
Se även [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Den sträng som innehåller tidsstämpeln |
| <*intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska subtraheras |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *Interval*: "sekund", "minut", "timme", "dag", "vecka", "månad", "år" |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Och returnerar följande resultat: `"2018-01-01T00:00:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar det här resultatet med det valfria "D"-formatet: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Returnera objekt från början av en samling.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*samlingen*> | Ja | Sträng eller matris | Den samling vars objekt du vill ha |
| <*reparationer*> | Ja | Integer | Ett positivt heltal för antalet objekt som du vill ha från fram sidan |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delmängd*> eller [<*delmängd*>] | Sträng eller matris | En sträng eller matris som har det angivna antalet objekt som har hämtats från början av den ursprungliga samlingen |
||||

*Exempel*

Följande exempel hämtar det angivna antalet objekt från början av de här samlingarna:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Och returnerar följande resultat:

* Första exemplet: `"abc"`
* Andra exempel: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Returnerar antalet Tick, som är 100 – nanosekunder-intervall, sedan den 1 januari 0001 12:00:00 midnatt (eller DateTime. Tick i C#) upp till den angivna tidsstämpeln. Mer information finns i det här avsnittet: [DateTime. ticks-egenskapen (system)](/dotnet/api/system.datetime.ticks).

```
ticks('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*tidsstämpel*> | Ja | Sträng | Strängen för en tidsstämpel |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*ticks-tal*> | Integer | Antalet Tick sedan den angivna tidsstämpeln |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Returnera en sträng med gemener. Om ett tecken i strängen inte har en gemen version, är det tecken oförändrat i den returnerade strängen.

```
toLower('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som ska returneras i gemener-format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*gemener – text*> | Sträng | Den ursprungliga strängen i gement format |
||||

*Exempel*

I det här exemplet konverteras strängen till gemener:

```
toLower('Hello World')
```

Och returnerar följande resultat: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Returnera en sträng i versal format. Om ett tecken i strängen inte har en versal version, är det tecken oförändrat i den returnerade strängen.

```
toUpper('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som ska returneras i versal format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*versaler – text*> | Sträng | Den ursprungliga strängen i versalt format |
||||

*Exempel*

I det här exemplet konverteras strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar följande resultat: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>utlösare

Returnera en utlösare utdata vid körning eller värden från andra JSON-namn och värde-par som du kan tilldela till ett uttryck.

* I en utlösare indata returnerar den här funktionen resultatet från föregående körning.

* I en utlösare villkor returnerar den här funktionen utdata från den aktuella körningen.

Som standard refererar funktionen till hela Utlösar-objektet, men du kan också ange en egenskap vars värde du vill ha.
Den här funktionen har också kort versioner tillgängliga, se [triggerOutputs ()](#triggerOutputs) och [triggerBody ()](#triggerBody).

```
trigger()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Utlös vid utdata*> | Sträng | Utdata från en utlösare vid körning |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Returnera en utlösare `body` utdata vid körning.
Snabb kort för `trigger().outputs.body` .
Se [utlösare ()](#trigger).

```
triggerBody()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Utlös ande Body-utdata*> | Sträng | `body`Utdata från utlösaren |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Returnera en matris med värden som matchar ett nyckel namn i utlösaren *formulär data* eller *formulär-kodade* utdata.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*knapp*> | Ja | Sträng | Namnet på nyckeln vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris med nyckel värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en matris från nyckel värdet "feedUrl" i en RSS-utlösare formulär-data eller formulär-kodade utdata:

```
triggerFormDataMultiValues('feedUrl')
```

Och returnerar denna matris som ett exempel resultat: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Returnera en sträng med ett enda värde som matchar ett nyckel namn i utlösaren *formulär data* eller *formulär-kodade* utdata.
Om funktionen hittar fler än en matchning, genererar funktionen ett fel.

```
triggerFormDataValue('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*knapp*> | Ja | Sträng | Namnet på nyckeln vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckel värde*> | Sträng | Värdet i den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en sträng från nyckel värdet "feedUrl" i en RSS-utlösare formulär data eller formulär-kodade utdata:

```
triggerFormDataValue('feedUrl')
```

Och returnerar strängen som ett exempel resultat: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Returnera bröd texten för en viss del i en utlösare utdata som har flera delar.

```
triggerMultipartBody(<index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tabbindex*> | Ja | Integer | Indexets värde för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*brödtext*> | Sträng | Bröd texten för den angivna delen i en utlösare för flerdelade utdata |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Returnera en utlösare utdata vid körning eller värden från andra JSON-namn och värde-par.
Snabb kort för `trigger().outputs` .
Se [utlösare ()](#trigger).

```
triggerOutputs()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Utlös vid utdata*> | Sträng | Utdata från en utlösare vid körning  |
||||

<a name="trim"></a>

### <a name="trim"></a>reducera

Ta bort inledande och avslutande blank steg från en sträng och returnera den uppdaterade strängen.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Den sträng som har det inledande och avslutande blank steg som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedText*> | Sträng | En uppdaterad version av den ursprungliga strängen utan inledande eller avslutande blank steg |
||||

*Exempel*

I det här exemplet tas inledande och avslutande blank steg bort från strängen "Hello World":

```
trim(' Hello World  ')
```

Och returnerar följande resultat: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Union

Returnera en samling som innehåller *alla* objekt från de angivna samlingarna.
För att visas i resultatet kan ett objekt visas i alla samlingar som skickas till den här funktionen. Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*> <*collection2*>...  | Ja | Matris eller objekt, men inte båda | Samlingarna där du vill ha *alla* objekt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matris eller objekt respektive | En samling med alla objekt från de angivna samlingarna-inga dubbletter |
||||

*Exempel*

Det här exemplet hämtar *alla* objekt från de här samlingarna:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Och returnerar följande resultat: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Returnera en URI-kodad (Uniform Resource Identifier) version för en sträng genom att ersätta URL-osäkra tecken med escape-tecken.
Använd den här funktionen i stället för [encodeUriComponent ()](#encodeUriComponent).
Även om båda funktionerna fungerar på samma sätt, `uriComponent()` är föredra.

```
uriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*kodad-URI*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
uriComponent('https://contoso.com')
```

Och returnerar följande resultat: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Returnera den binära versionen för en URI-komponent (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den URI-kodade sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Binary-för-kodad-URI*> | Sträng | Den binära versionen för den URI-kodade strängen. Det binära innehållet är Base64-kodat och representeras av `$content` . |
||||

*Exempel*

I det här exemplet skapas den binära versionen för denna URI-kodade sträng:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

Och returnerar följande resultat:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Returnera sträng versionen för en kodad sträng med URI (Uniform Resource Identifier), och avkoda den URI-kodade strängen effektivt.

```
uriComponentToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Den URI-kodade sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*avkodad-URI*> | Sträng | Den avkodade versionen för URI-kodad sträng |
||||

*Exempel*

I det här exemplet skapas den avkodade sträng versionen för denna URI-kodade sträng:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

Och returnerar följande resultat: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Returnera `host` värdet för en URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `host` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*värd värde*> | Sträng | `host`Värdet för angiven URI |
||||

*Exempel*

I det här exemplet hittas `host` värdet för denna URI:

```
uriHost('https://www.localhost.com:8080')
```

Och returnerar följande resultat: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Returnera `path` värdet för en URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `path` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sökväg-värde*> | Sträng | `path`Värdet för den angivna URI: n. `path`Returnera "/" om det inte har något värde. |
||||

*Exempel*

I det här exemplet hittas `path` värdet för denna URI:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar följande resultat: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Returnera `path` värdena och `query` för en URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `path` och de `query` värden du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sökväg-fråga-värde*> | Sträng | `path`Värdena och `query` för angiven URI. Om inget `path` värde anges returnerar du-tecken. |
||||

*Exempel*

I det här exemplet `path` hittas `query` värdena och för denna URI:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar följande resultat: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Returnera `port` värdet för en URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `port` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Port-värde*> | Integer | `port`Värdet för den angivna URI: n. Om inget `port` värde anges returnerar du standard porten för protokollet. |
||||

*Exempel*

Det här exemplet returnerar `port` värdet för denna URI:

```
uriPort('https://www.localhost:8080')
```

Och returnerar följande resultat: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Returnera `query` värdet för en URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `query` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*fråga-värde*> | Sträng | `query`Värdet för angiven URI |
||||

*Exempel*

Det här exemplet returnerar `query` värdet för denna URI:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar följande resultat: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Returnera `scheme` värdet för en URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*URI*> | Ja | Sträng | Den URI vars `scheme` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*schema-värde*> | Sträng | `scheme`Värdet för angiven URI |
||||

*Exempel*

Det här exemplet returnerar `scheme` värdet för denna URI:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar följande resultat: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Alternativt kan du ange ett annat format med parametern <*format*>.


| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS. fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*aktuell – tidsstämpel*> | Sträng | Aktuellt datum och aktuell tid |
||||

*Exempel 1*

Anta att dagens datum är 15 april 2018 vid 1:00:00 PM.
Det här exemplet hämtar aktuell tidsstämpel:

```
utcNow()
```

Och returnerar följande resultat: `"2018-04-15T13:00:00.0000000Z"`

*Exempel 2*

Anta att dagens datum är 15 april 2018 vid 1:00:00 PM.
Det här exemplet hämtar den aktuella tidsstämpeln med det valfria "D"-formatet:

```
utcNow('D')
```

Och returnerar följande resultat: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>användarvariabler

Returnera värdet för en angiven variabel.

```
variables('<variableName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | Sträng | Namnet på den variabel vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*variabel värde*> | Valfri | Värdet för den angivna variabeln |
||||

*Exempel*

Anta att det aktuella värdet för en "numItems"-variabel är 20.
Det här exemplet hämtar heltal svärdet för den här variabeln:

```
variables('numItems')
```

Och returnerar följande resultat: `20`

<a name="workflow"></a>

### <a name="workflow"></a>arbetsflöde

Returnera all information om själva arbets flödet under körnings tiden.

```
workflow().<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*immaterialrätt*> | Inga | Sträng | Namnet på den arbets flödes egenskap vars värde du vill ha <p><p>Som standard har ett arbets flödes objekt följande egenskaper: `name` ,,,, `type` `id` `location` `run` och `tags` . <p><p>– `run` Egenskap svärdet är ett JSON-objekt som innehåller dessa egenskaper: `name` , `type` och `id` . <p><p>- `tags` Egenskapen är ett JSON-objekt som innehåller [taggar som är associerade med din Logic app i Azure Logic Apps eller flödet i Energis par](../azure-resource-manager/management/tag-resources.md) och värden för dessa taggar. Om du vill ha mer information om taggar i Azure-resurser granskar du [tagga resurser, resurs grupper och prenumerationer för logisk organisation i Azure](../azure-resource-manager/management/tag-resources.md). <p><p>**Obs!** en Logic app har som standard inga taggar, men ett energi flöde har `flowDisplayName` `environmentName` taggarna och. |
|||||

*Exempel 1*

Det här exemplet returnerar namnet på arbets flödets aktuella körning:

`workflow().run.name`

*Exempel 2*

Om du använder automatisk energi användning kan du skapa ett `@workflow()` uttryck som använder `tags` egenskapen output för att hämta värdena från ditt flödes `flowDisplayName` eller `environmentName` egenskap.

Du kan till exempel skicka anpassade e-postaviseringar från själva flödet som länkar tillbaka till ditt flöde. Dessa meddelanden kan innehålla en HTML-länk som innehåller flödets visnings namn i e-postrubriken och följer den här syntaxen:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

Returnera XML-versionen för en sträng som innehåller ett JSON-objekt.

```
xml('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng | Strängen med JSON-objektet som ska konverteras <p>JSON-objektet får bara ha en rot egenskap som inte kan vara en matris. <br>Använd det omvända snedstrecket ( \\ ) som ett escape-tecken för det dubbla citat tecknet ("). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*XML-version*> | Objekt | Den kodade XML-koden för den angivna strängen eller JSON-objektet |
||||

*Exempel 1*

I det här exemplet skapas XML-versionen för den här strängen, som innehåller ett JSON-objekt:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Och returnerar denna resultat-XML:

```xml
<name>Sophia Owen</name>
```

*Exempel 2*

Anta att du har det här JSON-objektet:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

I det här exemplet skapas XML för en sträng som innehåller detta JSON-objekt:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Och returnerar denna resultat-XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>XPath

Kontrol lera XML för noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera matchande noder eller värden. Ett XPath-uttryck, eller bara "XPath", hjälper dig att navigera i XML-dokumentets struktur så att du kan välja noder eller beräknings värden i XML-innehållet.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*fil*> | Ja | Valfri | XML-sträng för att söka efter noder eller värden som matchar ett XPath-uttrycks värde |
| <*XPath*> | Ja | Valfri | XPath-uttryck som används för att hitta matchande XML-noder eller värden |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*XML-nod*> | XML | En XML-nod när endast en nod matchar angivet XPath-uttryck |
| <*värde*> | Valfri | Värdet från en XML-nod när endast ett enda värde matchar angivet XPath-uttryck |
| [<*XML-nod1*> <*xml-NOD2*>,...] </br>\- eller - </br>[<*värde1*> <*värde2*>,...] | Matris | En matris med XML-noder eller värden som matchar angivet XPath-uttryck |
||||

*Exempel 1*

Anta att du har följande `'items'` XML-sträng: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Det här exemplet skickar i XPath-uttrycket, `'/produce/item/name'` för att hitta de noder som matchar `<name></name>` noden i `'items'` XML-strängen och returnerar en matris med dessa Node-värden:

`xpath(xml(parameters('items')), '/produce/item/name')`

Exemplet använder också funktionen [Parameters ()](#parameters) för att hämta XML-strängen från `'items'` och konvertera strängen till XML-format med hjälp av funktionen [XML ()](#xml) .

Här är resultatet matrisen med de noder som matchar `<name></name` :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exempel 2*

I följande exempel skickar det här exemplet i XPath-uttrycket, `'/produce/item/name[1]'` för att hitta det första `name` elementet som är underordnat `item` elementet.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Här är resultatet: `Gala`

*Exempel 3*

I följande exempel skickar det här exemplet i XPath-uttrycket, `'/produce/item/name[last()]'` för att hitta det sista `name` elementet som är underordnat `item` elementet.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Här är resultatet: `Honeycrisp`

*Exempel 4*

I det här exemplet antar vi att din `items` XML-sträng även innehåller attributen `expired='true'` och `expired='false'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Det här exemplet skickar i XPath-uttrycket, `'//name[@expired]'` för att hitta alla `name` element som har `expired` attributet:

`xpath(xml(parameters('items')), '//name[@expired]')`

Här är resultatet: `[ Gala, Honeycrisp ]`

*Exempel 5*

I det här exemplet antar vi att din `items` XML-sträng endast innehåller det här attributet `expired = 'true'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Det här exemplet skickar i XPath-uttrycket, `'//name[@expired = 'true']'` för att hitta alla `name` element som har attributet `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Här är resultatet: `[ Gala ]`

*Exempel 6*

I det här exemplet antar vi att din `items` XML-sträng även innehåller följande attribut: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Det här exemplet skickar i XPath-uttrycket, `'//name[price>35]'` för att hitta alla `name` element som har `price > 35` :

`xpath(xml(parameters('items')), '//name[price>35]')`

Här är resultatet: `Honeycrisp`

*Exempel 7*

I det här exemplet antar vi att din `items` XML-sträng är samma som i exempel 1:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Det här exemplet hittar noder som matchar `<count></count>` noden och lägger till noderna med `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Här är resultatet: `30`

*Exempel 8*

I det här exemplet antar vi att du har den här XML-strängen, som innehåller XML-dokumentets namnrymd, `xmlns="https://contoso.com"` :

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Dessa uttryck använder antingen XPath-uttryck `/*[name()="file"]/*[name()="location"]` eller `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` , för att hitta noder som matchar `<location></location>` noden. I de här exemplen visas den syntax som du använder i antingen Logic Apps designer eller i uttrycks redigeraren:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Här är den resulterande noden som matchar `<location></location>` noden: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Om du arbetar i kodvyn ska du undanta det dubbla citat tecknet (") med hjälp av omvänt snedstreck ( \\ ). 
> Du måste till exempel använda escape-tecken när du serialiserar ett uttryck som en JSON-sträng. 
> Men om du arbetar i Logic App Designer eller i uttrycks redigeraren, behöver du inte undanta det dubbla citat tecknet eftersom det omvända snedstrecket automatiskt läggs till i den underliggande definitionen, till exempel:
> 
> * Kodvy: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Uttrycks redigerare: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Exempel 9*

Följande exempel använder ett XPath-uttryck för `'string(/*[name()="file"]/*[name()="location"])'` att hitta värdet i `<location></location>` noden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Här är resultatet: `Paris`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [språket för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md)
