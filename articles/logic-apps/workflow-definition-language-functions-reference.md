---
title: Referensguide för funktioner i uttryck
description: Referensguide till funktioner i uttryck för Azure Logic Apps och Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: d2ea08551299d66edd919a828877c134c84ef938
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477782"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referensguide för att använda funktioner i uttryck för Azure Logic Apps och Power Automate

För arbetsflödesdefinitioner [i Azure Logic Apps](../logic-apps/logic-apps-overview.md) [och Power Automate](/flow/getting-started) [](../logic-apps/logic-apps-workflow-definition-language.md#expressions) hämtar vissa uttryck sina värden från körningsåtgärder som kanske ännu inte finns när arbetsflödet börjar köras. Om du vill referera till dessa värden eller bearbeta värdena i dessa uttryck kan du använda *funktioner* som tillhandahålls av [Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Den här referenssidan gäller både för Azure Logic Apps och Power Automate, men visas i Azure Logic Apps dokumentationen. Även om den här sidan refererar specifikt till logikappar fungerar dessa funktioner för både flöden och logikappar. Mer information om funktioner och uttryck i Power Automate finns i [Använda uttryck i villkor](/flow/use-expressions-in-conditions).

Du kan till exempel beräkna värden med hjälp av matematiska funktioner, till exempel [funktionen add()](../logic-apps/workflow-definition-language-functions-reference.md#add) när du vill ha summan från heltal eller flyttal. Här är några andra exempeluppgifter som du kan utföra med funktioner:

| Uppgift | Funktionssyntax | Resultat |
| ---- | --------------- | ------ |
| Returnera en sträng i gemener. | toLower('<*text*>') <p>Till exempel: toLower('Hello') | "hello" |
| Returnera en globalt unik identifierare (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Om du vill [hitta funktioner baserat på deras allmänna syfte](#ordered-by-purpose)kan du granska följande tabeller. Detaljerad information om varje funktion finns i den [alfabetiska listan](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funktioner i uttryck

För att visa hur du använder en funktion i ett uttryck visar det här exemplet hur du kan hämta värdet från parametern och tilldela värdet till egenskapen med hjälp av `customerName` `accountName` funktionen [parameters()](#parameters) i ett uttryck:

```json
"accountName": "@parameters('customerName')"
```

Här är några andra allmänna sätt som du kan använda funktioner i uttryck:

| Uppgift | Funktionssyntax i ett uttryck |
| ---- | -------------------------------- |
| Utför arbete med ett objekt genom att skicka objektet till en funktion. | \@ < *"functionName*>(<*item*>)" |
| 1. Hämta *värdet för parameterName* med hjälp av den kapslade `parameters()` funktionen. </br>2. Utför arbete med resultatet genom att skicka värdet till *functionName*. | " \@ < *functionName*>(parameters('<*parameterName*>'))" |
| 1. Hämta resultatet från den kapslade inre funktionen *functionName*. </br>2. Skicka resultatet till den yttre funktionen *functionName2*. | " \@ < *functionName2*>(<*functionName*>(<*item*>))" |
| 1. Hämta resultatet från *functionName*. </br>2. Givet att resultatet är ett objekt med *egenskapen propertyName* hämtar du egenskapens värde. | " \@ < *functionName*>(<*item*>).<*propertyName*>" |
|||

Funktionen kan till `concat()` exempel ta två eller flera strängvärden som parametrar. Den här funktionen kombinerar dessa strängar till en sträng. Du kan antingen skicka in stränglitteraler, till exempel "Smith" och "String" så att du får en kombinerad sträng, "Owen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Eller så kan du hämta strängvärden från parametrar. I det här exemplet `parameters()` används funktionen i varje parameter och `concat()` `firstName` `lastName` parametrarna och . Sedan skickar du de resulterande strängarna till funktionen så att du får en `concat()` kombinerad sträng, till exempel "Owen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Oavsett vilket tilldelar båda exemplen resultatet till `customerName` egenskapen .

Här är några andra kommentarer om funktioner i uttryck:

* Funktionsparametrar utvärderas från vänster till höger.

* I syntaxen för parameterdefinitioner innebär ett frågetecken (?) som visas efter en parameter att parametern är valfri. Se till exempel [getFutureTime()](#getFutureTime).

I följande avsnitt ordnas funktioner baserat på deras allmänna syfte, eller så kan du bläddra bland funktionerna i [alfabetisk ordning.](#alphabetical-list)

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Strängfunktioner

Om du vill arbeta med strängar kan du använda dessa strängfunktioner och även vissa [samlingsfunktioner](#collection-functions). Strängfunktioner fungerar bara på strängar.

| Funktionen String | Uppgift |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinera två eller flera strängar och returnera den kombinerade strängen. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Kontrollera om en sträng slutar med den angivna delsträngen. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Returnera ett tal som en sträng baserat på det angivna formatet |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Returnera startpositionen för en delsträng. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Returnera startpositionen för den sista förekomsten av en delsträng. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Ersätta](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersätt en delsträng med den angivna strängen och returnera den uppdaterade strängen. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, från en större sträng baserat på ett angivet avgränsartecken i den ursprungliga strängen. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontrollera om en sträng börjar med en specifik delsträng. |
| [Delsträng](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Returnera tecken från en sträng med början från den angivna positionen. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Returnera en sträng i gemener. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Returnera en sträng i versaler. |
| [Trimma](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Ta bort inledande och avslutande blanksteg från en sträng och returnera den uppdaterade strängen. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Samlingsfunktioner

Om du vill arbeta med samlingar, vanligtvis matriser, strängar och ibland ordlistor, kan du använda dessa samlingsfunktioner.

| Samlingsfunktion | Uppgift |
| ------------------- | ---- |
| [Innehåller](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontrollera om en samling har ett visst objekt. |
| [tomt](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Kontrollera om en samling är tom. |
| [Första](../logic-apps/workflow-definition-language-functions-reference.md#first) | Returnera det första objektet från en samling. |
| [Korsningen](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Returnera en samling som bara *har gemensamma* objekt i de angivna samlingarna. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | I en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [Ansluta sig till](../logic-apps/workflow-definition-language-functions-reference.md#join) | Returnera en sträng som har *alla* objekt från en matris, avgränsade med det angivna tecknet. |
| [Senaste](../logic-apps/workflow-definition-language-functions-reference.md#last) | Returnera det sista objektet från en samling. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Hoppa över](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Ta bort objekt från framsidan av en samling och returnera *alla andra* objekt. |
| [Ta](../logic-apps/workflow-definition-language-functions-reference.md#take) | Returnera objekt från en samlings framsida. |
| [Unionen](../logic-apps/workflow-definition-language-functions-reference.md#union) | Returnera en samling som innehåller *alla* objekt från de angivna samlingarna. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funktioner för logisk jämförelse

Om du vill arbeta med villkor, jämföra värden och uttrycksresultat eller utvärdera olika typer av logik kan du använda dessa logiska jämförelsefunktioner. En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Om du använder logiska funktioner eller villkor för att jämföra värden konverteras null-värden till tomma strängvärden ( `""` ). Villkorsbeteendet skiljer sig åt när du jämför med en tom sträng i stället för ett null-värde. Mer information finns i [funktionen string().](#string) 

| Logisk jämförelsefunktion | Uppgift |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Kontrollera om alla uttryck är sanna. |
| [Motsvarar](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Kontrollera om båda värdena är likvärdiga. |
| [större än](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Kontrollera om det första värdet är större än det andra värdet. |
| [större än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Kontrollera om det första värdet är större än eller lika med det andra värdet. |
| [Om](../logic-apps/workflow-definition-language-functions-reference.md#if) | Kontrollera om ett uttryck är sant eller falskt. Returnera ett angivet värde baserat på resultatet. |
| [Mindre](../logic-apps/workflow-definition-language-functions-reference.md#less) | Kontrollera om det första värdet är mindre än det andra värdet. |
| [mindre än eller lika med](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Kontrollera om det första värdet är mindre än eller lika med det andra värdet. |
| [Inte](../logic-apps/workflow-definition-language-functions-reference.md#not) | Kontrollera om ett uttryck är falskt. |
| [eller](../logic-apps/workflow-definition-language-functions-reference.md#or) | Kontrollera om minst ett uttryck är sant. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konverteringsfunktioner

Om du vill ändra ett värdes typ eller format kan du använda dessa konverteringsfunktioner. Du kan till exempel ändra ett värde från ett booleskt värde till ett heltal. Mer information om hur Logic Apps hanterar innehållstyper under konverteringen finns i [Hantera innehållstyper.](../logic-apps/logic-apps-content-type.md) En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du använder dessa funktioner ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar endast funktionernas synlighet och inte deras effekt såvida du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från koden. Mer information finns i [Implicita datatypskonverteringar.](#implicit-data-conversions)

| Konverteringsfunktion | Uppgift |
| ------------------- | ---- |
| [matris](../logic-apps/workflow-definition-language-functions-reference.md#array) | Returnera en matris från en enda angiven indata. Flera indata finns i [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Returnera den base64-kodade versionen för en sträng. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Returnera den binära versionen för en base64-kodad sträng. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Returnera strängversionen för en base64-kodad sträng. |
| [Binära](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Returnera den binära versionen för ett indatavärde. |
| [boolesk](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Returnera den booleska versionen för ett indatavärde. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Returnera en matris från flera indata. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Returnera data-URI för ett indatavärde. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Returnera strängversionen för en data-URI. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Returnera strängversionen för en base64-kodad sträng. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Returnera den binära versionen för en data-URI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Returnera en sträng som ersätter escape-tecken med avkodade versioner. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecken med escape-tecken. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Returnera ett flyttalsnummer för ett indatavärde. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Returnera heltalsversionen för en sträng. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Returnera värdet JavaScript Object Notation JSON-typ (JSON) för en sträng eller XML. |
| [sträng](../logic-apps/workflow-definition-language-functions-reference.md#string) | Returnera strängversionen för ett indatavärde. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Returnera den URI-kodade versionen för ett indatavärde genom att ersätta url-osäkra tecken med escape-tecken. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Returnera strängversionen för en URI-kodad sträng. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Returnera XML-versionen för en sträng. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Implicita datatypskonverteringar

Azure Logic Apps automatiskt eller implicit konverterar mellan vissa datatyper, så du behöver inte utföra dessa konverteringar manuellt. Om du till exempel använder värden som inte är strängar där strängar förväntas som indata, Logic Apps automatiskt icke-strängvärdena till strängar.

Anta till exempel att en utlösare returnerar ett numeriskt värde som utdata:

`triggerBody()?['123']`

Om du använder dessa numeriska utdata där strängindata förväntas, till exempel en URL, konverterar Logic Apps automatiskt värdet till en sträng med hjälp av kparenteserna ( `{}` ) notation:

`@{triggerBody()?['123']}`

<a name="base64-encoding-decoding"></a>

### <a name="base64-encoding-and-decoding"></a>Base64-kodning och avkodning

Logic Apps utför base64-kodning eller avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av motsvarande funktioner:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Om du manuellt lägger till någon av dessa funktioner i arbetsflödet via Logikappdesignern, till exempel genom att använda uttrycksredigeraren, navigera bort från designern och gå tillbaka till designern, försvinner funktionen från designern och lämnar endast parametervärdena efter sig. Det här beteendet inträffar också om du väljer en utlösare eller åtgärd som använder den här funktionen utan att redigera funktionens parametervärden. Det här resultatet påverkar bara funktionens synlighet och inte effekten. I kodvyn påverkas inte funktionen. Men om du redigerar funktionens parametervärden tas både funktionen och dess effekt bort från kodvyn, och endast funktionens parametervärden finns kvar.

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematiska funktioner

Du kan använda de här matematikfunktionerna för att arbeta med heltal och flyttal.
En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Math-funktion | Uppgift |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Returnera resultatet från divisionen av två tal. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Returnera resten från division av två tal. |
| [multi](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Returnera produkten från att multiplicera två tal. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [Utbud](../logic-apps/workflow-definition-language-functions-reference.md#range) | Returnera en heltalsmatris som startar från ett angivet heltal. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Returnera resultatet från subtraktion av det andra talet från det första talet. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datum- och tidsfunktioner

Om du vill arbeta med datum och tider kan du använda dessa datum- och tidsfunktioner.
En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funktionen Datum eller tid | Uppgift |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Lägg till ett antal dagar till en tidsstämpel. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Lägg till ett antal timmar i en tidsstämpel. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Lägg till ett antal minuter i en tidsstämpel. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Lägg till ett antal sekunder till en tidsstämpel. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Lägg till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertera en tidsstämpel från Universal Time Coordinated (UTC) till måltidszonen. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konvertera en tidsstämpel från källtidszonen till måltidszonen. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertera en tidsstämpel från källtidszonen till Universal Time Coordinated (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Returnera dag i månadskomponenten från en tidsstämpel. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Returnera veckodagskomponenten från en tidsstämpel. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Returnera dag på årskomponenten från en tidsstämpel. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Returnera datumet från en tidsstämpel. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna. Se även [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Returnera dagens början för en tidsstämpel. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Returnerar timmens start för en tidsstämpel. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Returnera början av månaden för en tidsstämpel. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrahera ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Fästingar](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Returnera `ticks` egenskapsvärdet för en angiven tidsstämpel. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Returnera den aktuella tidsstämpeln som en sträng. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Arbetsflödesfunktioner

De här arbetsflödesfunktionerna kan hjälpa dig att:

* Få information om en arbetsflödesinstans vid körning.
* Arbeta med de indata som används för att instansiera logikappar eller flöden.
* Referera till utdata från utlösare och åtgärder.

Du kan till exempel referera till utdata från en åtgärd och använda dessa data i en senare åtgärd.
En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Arbetsflödesfunktion | Uppgift |
| ----------------- | ---- |
| [åtgärd](../logic-apps/workflow-definition-language-functions-reference.md#action) | Returnera den aktuella åtgärdens utdata vid körning eller värden från andra namn- och värdepar i JSON. Se även [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Returnera utdata för `body` en åtgärd vid körning. Se även [brödtext](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Returnera utdata för en åtgärd vid körning. Se [utdata](../logic-apps/workflow-definition-language-functions-reference.md#outputs) och [åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Åtgärder](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Returnera en åtgärds utdata vid körning eller värden från andra JSON-namn- och värdepar. Se även [åtgärden](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Kroppen](#body) | Returnera utdata för `body` en åtgärd vid körning. Se även [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Skapa en matris med de värden som matchar ett nyckelnamn i utdata från *formdata* eller *formulärkodade* åtgärder. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Returnera ett enda värde som matchar ett nyckelnamn i en åtgärds *formdata* eller *formulärkodade utdata*. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#item) | I en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration. |
| [Objekt](../logic-apps/workflow-definition-language-functions-reference.md#items) | I en Foreach- eller Until-loop returnerar du det aktuella objektet från den angivna loopen.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | I en Until-loop returnerar du indexvärdet för den aktuella iterationen. Du kan använda den här funktionen i kapslade Until-loopar. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Returnera den "motringning-URL" som anropar en utlösare eller åtgärd. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Returnera brödtexten för en viss del i en åtgärds utdata som har flera delar. |
| [Utgångar](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Returnera utdata för en åtgärd vid körning. |
| [Parametrar](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Returnera värdet för en parameter som beskrivs i arbetsflödesdefinitionen. |
| [Resultatet](../logic-apps/workflow-definition-language-functions-reference.md#result) | Returnera indata och utdata från åtgärderna på den översta nivån inuti den angivna begränsade åtgärden, till `For_each` exempel `Until` , och `Scope` . |
| [Utlösa](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Returnera utdata för en utlösare vid körning eller från andra JSON-namn- och värdepar. Se även [triggerOutputs](#triggerOutputs) och [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Returnera utdata för `body` en utlösare vid körning. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Returnera ett enda värde som matchar ett nyckelnamn *i utdata* från *formdata eller formulärkodade* utlösare. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Returnera brödtexten för en viss del i utdata för en utlösare med flera delar. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Skapa en matris vars värden matchar ett nyckelnamn i *utdata* från formdata *eller formulärkodade* utlösare. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Returnera utdata för en utlösare vid körning, eller värden från andra JSON-namn- och värdepar. Se [utlösaren](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variabler](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Returnera värdet för en angiven variabel. |
| [Arbetsflöde](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Returnera all information om själva arbetsflödet under körning. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-parsningsfunktioner

Om du vill arbeta med URI:er (Uniform Resource Identifiers) och hämta olika egenskapsvärden för dessa URI:er kan du använda dessa URI-parsningsfunktioner.
En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funktionen URI-parsning | Uppgift |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Returnera värdet `host` för en URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Returnera värdet `path` för en URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Returnera värdena `path` och `query` för en URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Returnera värdet `port` för en URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Returnera värdet `query` för en URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Returnera värdet `scheme` för en URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulationsfunktioner: JSON-& XML

Om du vill arbeta med JSON-objekt och XML-noder kan du använda dessa funktioner för manipulering.
En fullständig referens om varje funktion finns i den [alfabetiska listan](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulationsfunktion | Uppgift |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Lägg till en egenskap och dess värde, eller namn-värde-par, i ett JSON-objekt och returnera det uppdaterade objektet. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Returnera det första icke-null-värdet från en eller flera parametrar. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Ta bort en egenskap från ett JSON-objekt och returnera det uppdaterade objektet. |
| [Setproperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ange värdet för egenskapen för ett JSON-objekt och returnera det uppdaterade objektet. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Kontrollera XML för noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera matchande noder eller värden. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alla funktioner – alfabetisk lista

I det här avsnittet visas alla tillgängliga funktioner i alfabetisk ordning.

<a name="action"></a>

### <a name="action"></a>åtgärd

Returnera den *aktuella* åtgärdens utdata vid körning eller värden från andra JSON-namn- och värdepar som du kan tilldela till ett uttryck.
Som standard refererar den här funktionen till hela åtgärdsobjektet, men du kan även ange en egenskap vars värde du vill ha.
Se även [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Du kan bara använda `action()` funktionen på följande platser:

* Egenskapen `unsubscribe` för en webhook-åtgärd så att du kan komma åt resultatet från den ursprungliga `subscribe` begäran
* Egenskapen `trackedProperties` för en åtgärd
* `do-until`Loopvillkoret för en åtgärd

```
action()
action().outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Inga | Sträng | Namnet på **åtgärdsobjektets**-egenskap vars värde du vill ha: **name**, **startTime**, **endTime**, inputs , **outputs**, **status**, **code**, **trackingId** och **clientTrackingId**. I Azure Portal du dessa egenskaper genom att granska information om en specifik körningshistorik. Mer information finns i REST API [– Arbetsflödeskörningsåtgärder.](/rest/api/logic/workflowrunactions/get) |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-output*> | Sträng | Utdata från den aktuella åtgärden eller egenskapen |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Returnera utdata för `body` en åtgärd vid körning.
Förkortning för `actions('<actionName>').outputs.body` .
Se [body()](#body) och [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sträng | Utdata `body` från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar `body` utdata från Twitter-åtgärden `Get user` :

```
actionBody('Get_user')
```

Och returnerar det här resultatet:

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

Returnera utdata för en åtgärd vid körning.  och är förkortning för `actions('<actionName>').outputs` . Se [actions()](#actions). Funktionen `actionOutputs()` matchar i `outputs()` Logikappdesignern, så överväg att använda [outputs()](#outputs)i stället för `actionOutputs()` . Även om båda funktionerna fungerar på samma sätt är `outputs()` det bättre.

```
actionOutputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*Produktionen*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar utdata från Twitter-åtgärden `Get user` :

```
actionOutputs('Get_user')
```

Och returnerar det här resultatet:

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

Returnera en åtgärds utdata vid körning, eller värden från andra JSON-namn- och värdepar som du kan tilldela till ett uttryck. Som standard refererar funktionen till hela åtgärdsobjektet, men du kan även ange en egenskap vars värde du vill ha.
För förkortningar, se [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)och [body()](#body).
Information om den aktuella åtgärden finns [i action()](#action).

> [!TIP]
> Funktionen `actions()` returnerar utdata som en sträng. Om du behöver arbeta med ett returnerat värde som ett JSON-objekt måste du först konvertera strängvärdet. Du kan transformera strängvärdet till ett JSON-objekt med [åtgärden Parsa JSON.](logic-apps-perform-data-operations.md#parse-json-action)

> [!NOTE]
> Tidigare kunde du använda funktionen `actions()` eller elementet när du anger att en åtgärd `conditions` kördes baserat på utdata från en annan åtgärd. Men om du vill deklarera explicit beroenden mellan åtgärder måste du nu använda den beroende åtgärdens `runAfter` -egenskap.
> Mer information om egenskapen `runAfter` finns i Catch and handle failures with the [runAfter property](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdsobjektet vars utdata du vill ha  |
| <*Egenskapen*> | Inga | Sträng | Namnet på **åtgärdsobjektets**-egenskap vars värde du vill ha: **name**, **startTime**, **endTime**, inputs , **outputs**, **status**, **code**, **trackingId** och **clientTrackingId**. I Azure Portal du dessa egenskaper genom att granska information om en specifik körningshistorik. Mer information finns i REST API [– Arbetsflödeskörningsåtgärder.](/rest/api/logic/workflowrunactions/get) |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-output*> | Sträng | Utdata från den angivna åtgärden eller egenskapen |
||||

*Exempel*

Det här exemplet hämtar `status` egenskapsvärdet från `Get user` Twitter-åtgärden vid körning:

```
actions('Get_user').outputs.body.status
```

Och returnerar det här resultatet: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Returnera resultatet från att lägga till två tal.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Ja | Heltal, flyttal eller blandat | Talen som ska läggas till |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*result-sum*> | Heltal eller flyttal | Resultatet av att lägga till de angivna talen |
||||

*Exempel*

I det här exemplet läggs de angivna talen till:

```
add(1, 1.5)
```

Och returnerar det här resultatet: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Lägg till ett antal dagar till en tidsstämpel.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Dagar*> | Ja | Integer | Det positiva eller negativa antalet dagar som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet dagar  |
||||

*Exempel 1*

Det här exemplet lägger till 10 dagar till den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', 10)
```

Och returnerar det här resultatet: `"2018-03-25T00:00:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar det här resultatet: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Lägg till ett antal timmar i en tidsstämpel.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Timmar*> | Ja | Integer | Det positiva eller negativa antalet timmar som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Tidsstämpeln plus det angivna antalet timmar  |
||||

*Exempel 1*

Det här exemplet lägger till 10 timmar till den angivna tidsstämpeln:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Och returnerar det här resultatet: "2018-03-15T10:00:00.0000000Z"

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar det här resultatet: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Lägg till ett antal minuter till en tidsstämpel.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Minuter*> | Ja | Integer | Det positiva eller negativa antalet minuter som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus angivet antal minuter |
||||

*Exempel 1*

Det här exemplet lägger till 10 minuter till den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Och returnerar det här resultatet: `"2018-03-15T00:20:00.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem minuter från den angivna tidsstämpeln:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Och returnerar det här resultatet: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Lägg till en egenskap och dess värde, eller namn-värde-par, i ett JSON-objekt och returnera det uppdaterade objektet. Om egenskapen redan finns vid körning misslyckas funktionen och returnerar ett fel.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*Egenskapen*> | Ja | Sträng | Namnet på egenskapen som ska läggas till |
| <*Värde*> | Ja | Valfri | Värdet för egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet med den angivna egenskapen |
||||

Om du vill lägga till en överordnad egenskap i en befintlig egenskap `setProperty()` använder du funktionen , inte funktionen `addProperty()` . Annars returnerar funktionen endast det underordnade objektet som utdata.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet där du vill lägga till en egenskap |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen där du vill lägga till den underordnade egenskapen |
| <*underordnad-egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska läggas till |
| <*Värde*> | Ja | Valfri | Det värde som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

I det här exemplet läggs egenskapen till i ett JSON-objekt som konverteras från en sträng till JSON med `middleName` hjälp av funktionen [JSON().](#json) Objektet innehåller redan egenskaperna `firstName` `surName` och . Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

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

I det här exemplet läggs den underordnade egenskapen till i den befintliga egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av `middleName` `customerName` funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till den nya egenskapen och returnerar det uppdaterade objektet:

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

Lägg till ett antal sekunder till en tidsstämpel.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Sekunder*> | Ja | Integer | Det positiva eller negativa antalet sekunder som ska läggas till |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet sekunder  |
||||

*Exempel 1*

I det här exemplet läggs 10 sekunder till i den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Och returnerar det här resultatet: `"2018-03-15T00:00:10.0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem sekunder till den angivna tidsstämpeln:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Och returnerar det här resultatet: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Lägg till ett antal tidsenheter i en tidsstämpel.
Se även [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervallet*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Tidsstämpeln plus det angivna antalet tidsenheter  |
||||

*Exempel 1*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Och returnerar det här resultatet: `"2018-01-02T00:00:00.0000000Z"`

*Exempel 2*

I det här exemplet läggs en dag till i den angivna tidsstämpeln:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar resultatet med det valfria D-formatet: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>och

Kontrollera om alla uttryck är sanna.
Returnera true när alla uttryck är sanna eller returnera false när minst ett uttryck är falskt.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Boolesk | Uttrycken som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| sant eller falskt | Boolesk | Returnera true när alla uttryck är sanna. Returnera false när minst ett uttryck är falskt. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna booleska värdena är sanna:

```
and(true, true)
and(false, true)
and(false, false)
```

Och returnerar följande resultat:

* Första exemplet: Båda uttrycken är sanna, så returnerar `true` .
* Andra exemplet: Ett uttryck är falskt, så returnerar `false` .
* Tredje exemplet: Båda uttrycken är false, så returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om alla angivna uttryck är sanna:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Första exemplet: Båda uttrycken är sanna, så returnerar `true` .
* Andra exemplet: Ett uttryck är falskt, så returnerar `false` .
* Tredje exemplet: Båda uttrycken är false, så returnerar `false` .

<a name="array"></a>

### <a name="array"></a>matris

Returnera en matris från en enda angiven indata.
Flera indata finns i [createArray()](#createArray).

```
array('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen för att skapa en matris |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*värde*>] | Matris | En matris som innehåller de enskilda angivna indata |
||||

*Exempel*

I det här exemplet skapas en matris från strängen "hello":

```
array('hello')
```

Och returnerar det här resultatet: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Returnera den base64-kodade versionen för en sträng.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du ändå använder dessa funktioner kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar endast funktionernas synlighet och inte deras effekt såvida du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från koden. Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
base64('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Indatasträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*base64-string*> | Sträng | Den base64-kodade versionen för indatasträngen |
||||

*Exempel*

I det här exemplet konverteras "hello"-strängen till en base64-kodad sträng:

```
base64('hello')
```

Och returnerar det här resultatet: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Returnera den binära versionen för en base64-kodad sträng.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du använder de här funktionerna ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar bara funktionernas synlighet och inte deras effekt om du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från koden. Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
base64ToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den base64-kodade sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Sträng | Den binära versionen för den base64-kodade strängen |
||||

*Exempel*

I det här exemplet konverteras den base64-kodade strängen "aGVsbG8=" till en binär sträng:

```
base64ToBinary('aGVsbG8=')
```

Och returnerar det här resultatet:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Returnera strängversionen för en base64-kodad sträng, vilket effektivt avkodar base64-strängen. Använd den här funktionen i [stället för att avkodaBase64()](#decodeBase64), som är inaktuell.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du använder dessa funktioner ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar endast funktionernas synlighet och inte deras effekt såvida du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från koden. Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
base64ToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den base64-kodade strängen som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Sträng | Strängversionen för en base64-kodad sträng |
||||

*Exempel*

I det här exemplet konverteras den base64-kodade strängen "aGVsbG8=" till bara en sträng:

```
base64ToString('aGVsbG8=')
```

Och returnerar det här resultatet: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Returnera den binära versionen för en sträng.

```
binary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Sträng | Binärversionen för den angivna strängen |
||||

*Exempel*

I det här exemplet konverteras "hello"-strängen till en binär sträng:

```
binary('hello')
```

Och returnerar det här resultatet:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Returnera utdata för `body` en åtgärd vid körning. Förkortning för `actions('<actionName>').outputs.body` . Se [actionBody()](#actionBody) och [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens `body` utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*action-body-output*> | Sträng | Utdata `body` från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar `body` utdata från `Get user` Twitter-åtgärden:

```
body('Get_user')
```

Och returnerar det här resultatet:

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
| <*Värde*> | Ja | Valfri | Värdet som ska konverteras till booleskt. |
|||||

Om du använder med ett -objekt måste värdet för objektet vara en sträng eller `bool()` ett heltal som kan konverteras till booleskt värde.

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| `true` eller `false` | Boolesk | Den booleska versionen av det angivna värdet. |
||||

*Utdata*

De här exemplen visar de olika typer av indata som stöds för `bool()` :

| Indatavärde | Typ | Returvärde |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Integer | `true` |
| `bool(0)` | Integer    | `false` |
| `bool(-1)` | Integer | `true` |
| `bool('true')` | Sträng | `true` |
| `bool('false')` | Sträng | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Returnera det första icke-null-värdet från en eller flera parametrar.
Tomma strängar, tomma matriser och tomma objekt är inte null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Ja | Alla, kan blanda typer | Ett eller flera objekt att söka efter null |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Valfri | Det första objektet eller värdet som inte är null. Om alla parametrar är null returnerar den här funktionen null. |
||||

*Exempel*

De här exemplen returnerar det första icke-null-värdet från de angivna värdena, eller null när alla värden är null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exemplet: `"hello"`
* Tredje exemplet: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Kombinera två eller flera strängar och returnera den kombinerade strängen.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt när du använder funktionen med data som behöver kodning eller `concat()` avkodning:
> 
> * `concat('data:;base64,',<value>)`
> * `concat('data:,',encodeUriComponent(<value>))`
> 
> Men om du använder den här funktionen ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar endast funktionens synlighet och inte effekten såvida du inte redigerar funktionens parametervärden, som tar bort funktionen och effekten från koden. 
> Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ja | Sträng | Minst två strängar att kombinera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Sträng | Strängen som skapas från de kombinerade indatasträngarna |
||||

*Exempel*

I det här exemplet kombineras strängarna "Hello" och "World":

```
concat('Hello', 'World')
```

Och returnerar det här resultatet: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>innehåller

Kontrollera om en samling har ett visst objekt. Returnera true när objektet hittas eller returnera false när det inte hittas. Den här funktionen är fallkänslig.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Mer specifikt fungerar den här funktionen på dessa samlingstyper:

* En *sträng för* att hitta en *delsträng*
* En *matris för* att hitta ett *värde*
* En *ordlista för* att hitta en *nyckel*

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matris eller ordlista | Samlingen som ska kontrolleras |
| <*Värde*> | Ja | Sträng, matris eller ordlista | Objektet som ska sökas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när objektet hittas. Returnera false om det inte hittas. |
||||

*Exempel 1*

Det här exemplet kontrollerar strängen "hello world" för delsträngen "world" och returnerar true:

```
contains('hello world', 'world')
```

*Exempel 2*

Det här exemplet kontrollerar strängen "hello world" för delsträngen "universe" och returnerar false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konvertera en tidsstämpel från UTC (Universal Time Coordinated) till måltidszonen.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på måltidszonen. Information om tidszonsnamn finns [i Standardtidszoner](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)i Microsoft Windows, men du kan behöva ta bort eventuella skiljetecken från tidszonens namn. |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverteras till måltidszonen |
||||

*Exempel 1*

I det här exemplet konverteras en tidsstämpel till den angivna tidszonen:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Och returnerar det här resultatet: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

I det här exemplet konverteras en tidsstämpel till den angivna tidszonen och formatet:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Och returnerar det här resultatet: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konvertera en tidsstämpel från källtidszonen till måltidszonen.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | Sträng | Namnet på källtidszonen. Information om tidszonsnamn finns [i Standardtidszoner](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)i Microsoft Windows, men du kan behöva ta bort eventuella skiljetecken från tidszonens namn. |
| <*destinationTimeZone*> | Ja | Sträng | Namnet på måltidszonen. Information om tidszonsnamn finns i Standardtidszoner i [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)men du kan behöva ta bort eventuella skiljetecken från tidszonens namn. |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverteras till måltidszonen |
||||

*Exempel 1*

Det här exemplet konverterar källtidszonen till måltidszonen:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Och returnerar det här resultatet: `"2018-01-01T00:00:00.0000000"`

*Exempel 2*

Det här exemplet konverterar en tidszon till den angivna tidszonen och formatet:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Och returnerar det här resultatet: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konvertera en tidsstämpel från källtidszonen till UTC (Universal Time Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*sourceTimeZone*> | Ja | Sträng | Namnet på källtidszonen. Information om tidszonsnamn finns i Standardtidszoner i [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)men du kan behöva ta bort eventuella skiljetecken från tidszonens namn. |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*konverterad tidsstämpel*> | Sträng | Tidsstämpeln konverteras till UTC |
||||

*Exempel 1*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Och returnerar det här resultatet: `"2018-01-01T08:00:00.0000000Z"`

*Exempel 2*

I det här exemplet konverteras en tidsstämpel till UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Och returnerar det här resultatet: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Returnera en matris från flera indata.
För enskilda indatamatriser, se [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Ja | Alla, men inte blandade | Minst två objekt för att skapa matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Matris | Matrisen som skapats från alla indataobjekt |
||||

*Exempel*

Det här exemplet skapar en matris från dessa indata:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Och returnerar det här resultatet: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Returnera en data-URI (Uniform Resource Identifier) för en sträng.

```
dataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*data-uri*> | Sträng | Data-URI för indatasträngen |
||||

*Exempel*

I det här exemplet skapas en data-URI för "hello"-strängen:

```
dataUri('hello')
```

Och returnerar det här resultatet: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Returnera den binära versionen för en URI (Data Uniform Resource Identifier).
Använd den här funktionen i stället [för decodeDataUri()](#decodeDataUri).
Även om båda funktionerna fungerar på samma sätt är `dataUriBinary()` det bättre.

```
dataUriToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den data-URI som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Sträng | Den binära versionen för data-URI |
||||

*Exempel*

I det här exemplet skapas en binär version för den här data-URI:en:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar det här resultatet:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Returnera strängversionen för en URI (Data Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den data-URI som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Sträng | Strängversion för data-URI |
||||

*Exempel*

I det här exemplet skapas en sträng för den här data-URI:en:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar det här resultatet: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Returnera dagen i månaden från en tidsstämpel.

```
dayOfMonth('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag i månaden*> | Integer | Dagen i månaden från den angivna tidsstämpeln |
||||

*Exempel*

Det här exemplet returnerar talet för dagen i månaden från den här tidsstämpeln:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Och returnerar det här resultatet: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Returnera veckodagen från en tidsstämpel.

```
dayOfWeek('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*veckodag*> | Integer | Veckodagen från den angivna tidsstämpeln där söndag är 0, måndag är 1 och så vidare |
||||

*Exempel*

Det här exemplet returnerar talet för veckodagen från den här tidsstämpeln:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Och returnerar det här resultatet: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Returnera dagen på året från en tidsstämpel.

```
dayOfYear('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*dag på året*> | Integer | Dagen på året från den angivna tidsstämpeln |
||||

*Exempel*

Det här exemplet returnerar antalet dagar på året från den här tidsstämpeln:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Och returnerar det här resultatet: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (inaktuell)

Den här funktionen är inaktuell, så använd [base64ToString()](#base64ToString) i stället.

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Returnera den binära versionen för en URI (Data Uniform Resource Identifier). Överväg att [använda dataUriToBinary()](#dataUriToBinary)i stället för `decodeDataUri()` . Även om båda funktionerna fungerar på samma sätt är `dataUriToBinary()` det bättre.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du använder de här funktionerna ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar bara funktionernas synlighet och inte deras effekt om du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från koden. Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
decodeDataUri('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den data-URI-sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Sträng | Den binära versionen för en data-URI-sträng |
||||

*Exempel*

Det här exemplet returnerar den binära versionen för den här data-URI:en:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Och returnerar det här resultatet:

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
| <*Värde*> | Ja | Sträng | Strängen med escape-tecken som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Sträng | Den uppdaterade strängen med de avkodade escape-tecknen |
||||

*Exempel*

Det här exemplet ersätter escape-tecknen i den här strängen med avkodade versioner:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

Och returnerar det här resultatet: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Returnera resultatet från divisionen av två tal. För att få resten av resultatet, se [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Utdelning*> | Ja | Heltal eller flyttal | Talet som ska dividera med *nämnaren* |
| <*Divisor*> | Ja | Heltal eller flyttal | Talet som delar upp t *dividenden* men inte får vara 0 |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Heltal eller flyttal | Resultatet från divisionen av det första talet med det andra talet. Om antingen t dividend eller divisor har flyttalstyp har resultatet Flyttalstyp. <p><p>**Obs!** Om du vill konvertera flyttalsresultatet till ett heltal provar [du att skapa och anropa en funktion i Azure](../logic-apps/logic-apps-azure-functions.md) från logikappen. |
||||

*Exempel 1*

Båda exemplen returnerar det här värdet med heltalstypen: `2`

```
div(10,5)
div(11,5)
```

*Exempel 2*

Båda exemplen returnerar det här värdet med flyttalstyp: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en URI-kodad version (Uniform Resource Identifier) för en sträng genom att ersätta URL-osäkra tecken med escape-tecken. Överväg att [använda uriComponent()](#uriComponent)i stället för `encodeUriComponent()` . Även om båda funktionerna fungerar på samma sätt är `uriComponent()` det bättre.

> [!NOTE]
> Azure Logic Apps utför base64-kodning och avkodning automatiskt eller implicit, så du behöver inte utföra dessa konverteringar manuellt med hjälp av kodnings- och avkodningsfunktioner. Men om du använder de här funktionerna ändå i designern kan du uppleva oväntade renderingsbeteenden i designern. Dessa beteenden påverkar bara funktionernas synlighet och inte deras effekt såvida du inte redigerar funktionernas parametervärden, vilket tar bort funktionerna och deras effekter från din kod. Mer information finns i [Base64-kodning och avkodning.](#base64-encoding-decoding)

```
encodeUriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
encodeUriComponent('https://contoso.com')
```

Och returnerar det här resultatet: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>tomt

Kontrollera om en samling är tom.
Returnera true när samlingen är tom eller returnera false när den inte är tom.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng, matris eller objekt | Den samling som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när samlingen är tom. Returnera false om det inte är tomt. |
||||

*Exempel*

De här exemplen kontrollerar om de angivna samlingarna är tomma:

```
empty('')
empty('abc')
```

Och returnerar följande resultat:

* Första exemplet: Skickar en tom sträng, så funktionen returnerar `true` .
* Andra exemplet: Skickar strängen "abc", så funktionen returnerar `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Kontrollera om en sträng slutar med en specifik delsträng.
Returnera true när delsträngen hittas, eller returnera false när det inte går att hitta.
Den här funktionen är inte fallkänslig.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska kontrolleras |
| <*searchText*> | Ja | Sträng | Den avslutande delsträngen som ska sökas efter |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera true när den avslutande delsträngen hittas. Returnera false när det inte går att hitta. |
||||

*Exempel 1*

Det här exemplet kontrollerar om "hello world"-strängen slutar med "world"-strängen:

```
endsWith('hello world', 'world')
```

Och returnerar det här resultatet: `true`

*Exempel 2*

Det här exemplet kontrollerar om "hello world"-strängen slutar med strängen "universe":

```
endsWith('hello world', 'universe')
```

Och returnerar det här resultatet: `false`

<a name="equals"></a>

### <a name="equals"></a>lika med

Kontrollera om båda värdena, uttrycken eller objekten är likvärdiga.
Returnera true när båda är likvärdiga, eller returnera false när de inte är likvärdiga.

```
equals('<object1>', '<object2>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Ja | Olika | Värden, uttryck eller objekt som ska jämföras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när båda är likvärdiga. Returnera false om det inte är likvärdigt. |
||||

*Exempel*

De här exemplen kontrollerar om de angivna indata är likvärdiga.

```
equals(true, 1)
equals('abc', 'abcd')
```

Och returnerar följande resultat:

* Första exemplet: Båda värdena är likvärdiga, så funktionen returnerar `true` .
* Andra exemplet: Båda värdena är inte likvärdiga, så funktionen returnerar `false` .

<a name="first"></a>

### <a name="first"></a>Första

Returnera det första objektet från en sträng eller matris.

```
first('<collection>')
first([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen där det första objektet finns |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Valfri | Det första objektet i samlingen |
||||

*Exempel*

De här exemplen hittar det första objektet i dessa samlingar:

```
first('hello')
first(createArray(0, 1, 2))
```

Och returnerar följande resultat:

* Första exemplet: `"h"`
* Andra exemplet: `0`

<a name="float"></a>

### <a name="float"></a>flyt

Konvertera en strängversion för ett flyttalsnummer till ett faktiskt flyttalsnummer.
Du kan bara använda den här funktionen när du vidarebefordrar anpassade parametrar till en app, till exempel en logikapp eller ett flöde.

```
float('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som har ett giltigt flyttalsnummer att konvertera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Flyttalsnummer för den angivna strängen |
||||

*Exempel*

Det här exemplet skapar en strängversion för det här flyttalsnumret:

```
float('10.333')
```

Och returnerar det här resultatet: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Returnera en tidsstämpel i det angivna formatet.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterad tidsstämpel*> | Sträng | Den uppdaterade tidsstämpeln i det angivna formatet |
||||

*Exempel*

I det här exemplet konverteras en tidsstämpel till det angivna formatet:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Och returnerar det här resultatet: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Returnera en matris med värden som matchar ett nyckelnamn i en åtgärds *formulärdata* eller *formulärkodade utdata.*

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Den åtgärd vars utdata har det nyckelvärde som du vill använda |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris-med-nyckel-värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

I det här exemplet skapas en matris från ämnesnyckelns värde i den angivna åtgärdens formdata eller formulärkodade utdata:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Och returnerar ämnestexten i en matris, till exempel: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Returnera ett enda värde som matchar ett nyckelnamn i en åtgärds *formulärdata* eller *formulärkodade utdata.*
Om funktionen hittar fler än en matchning returnerar funktionen ett fel.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Den åtgärd vars utdata har det nyckelvärde som du vill använda |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckelvärde*> | Sträng | Värdet i den angivna nyckeln  |
||||

*Exempel*

I det här exemplet skapas en sträng från värdet för "Ämne"-nyckeln i den angivna åtgärdens formulärdata eller formulärkodade utdata:

```
formDataValue('Send_an_email', 'Subject')
```

Och returnerar ämnestexten som en sträng, till exempel: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Returnera ett tal som en sträng som baseras på det angivna formatet.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nummer*> | Ja | Heltal eller Double | Det värde som du vill formatera. |
| <*Format*> | Ja | Sträng | En sammansatt formatsträng som anger vilket format du vill använda. Information om de numeriska formatsträngar som stöds finns [i Standard numeriska formatsträngar](/dotnet/standard/base-types/standard-numeric-format-strings), som stöds av `number.ToString(<format>, <locale>)` . |
| <*Locale*> | Inga | Sträng | De språk som ska användas som stöds av `number.ToString(<format>, <locale>)` . Om inget värde anges är standardvärdet `en-us` . |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*formaterat tal*> | Sträng | Det angivna talet som en sträng i det format som du angav. Du kan skicka det här returvärdet till `int` en eller `float` . |
||||

*Exempel 1*

Anta att du vill formatera talet `1234567890` . I det här exemplet formateras talet som strängen "1,234,567,890.00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Exempel 2"

Anta att du vill formatera talet `1234567890` . I det här exemplet formateras talet till strängen "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Exempel 3*

Anta att du vill formatera talet `17.35` . I det här exemplet formateras talet till strängen "$17.35".

```
formatNumber(17.35, 'C2')
```

*Exempel 4*

Anta att du vill formatera talet `17.35` . I det här exemplet formateras talet till strängen "17,35 kr".

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter som ska läggas till |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervallet*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Den aktuella tidsstämpeln plus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.0000000Z".
Det här exemplet lägger till fem dagar till den tidsstämpeln:

```
getFutureTime(5, 'Day')
```

Och returnerar det här resultatet: `"2018-03-06T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-03-01T00:00:00.0000000Z".
Det här exemplet lägger till fem dagar och konverterar resultatet till "D"-format:

```
getFutureTime(5, 'Day', 'D')
```

Och returnerar det här resultatet: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter att subtrahera |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervallet*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den aktuella tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.0000000Z".
Det här exemplet subtraherar fem dagar från den tidsstämpeln:

```
getPastTime(5, 'Day')
```

Och returnerar det här resultatet: `"2018-01-27T00:00:00.0000000Z"`

*Exempel 2*

Anta att den aktuella tidsstämpeln är "2018-02-01T00:00:00.0000000Z".
Det här exemplet subtraherar fem dagar och konverterar resultatet till "D"-format:

```
getPastTime(5, 'Day', 'D')
```

Och returnerar det här resultatet: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>större än

Kontrollera om det första värdet är större än det andra värdet.
Returnera true när det första värdet är mer, eller returnera false när det är mindre.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om det är större än det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal eller sträng | Jämförelsevärdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när det första värdet är större än det andra värdet. Returnera false när det första värdet är lika med eller mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större än det andra värdet:

```
greater(10, 5)
greater('apple', 'banana')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exemplet: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>större än eller lika med

Kontrollera om det första värdet är större än eller lika med det andra värdet.
Returnera true när det första värdet är större eller lika med, eller returnera false när det första värdet är mindre.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om större än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal eller sträng | Jämförelsevärdet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när det första värdet är större än eller lika med det andra värdet. Returnera false när det första värdet är mindre än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är större än eller lika med det andra värdet:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exemplet: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Generera en globalt unik identifierare (GUID) som en sträng, till exempel "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Du kan också ange ett annat format för GUID än standardformatet " D", som är 32 siffror avgränsade med bindestreck.

```
guid('<format>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Inga | Sträng | En enda [formatspecificerare](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) för det returnerade GUID:t. Som standard är formatet "D", men du kan använda "N", "D", "B", "P" eller "X". |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*GUID-värde*> | Sträng | Ett slumpmässigt genererat GUID |
||||

*Exempel*

Det här exemplet genererar samma GUID, men som 32 siffror avgränsade med bindestreck och omslutna inom parentes:

```
guid('P')
```

Och returnerar det här resultatet: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>om

Kontrollera om ett uttryck är sant eller falskt. Returnera ett angivet värde baserat på resultatet. Parametrar utvärderas från vänster till höger.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck*> | Ja | Boolesk | Uttrycket som ska kontrolleras |
| <*valueIfTrue*> | Ja | Valfri | Värdet som ska returneras när uttrycket är sant |
| <*valueIfFalse*> | Ja | Valfri | Värdet som ska returneras när uttrycket är falskt |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Valfri | Det angivna värdet som returneras baserat på om uttrycket är sant eller falskt |
||||

*Exempel*

Det här exemplet `"yes"` returneras eftersom det angivna uttrycket returnerar true.
Annars returnerar exemplet `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Returnera startpositionen eller indexvärdet för en delsträng.
Den här funktionen är inte fallkänslig och index börjar med talet 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen att hitta |
| <*searchText*> | Ja | Sträng | Delsträngen att hitta |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Startpositionen eller indexvärdet för den angivna delsträngen. <p>Om strängen inte hittas returnerar du talet -1. |
||||

*Exempel*

Det här exemplet hittar startindexvärdet för delsträngen "world" i strängen "hello world":

```
indexOf('hello world', 'world')
```

Och returnerar det här resultatet: `6`

<a name="int"></a>

### <a name="int"></a>int

Returnera heltalsversionen för en sträng.

```
int('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*heltalsresultat*> | Integer | Heltalsversionen för den angivna strängen |
||||

*Exempel*

I det här exemplet skapas en heltalsversion för strängen "10":

```
int('10')
```

Och returnerar det här resultatet: `10`

<a name="item"></a>

### <a name="item"></a>objekt

När det används i en upprepad åtgärd över en matris returnerar du det aktuella objektet i matrisen under åtgärdens aktuella iteration.
Du kan också hämta värden från objektets egenskaper.

```
item()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Valfri | Det aktuella objektet i matrisen för åtgärdens aktuella iteration |
||||

*Exempel*

Det här exemplet hämtar elementet från det aktuella meddelandet för åtgärden "Send_an_email" i en `body` for-each-loops aktuella iteration:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Objekt

Returnera det aktuella objektet från varje cykel i en for-each-loop.
Använd den här funktionen i for-each-loopen.

```
items('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Ja | Sträng | Namnet på for-each-loopen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Objekt*> | Valfri | Objektet från den aktuella cykeln i den angivna for-each-loopen |
||||

*Exempel*

Det här exemplet hämtar det aktuella objektet från den angivna for-each-loopen:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Returnera indexvärdet för den aktuella iterationen inuti en Until-loop. Du kan använda den här funktionen i kapslade Until-loopar. 

```
iterationIndexes('<loopName>')
```

| Parameter | Krävs | Typ | Beskrivning | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ja | Sträng | Namnet på Until-loopen | 
||||| 

| Returvärde | Typ | Beskrivning | 
| ------------ | ---- | ----------- | 
| <*Index*> | Integer | Indexvärdet för den aktuella iterationen inuti den angivna Until-loopen | 
|||| 

*Exempel* 

Det här exemplet skapar en räknarvariabel och ökar variabeln med en under varje iteration i en Until-loop tills räknarvärdet når fem. Exemplet skapar också en variabel som spårar det aktuella indexet för varje iteration. I Until-loopen, under varje iteration, ökar exemplet räknaren och tilldelar sedan räknarvärdet till det aktuella indexvärdet och ökar sedan räknaren. I loopen refererar det här exemplet till det aktuella iterationsindexet med hjälp av `iterationIndexes` funktionen :

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

Returnera JavaScript Object Notation (JSON) typvärde, objekt eller matris med objekt för en sträng eller XML.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Utan ett XML-schema som definierar utdatastrukturen kan funktionen returnera resultat där strukturen skiljer sig avsevärt från det förväntade formatet, beroende på indata.
>  
> Det här beteendet gör den här funktionen olämplig för scenarier där utdata måste överensstämma med ett väldefinierat kontrakt, till exempel i kritiska affärssystem eller lösningar.

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng eller XML | Strängen eller XML som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*JSON-resultat*> | Intern JSON-typ, -objekt eller -matris | Det inbyggda JSON-typvärdet, -objektet eller -matrisen med objekt från indatasträngen eller XML-filen. <p><p>- Om du skickar in XML som har ett enda underobjekt i rotelementet returnerar funktionen ett enda JSON-objekt för det underordnade elementet. <p> - Om du skickar in XML som har flera underordnade element i rotelementet returnerar funktionen en matris som innehåller JSON-objekt för de underordnade elementen. <p>– Om strängen är null returnerar funktionen ett tomt objekt. |
||||

*Exempel 1*

Det här exemplet konverterar den här strängen till ett JSON-värde:

```
json('[1, 2, 3]')
```

Och returnerar det här resultatet: `[1, 2, 3]`

*Exempel 2*

Det här exemplet konverterar den här strängen till JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Och returnerar det här resultatet:

```json
{
  "fullName": "Sophia Owen"
}
```

*Exempel 3*

I det här exemplet används funktionerna och för att konvertera XML som har ett enda underobjekt i rotelementet till ett `json()` `xml()` JSON-objekt med namnet `person` för det underordnade elementet:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

Och returnerar det här resultatet:

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

I det här exemplet används funktionerna och för att konvertera XML som har flera underordnade element i rotelementet till en matris med namnet som innehåller `json()` `xml()` `person` JSON-objekt för dessa underordnade element:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

Och returnerar det här resultatet:

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

### <a name="intersection"></a>Korsningen

Returnera en samling som bara *har gemensamma* objekt i de angivna samlingarna.
För att visas i resultatet måste ett objekt visas i alla samlingar som skickas till den här funktionen.
Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Ja | Matris eller objekt, men inte båda | De samlingar som du bara vill *använda* gemensamma objekt från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*common-items*> | Matris eller objekt | En samling som bara har gemensamma objekt i de angivna samlingarna |
||||

*Exempel*

Det här exemplet hittar vanliga objekt i dessa matriser:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Och returnerar en matris med *endast* dessa objekt: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Returnera en sträng som har alla objekt från en matris och där varje tecken avgränsas med *en avgränsare*.

```
join([<collection>], '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Matrisen som har de objekt som ska anslutas |
| <*Avgränsare*> | Ja | Sträng | Avgränsaren som visas mellan varje tecken i den resulterande strängen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*char1* >< *avgränsare* >< *char2* >< *avgränsare*>... | Sträng | Den resulterande strängen som skapats från alla objekt i den angivna matrisen |
||||

*Exempel*

Det här exemplet skapar en sträng från alla objekt i den här matrisen med det angivna tecknet som avgränsare:

```
join(createArray('a', 'b', 'c'), '.')
```

Och returnerar det här resultatet: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>Senaste

Returnera det sista objektet från en samling.

```
last('<collection>')
last([<collection>])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Samlingen där det sista objektet finns |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Sträng eller matris | Det sista objektet i samlingen |
||||

*Exempel*

De här exemplen hittar det sista objektet i dessa samlingar:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Och returnerar följande resultat:

* Första exemplet: `"d"`
* Andra exemplet: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Returnera startpositionen eller indexvärdet för den sista förekomsten av en delsträng. Den här funktionen är inte fallkänslig och index börjar med talet 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen för att hitta |
| <*searchText*> | Ja | Sträng | Delsträngen för att hitta |
|||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slutindexvärde*> | Integer | Startpositionen eller indexvärdet för den sista förekomsten av den angivna delsträngen. |
|||

Om strängen eller delsträngsvärdet är tomt inträffar följande:

* Om bara strängvärdet är tomt returnerar funktionen `-1` .

* Om både sträng- och delsträngsvärdena är tomma returnerar funktionen `0` .

* Om bara delsträngsvärdet är tomt returnerar funktionen stränglängden minus 1.

*Exempel*

Det här exemplet hittar startindexvärdet för den sista förekomsten av delsträngens `world` delsträng i strängen `hello world hello world` . Det returnerade resultatet är `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

Det här exemplet saknar delsträngsparametern och returnerar värdet för eftersom värdet för indatasträngen `22` ( `23` ) minus 1 är större än 0.

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
| <*Samling*> | Ja | Sträng eller matris | Samlingen med de objekt som ska räknas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Antalet objekt i samlingen |
||||

*Exempel*

I de här exemplen räknas antalet objekt i dessa samlingar:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Och returnerar det här resultatet: `4`

<a name="less"></a>

### <a name="less"></a>mindre än

Kontrollera om det första värdet är mindre än det andra värdet.
Returnera true när det första värdet är mindre, eller returnera false när det första värdet är mer.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om det är mindre än det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal eller sträng | Jämförelseobjektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när det första värdet är mindre än det andra värdet. Returnera false när det första värdet är lika med eller större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre än det andra värdet.

```
less(5, 10)
less('banana', 'apple')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exemplet: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>mindre än eller lika med

Kontrollera om det första värdet är mindre än eller lika med det andra värdet.
Returnera true när det första värdet är mindre än eller lika med, eller returnera false när det första värdet är större.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Heltal, flyttal eller sträng | Det första värdet för att kontrollera om mindre än eller lika med det andra värdet |
| <*compareTo*> | Ja | Heltal, flyttal eller sträng | Jämförelseobjektet |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera true när det första värdet är mindre än eller lika med det andra värdet. Returnera false när det första värdet är större än det andra värdet. |
||||

*Exempel*

De här exemplen kontrollerar om det första värdet är mindre än eller lika med det andra värdet.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exemplet: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Returnera den "motringning-URL" som anropar en utlösare eller åtgärd.
Den här funktionen fungerar bara med utlösare och åtgärder för anslutningstyperna **HttpWebhook** och **ApiConnectionWebhook,** men inte typerna **Manuell,** **Upprepning,** **HTTP** **och APIConnection.**

```
listCallbackUrl()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Sträng | Motringning-URL:en för en utlösare eller åtgärd |
||||

*Exempel*

Det här exemplet visar ett exempel på en återanrops-URL som den här funktionen kan returnera:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Returnera det högsta värdet från en lista eller matris med tal som är inkluderande i båda ändar.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Heltal, flyttal eller båda | Den uppsättning tal som du vill ha det högsta värdet från |
| [<*number1*>, <*number2*>, ...] | Ja | Matris – heltal, flyttal eller båda | Matrisen med tal som du vill ha det högsta värdet från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*maxvärde*> | Heltal eller flyttal | Det högsta värdet i den angivna matrisen eller uppsättningen tal |
||||

*Exempel*

De här exemplen hämtar det högsta värdet från uppsättningen tal och matrisen:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Och returnerar det här resultatet: `3`

<a name="min"></a>

### <a name="min"></a>min

Returnera det lägsta värdet från en uppsättning tal eller en matris.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Heltal, flyttal eller båda | Den uppsättning tal som du vill ha det lägsta värdet från |
| [<*number1*>, <*number2*>, ...] | Ja | Matris – heltal, flyttal eller båda | Matrisen med tal som du vill ha det lägsta värdet från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*minsta värde*> | Heltal eller flyttal | Det lägsta värdet i den angivna uppsättningen tal eller angiven matris |
||||

*Exempel*

De här exemplen får det lägsta värdet i uppsättningen tal och matrisen:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Och returnerar det här resultatet: `1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Returnera resten från divisionen av två tal.
Information om hur du hämtar heltalsresultatet finns [i div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Utdelning*> | Ja | Heltal eller flyttal | Talet som ska dividera med *nämnaren* |
| <*Divisor*> | Ja | Heltal eller flyttal | Talet som delar upp t *dividenden*, men som inte får vara 0. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Heltal eller flyttal | Resten från division av det första talet med det andra talet |
||||

*Exempel*

I det här exemplet dividerar vi det första talet med det andra talet:

```
mod(3, 2)
```

Och returnerar det här resultatet: `1`

<a name="mul"></a>

### <a name="mul"></a>multi

Returnera produkten från att multiplicera två tal.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Heltal eller flyttal | Det tal som multipliceras *med multiplicand2* |
| <*multiplicand2*> | Ja | Heltal eller flyttal | Det tal som multiplar *multiplicerat1* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*produktresultat*> | Heltal eller flyttal | Produkten från att multiplicera det första talet med det andra talet |
||||

*Exempel*

De här exemplen är flera av de första talen med det andra talet:

```
mul(1, 2)
mul(1.5, 2)
```

Och returnerar följande resultat:

* Första exemplet: `2`
* Andra exemplet `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Returnera brödtexten för en viss del i en åtgärds utdata som har flera delar.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärden som har utdata med flera delar |
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Kroppen*> | Sträng | Brödtexten för den angivna delen |
||||

<a name="not"></a>

### <a name="not"></a>inte

Kontrollera om ett uttryck är falskt.
Returnera true när uttrycket är falskt eller returnera false när sant.

```json
not(<expression>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uttryck*> | Ja | Boolesk | Uttrycket som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när uttrycket är falskt. Returnera false när uttrycket är sant. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(false)
not(true)
```

Och returnerar följande resultat:

* Första exemplet: Uttrycket är falskt, så funktionen returnerar `true` .
* Andra exemplet: Uttrycket är sant, så funktionen returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om de angivna uttrycken är falska:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Och returnerar följande resultat:

* Första exemplet: Uttrycket är falskt, så funktionen returnerar `true` .
* Andra exemplet: Uttrycket är sant, så funktionen returnerar `false` .

<a name="or"></a>

### <a name="or"></a>eller

Kontrollera om minst ett uttryck är sant.
Returnera true när minst ett uttryck är sant eller returnera false när alla är false.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Boolesk | Uttrycken som ska kontrolleras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när minst ett uttryck är sant. Returnera false när alla uttryck är false. |
||||

*Exempel 1*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(true, false)
or(false, false)
```

Och returnerar följande resultat:

* Första exemplet: Minst ett uttryck är sant, så funktionen returnerar `true` .
* Andra exemplet: Båda uttrycken är false, så funktionen returnerar `false` .

*Exempel 2*

De här exemplen kontrollerar om minst ett uttryck är sant:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Och returnerar följande resultat:

* Första exemplet: Minst ett uttryck är sant, så funktionen returnerar `true` .
* Andra exemplet: Båda uttrycken är false, så funktionen returnerar `false` .

<a name="outputs"></a>

### <a name="outputs"></a>Utgångar

Returnera en åtgärds utdata vid körning. Använd den här funktionen i stället `actionOutputs()` för , som matchar i `outputs()` Logikappdesignern. Även om båda funktionerna fungerar på samma sätt är `outputs()` det bättre.

```
outputs('<actionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | Sträng | Namnet på åtgärdens utdata som du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| <*Produktionen*> | Sträng | Utdata från den angivna åtgärden |
||||

*Exempel*

Det här exemplet hämtar utdata från Twitter-åtgärden `Get user` :

```
outputs('Get_user')
```

Och returnerar det här resultatet:

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

Returnera värdet för en parameter som beskrivs i arbetsflödesdefinitionen.

```
parameters('<parameterName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | Sträng | Namnet på den parameter vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Valfri | Värdet för den angivna parametern |
||||

*Exempel*

Anta att du har det här JSON-värdet:

```json
{
  "fullName": "Sophia Owen"
}
```

Det här exemplet hämtar värdet för den angivna parametern:

```
parameters('fullName')
```

Och returnerar det här resultatet: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Returnera ett slumpmässigt heltal från ett angivet intervall, som endast är inkluderande i början.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Integer | Det lägsta heltal i intervallet |
| <*Maxvalue*> | Ja | Integer | Det heltal som följer det högsta heltal i intervallet som funktionen kan returnera |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slumpmässigt resultat*> | Integer | Det slumpmässiga heltal som returneras från det angivna intervallet |
||||

*Exempel*

Det här exemplet hämtar ett slumpmässigt heltal från det angivna intervallet, exklusive det högsta värdet:

```
rand(1, 5)
```

Och returnerar ett av dessa tal som resultat: `1` `2` , , `3` eller `4`

<a name="range"></a>

### <a name="range"></a>Utbud

Returnera en heltalsmatris som startar från ett angivet heltal.

```
range(<startIndex>, <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Ja | Integer | Ett heltalsvärde som startar matrisen som det första objektet |
| <*Räkna*> | Ja | Integer | Antalet heltal i matrisen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*intervallresultat>]* | Matris | Matrisen med heltal som börjar från det angivna indexet |
||||

*Exempel*

Det här exemplet skapar en heltalsmatris som startar från det angivna indexet och har det angivna antalet heltal:

```
range(1, 4)
```

Och returnerar det här resultatet: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Ersätta

Ersätt en delsträng med den angivna strängen och returnera resultatsträngen. Den här funktionen är fallkänslig.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har delsträngen som ska ersättas |
| <*oldText*> | Ja | Sträng | Delsträngen som ska ersättas |
| <*newText*> | Ja | Sträng | Ersättningssträngen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-text*> | Sträng | Den uppdaterade strängen när du har ersatt delsträngen <p>Om delsträngen inte hittas returnerar du den ursprungliga strängen. |
||||

*Exempel*

Det här exemplet hittar den "gamla" delsträngen i "den gamla strängen" och ersätter "gammal" med "ny":

```
replace('the old string', 'old', 'new')
```

Och returnerar det här resultatet: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Ta bort en egenskap från ett objekt och returnera det uppdaterade objektet. Om egenskapen som du försöker ta bort inte finns returnerar funktionen det ursprungliga objektet.

```
removeProperty(<object>, '<property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | JSON-objektet som du vill ta bort en egenskap från |
| <*Egenskapen*> | Ja | Sträng | Namnet på egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet utan den angivna egenskapen |
||||

Om du vill ta bort en underordnad egenskap från en befintlig egenskap använder du den här syntaxen:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | Det JSON-objekt vars egenskap du vill ta bort |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen med den underordnade egenskapen som du vill ta bort |
| <*underordnad-egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet vars underordnade egenskap du tog bort |
||||

*Exempel 1*

Det här exemplet tar bort egenskapen från ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av `middleName` [funktionen JSON()](#json) och returnerar det uppdaterade objektet:

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

Det här exemplet tar bort den underordnade egenskapen från en överordnad egenskap i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av funktionen `middleName` `customerName` [JSON()](#json) och returnerar det uppdaterade objektet:

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

Returnera resultatet från åtgärderna på den översta nivån i den angivna omfångsåtgärden, till exempel en `For_each` `Until` -, - eller `Scope` -åtgärd. Funktionen accepterar en enskild parameter, som är omfångsnamnet, och returnerar en matris som innehåller information från åtgärderna på `result()` den första nivån i det omfånget. Dessa åtgärdsobjekt innehåller samma attribut som de som returneras av funktionen, till exempel `actions()` åtgärdens starttid, sluttid, status, indata, korrelations-ID och utdata.

> [!NOTE]
> Den här funktionen *returnerar endast* information från åtgärderna på den första nivån i den begränsade åtgärden och inte från djupare kapslade åtgärder som växel- eller villkorsåtgärder.

Du kan till exempel använda den här funktionen för att hämta resultat från misslyckade åtgärder så att du kan diagnostisera och hantera undantag. Mer information finns i [Hämta kontext och resultat för fel.](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)

```
result('<scopedActionName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | Sträng | Namnet på den begränsade åtgärden där du vill ha indata och utdata från åtgärderna på den översta nivån i det omfånget |
||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*array-object*> | Matrisobjekt | En matris som innehåller matriser med indata och utdata från varje åtgärd på den översta nivån inom det angivna omfånget |
||||

*Exempel*

Det här exemplet returnerar indata och utdata från varje iteration av en HTTP-åtgärd inuti som finns i en loop med hjälp av `For_each` `result()` funktionen i `Compose` åtgärden:

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

Så här kan den returnerade exempelmatrisen se ut där det yttre objektet innehåller indata och utdata från varje `outputs` iteration av åtgärderna inuti `For_each` åtgärden.

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

### <a name="setproperty"></a>Setproperty

Ange värdet för JSON-objektets -egenskap och returnera det uppdaterade objektet. Om egenskapen som du försöker ange inte finns läggs egenskapen till i objektet. Om du vill lägga till en ny egenskap använder [du funktionen addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | Det JSON-objekt vars egenskap du vill ange |
| <*Egenskapen*> | Ja | Sträng | Namnet på den befintliga eller nya egenskapen som ska anges |
| <*Värde*> | Ja | Valfri | Värdet som ska anges för den angivna egenskapen |
|||||

Om du vill ange den underordnade egenskapen i ett underobjekt använder du ett kapslat `setProperty()` anrop i stället. Annars returnerar funktionen endast det underordnade objektet som utdata.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ja | Objekt | Det JSON-objekt vars egenskap du vill ange |
| <*överordnad-egenskap*> | Ja | Sträng | Namnet på den överordnade egenskapen med den underordnade egenskapen som du vill ange |
| <*underordnad-egenskap*> | Ja | Sträng | Namnet på den underordnade egenskapen som ska anges |
| <*Värde*> | Ja | Valfri | Det värde som ska anges för den angivna egenskapen |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objekt | Det uppdaterade JSON-objektet vars egenskap du anger |
||||

*Exempel 1*

Det här exemplet anger egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med `surName` hjälp av funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

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

Det här exemplet anger den underordnade egenskapen för den överordnade egenskapen i ett JSON-objekt, som konverteras från en sträng till JSON med hjälp av `surName` `customerName` funktionen [JSON().](#json) Funktionen tilldelar det angivna värdet till egenskapen och returnerar det uppdaterade objektet:

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

Ta bort objekt framför en samling och returnera *alla andra* objekt.

```
skip([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Matris | Den samling vars objekt du vill ta bort |
| <*Räkna*> | Ja | Integer | Ett positivt heltal för antalet objekt som ska tas bort längst fram |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Matris | Den uppdaterade samlingen efter borttagning av de angivna objekten |
||||

*Exempel*

Det här exemplet tar bort ett objekt, talet 0, framför den angivna matrisen:

```
skip(createArray(0, 1, 2, 3), 1)
```

Och returnerar den här matrisen med återstående objekt: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Returnera en matris som innehåller delsträngar, avgränsade med kommatecken, baserat på det angivna avgränsartecknet i den ursprungliga strängen.

```
split('<text>', '<delimiter>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska separeras i delsträngar baserat på den angivna avgränsaren i den ursprungliga strängen |
| <*Avgränsare*> | Ja | Sträng | Tecknet i den ursprungliga strängen som ska användas som avgränsare |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Matris | En matris som innehåller delsträngar från den ursprungliga strängen, avgränsade med kommatecken |
||||

*Exempel*

I det här exemplet skapas en matris med delsträngar från den angivna strängen baserat på det angivna tecknet som avgränsare:

```
split('a_b_c', '_')
```

Och returnerar den här matrisen som resultat: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Returnera dagens början för en tidsstämpel.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*uppdaterad tidsstämpel*> | Sträng | Den angivna tidsstämpeln men med början vid nolltimmemarkeringen för dagen |
||||

*Exempel*

I det här exemplet hittar vi dagens start för den här tidsstämpeln:

```
startOfDay('2018-03-15T13:30:30Z')
```

Och returnerar det här resultatet: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Returnerar timmens start för en tidsstämpel.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Den angivna tidsstämpeln men med början vid nollminutsmarkeringen för timmen |
||||

*Exempel*

Det här exemplet hittar timmens start för den här tidsstämpeln:

```
startOfHour('2018-03-15T13:30:30Z')
```

Och returnerar det här resultatet: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Returnera början av månaden för en tidsstämpel.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Den angivna tidsstämpeln men börjar den första dagen i månaden med nolltimmemarkeringen |
||||

*Exempel 1*

I det här exemplet returneras månadens start för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar det här resultatet: `"2018-03-01T00:00:00.0000000Z"`

*Exempel 2*

Det här exemplet returnerar början av månaden i det angivna formatet för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

Och returnerar det här resultatet: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontrollera om en sträng börjar med en specifik delsträng.
Returnera true när delsträngen hittas, eller returnera false när det inte går att hitta.
Den här funktionen är inte fallkänslig.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska kontrolleras |
| <*searchText*> | Ja | Sträng | Startsträngen som ska sökas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt  | Boolesk | Returnera true när den första delsträngen hittas. Returnera false när det inte går att hitta. |
||||

*Exempel 1*

Det här exemplet kontrollerar om "hello world"-strängen börjar med delsträngen "hello":

```
startsWith('hello world', 'hello')
```

Och returnerar det här resultatet: `true`

*Exempel 2*

Det här exemplet kontrollerar om strängen "hello world" börjar med delsträngen "greetings":

```
startsWith('hello world', 'greetings')
```

Och returnerar det här resultatet: `false`

<a name="string"></a>

### <a name="string"></a>sträng

Returnera strängversionen för ett värde.

```
string(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Valfri | Värdet som ska konverteras. Om det här värdet är null eller utvärderas till null konverteras värdet till ett tomt strängvärde ( `""` ). <p><p>Om du till exempel tilldelar en strängvariabel till en egenskap som inte finns, som du kan komma åt med operatorn, konverteras `?` null-värdet till en tom sträng. Att jämföra ett null-värde är dock inte detsamma som att jämföra en tom sträng. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*strängvärde*> | Sträng | Strängversionen för det angivna värdet. Om *värdeparametern* är null eller utvärderas till null returneras det här värdet som ett tomt strängvärde ( `""` ). |
||||





*Exempel 1*

I det här exemplet skapas strängversionen för det här talet:

```
string(10)
```

Och returnerar det här resultatet: `"10"`

*Exempel 2*

Det här exemplet skapar en sträng för det angivna JSON-objektet och använder omsnedstreckstecknet ( ) som ett \\ escape-tecken för det dubbla citattecknet (").

```
string( { "name": "Sophie Owen" } )
```

Och returnerar det här resultatet: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Returnera resultatet från subtraktion av det andra talet från det första talet.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ja | Heltal eller flyttal | Det tal som *subtratrahend ska subtraheras från* |
| <*subtrahend*> | Ja | Heltal eller flyttal | Det tal som ska subtraheras *från minuend* |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Resultatet*> | Heltal eller flyttal | Resultatet från subtraktion av det andra talet från det första talet |
||||

*Exempel*

Det här exemplet subtraherar det andra talet från det första talet:

```
sub(10.3, .3)
```

Och returnerar det här resultatet: `10`

<a name="substring"></a>

### <a name="substring"></a>Delsträng

Returnera tecken från en sträng med början från den angivna positionen eller indexet. Indexvärden börjar med talet 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen vars tecken du vill använda |
| <*startIndex*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som startposition eller indexvärde |
| <*Längd*> | Inga | Integer | Ett positivt antal tecken som du vill använda i delsträngen |
|||||

> [!NOTE]
> Kontrollera att summan från att lägga till *parametervärdena startIndex* och *length* är mindre än längden på strängen som du anger för *textparametern.*
> Annars får du ett felmeddelande, till skillnad från liknande funktioner på andra språk där resultatet är delsträngen från *startIndex* till slutet av strängen. *Längdparametern* är valfri och om den inte anges tar funktionen **substring()** alla tecken som börjar från *startIndex* till slutet av strängen.

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delsträngsresultat*> | Sträng | En delsträng med det angivna antalet tecken som börjar vid den angivna indexpositionen i källsträngen |
||||

*Exempel*

I det här exemplet skapas en delsträng med fem tecken från den angivna strängen, med början från indexvärdet 6:

```
substring('hello world', 6, 5)
```

Och returnerar det här resultatet: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrahera ett antal tidsenheter från en tidsstämpel.
Se även [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen som innehåller tidsstämpeln |
| <*Intervall*> | Ja | Integer | Antalet angivna tidsenheter att subtrahera |
| <*timeUnit*> | Ja | Sträng | Den tidsenhet som ska användas med *intervallet*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Sträng | Tidsstämpeln minus det angivna antalet tidsenheter |
||||

*Exempel 1*

I det här exemplet subtraherar vi en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Och returnerar det här resultatet: `"2018-01-01T00:00:00.0000000Z"`

*Exempel 2*

I det här exemplet subtraherar vi en dag från den här tidsstämpeln:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Och returnerar det här resultatet med det valfria "D"-formatet: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Returnera objekt från en samlings framsida.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Samling*> | Ja | Sträng eller matris | Den samling vars objekt du vill använda |
| <*Räkna*> | Ja | Integer | Ett positivt heltal för antalet objekt som du vill ha framifrån |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*delmängd*> eller [<*delmängd*>] | Sträng eller matris | En sträng eller matris som har det angivna antalet objekt som tas från framsidan av den ursprungliga samlingen |
||||

*Exempel*

De här exemplen hämtar det angivna antalet objekt framför dessa samlingar:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Och returnerar följande resultat:

* Första exemplet: `"abc"`
* Andra exemplet: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Fästingar

Returnerar antalet tick, som är 100 nanosekundersintervall, sedan den 1 januari 0001 12:00:00 midnatt (eller DateTime.Ticks i C#) upp till den angivna tidsstämpeln. Mer information finns i det här avsnittet: [DateTime.Ticks-egenskap (system).](/dotnet/api/system.datetime.ticks)

```
ticks('<timestamp>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Tidsstämpel*> | Ja | Sträng | Strängen för en tidsstämpel |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Antalet tick sedan den angivna tidsstämpeln |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Returnera en sträng i gemener. Om ett tecken i strängen inte har en version med gemener förblir det tecknet oförändrat i den returnerade strängen.

```
toLower('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska returneras i gemener |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*gemener och text*> | Sträng | Den ursprungliga strängen i gemener |
||||

*Exempel*

I det här exemplet konverteras strängen till gemener:

```
toLower('Hello World')
```

Och returnerar det här resultatet: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Returnera en sträng i versaler. Om ett tecken i strängen inte har en version med versaler förblir det tecknet oförändrat i den returnerade strängen.

```
toUpper('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som ska returneras i versaler |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*versaler och text*> | Sträng | Den ursprungliga strängen i versaler |
||||

*Exempel*

I det här exemplet konverteras strängen till versaler:

```
toUpper('Hello World')
```

Och returnerar det här resultatet: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Utlösa

Returnera utdata för en utlösare vid körning, eller värden från andra JSON-namn-och-värde-par, som du kan tilldela till ett uttryck.

* I indata för en utlösare returnerar den här funktionen utdata från den tidigare körningen.

* I villkoret för en utlösare returnerar den här funktionen utdata från den aktuella körningen.

Som standard refererar funktionen till hela utlösarobjektet, men du kan även ange en egenskap vars värde du vill ha.
Dessutom har den här funktionen tillgängliga kortversioner, se [triggerOutputs() och](#triggerOutputs) [triggerBody()](#triggerBody).

```
trigger()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Sträng | Utdata från en utlösare vid körning |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Returnera utdata för `body` en utlösare vid körning.
Förkortning för `trigger().outputs.body` .
Se [trigger()](#trigger).

```
triggerBody()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Sträng | Utdata `body` från utlösaren |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Returnera en matris med värden som matchar ett nyckelnamn i en utlösares *formulärdata* eller *formulärkodade utdata.*

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| [<*matris-med-nyckel-värden*>] | Matris | En matris med alla värden som matchar den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en matris från nyckelvärdet "feedUrl" i en RSS-utlösares formulärdata eller formulärkodade utdata:

```
triggerFormDataMultiValues('feedUrl')
```

Och returnerar den här matrisen som ett exempelresultat: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Returnera en sträng med ett enda värde som matchar ett nyckelnamn i en utlösares *formdata* eller *formulärkodade utdata.*
Om funktionen hittar fler än en matchning returnerar funktionen ett fel.

```
triggerFormDataValue('<key>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Nyckel*> | Ja | Sträng | Namnet på den nyckel vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*nyckelvärde*> | Sträng | Värdet i den angivna nyckeln |
||||

*Exempel*

Det här exemplet skapar en sträng från nyckelvärdet "feedUrl" i en RSS-utlösares formdata eller formulärkodade utdata:

```
triggerFormDataValue('feedUrl')
```

Och returnerar den här strängen som ett exempelresultat: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Returnera brödtexten för en viss del i utdata för en utlösare som har flera delar.

```
triggerMultipartBody(<index>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Ja | Integer | Indexvärdet för den del som du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Kroppen*> | Sträng | Brödtexten för den angivna delen i utdata för en utlösare med flera delar |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Returnera utdata för en utlösare vid körning, eller värden från andra JSON-namn- och värdepar.
Förkortning för `trigger().outputs` .
Se [trigger()](#trigger).

```
triggerOutputs()
```

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*utlösarutdata*> | Sträng | Utdata från en utlösare vid körning  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trimma

Ta bort inledande och avslutande blanksteg från en sträng och returnera den uppdaterade strängen.

```
trim('<text>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ja | Sträng | Strängen som har inledande och avslutande blanksteg som ska tas bort |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedText*> | Sträng | En uppdaterad version för den ursprungliga strängen utan inledande eller avslutande blanksteg |
||||

*Exempel*

Det här exemplet tar bort det inledande och avslutande blanksteget från strängen " Hello World ":

```
trim(' Hello World  ')
```

Och returnerar det här resultatet: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>Unionen

Returnera en samling som innehåller *alla* objekt från de angivna samlingarna.
För att visas i resultatet kan ett objekt visas i alla samlingar som skickas till den här funktionen. Om ett eller flera objekt har samma namn visas det sista objektet med det namnet i resultatet.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Ja | Matris eller objekt, men inte båda | De samlingar som du vill ha *alla objekt* från |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matris eller objekt | En samling med alla objekt från de angivna samlingarna – inga dubbletter |
||||

*Exempel*

Det här exemplet *hämtar* alla objekt från dessa samlingar:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Och returnerar det här resultatet: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Returnera en URI-kodad version (Uniform Resource Identifier) för en sträng genom att ersätta URL-osäkra tecken med escape-tecken.
Använd den här funktionen i stället [för encodeUriComponent()](#encodeUriComponent).
Även om båda funktionerna fungerar på samma sätt är `uriComponent()` det bättre.

```
uriComponent('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen som ska konverteras till URI-kodat format |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Sträng | Den URI-kodade strängen med escape-tecken |
||||

*Exempel*

I det här exemplet skapas en URI-kodad version för den här strängen:

```
uriComponent('https://contoso.com')
```

Och returnerar det här resultatet: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Returnera den binära versionen för en URI-komponent (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den URI-kodade sträng som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Sträng | Den binära versionen för den URI-kodade strängen. Det binära innehållet är base64-kodat och representeras av `$content` . |
||||

*Exempel*

I det här exemplet skapas den binära versionen för den här URI-kodade strängen:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

Och returnerar det här resultatet:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Returnera strängversionen för en URI-kodad sträng (Uniform Resource Identifier), vilket effektivt avkodar den URI-kodade strängen.

```
uriComponentToString('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Den URI-kodade sträng som ska avkodas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Sträng | Den avkodade versionen för den URI-kodade strängen |
||||

*Exempel*

I det här exemplet skapas den avkodade strängversionen för den här URI-kodade strängen:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

Och returnerar det här resultatet: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Returnera värdet `host` för en URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `host` värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*värdvärde*> | Sträng | Värdet `host` för den angivna URI:en |
||||

*Exempel*

Det här exemplet hittar `host` värdet för den här URI:en:

```
uriHost('https://www.localhost.com:8080')
```

Och returnerar det här resultatet: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Returnera värdet `path` för en URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `path` värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*path-value*> | Sträng | Värdet `path` för den angivna URI:en. Om `path` inte har något värde returnerar du tecknet "/". |
||||

*Exempel*

Det här exemplet hittar `path` värdet för den här URI:en:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar det här resultatet: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Returnera värdena `path` och `query` för en URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `path` värden och du vill `query` ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Sträng | Värdena `path` och för den angivna `query` URI:en. Om `path` inte anger något värde returnerar du tecknet "/". |
||||

*Exempel*

Det här exemplet hittar `path` värdena och för den här `query` URI:en:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar det här resultatet: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Returnera värdet `port` för en URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `port` värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*portvärde*> | Integer | Värdet `port` för den angivna URI:en. Om `port` inte anger något värde returnerar du standardporten för protokollet. |
||||

*Exempel*

Det här exemplet returnerar `port` värdet för den här URI:en:

```
uriPort('https://www.localhost:8080')
```

Och returnerar det här resultatet: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Returnera värdet `query` för en URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `query` värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*query-value*> | Sträng | Värdet `query` för den angivna URI:en |
||||

*Exempel*

Det här exemplet returnerar `query` värdet för den här URI:en:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar det här resultatet: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Returnera värdet `scheme` för en URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ja | Sträng | Den URI vars `scheme` värde du vill använda |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*schemavärde*> | Sträng | Värdet `scheme` för den angivna URI:en |
||||

*Exempel*

Det här exemplet returnerar `scheme` värdet för den här URI:en:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Och returnerar det här resultatet: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Du kan också ange ett annat format med <*som>* parameter.


| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Format*> | Inga | Sträng | Antingen [en enskild formatspecificerare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [ett anpassat formatmönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformatet för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (åå-MM-ddTHH:mm:ss.fffffffK), som uppfyller [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tidszonsinformation. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*aktuell tidsstämpel*> | Sträng | Aktuellt datum och aktuell tid |
||||

*Exempel 1*

Anta att det är den 15 april 2018 kl. 13:00:00.
Det här exemplet hämtar den aktuella tidsstämpeln:

```
utcNow()
```

Och returnerar det här resultatet: `"2018-04-15T13:00:00.0000000Z"`

*Exempel 2*

Anta att det är den 15 april 2018 kl. 13:00:00.
Det här exemplet hämtar den aktuella tidsstämpeln med det valfria D-formatet:

```
utcNow('D')
```

Och returnerar det här resultatet: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variabler

Returnera värdet för en angiven variabel.

```
variables('<variableName>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | Sträng | Namnet på variabeln vars värde du vill ha |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*variable-value*> | Valfri | Värdet för den angivna variabeln |
||||

*Exempel*

Anta att det aktuella värdet för variabeln "numItems" är 20.
Det här exemplet hämtar heltalsvärdet för den här variabeln:

```
variables('numItems')
```

Och returnerar det här resultatet: `20`

<a name="workflow"></a>

### <a name="workflow"></a>arbetsflöde

Returnera all information om själva arbetsflödet under körning.

```
workflow().<property>
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Egenskapen*> | Inga | Sträng | Namnet på arbetsflödesegenskapen vars värde du vill ha <p><p>Som standard har ett arbetsflödesobjekt följande egenskaper: `name` , , , , och `type` `id` `location` `run` `tags` . <p><p>- `run` Egenskapsvärdet är ett JSON-objekt som innehåller följande egenskaper: `name` `type` , och `id` . <p><p>- Egenskapen är ett JSON-objekt som innehåller taggar som är associerade med logikappen i Azure Logic Apps eller flöde i Power Automate och `tags` värdena för dessa taggar. [](../azure-resource-manager/management/tag-resources.md) Mer information om taggar i Azure-resurser finns i [Tagga resurser, resursgrupper och prenumerationer för logisk organisation i Azure](../azure-resource-manager/management/tag-resources.md). <p><p>**Obs!** Som standard har en logikapp inga taggar, men Power Automate flöde har `flowDisplayName` taggarna `environmentName` och . |
|||||

*Exempel 1*

Det här exemplet returnerar namnet på ett arbetsflödes aktuella körning:

`workflow().run.name`

*Exempel 2*

Om du använder Power Automate kan du skapa ett uttryck som använder utdataegenskapen för att hämta värdena från `@workflow()` `tags` flödets `flowDisplayName` eller `environmentName` -egenskapen.

Du kan till exempel skicka anpassade e-postaviseringar från själva flödet som länkar tillbaka till ditt flöde. Dessa meddelanden kan innehålla en HTML-länk som innehåller flödets visningsnamn i e-postmeddelandet och som följer den här syntaxen:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

Returnera XML-versionen för en sträng som innehåller ett JSON-objekt.

```
xml('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Värde*> | Ja | Sträng | Strängen med JSON-objektet som ska konverteras <p>JSON-objektet får bara ha en rotegenskap, som inte får vara en matris. <br>Använd omsnedstrecket ( \\ ) som ett escape-tecken för det dubbla citattecknet ("). |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-version*> | Objekt | Den kodade XML-koden för den angivna strängen eller JSON-objektet |
||||

*Exempel 1*

Det här exemplet skapar XML-versionen för den här strängen, som innehåller ett JSON-objekt:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Och returnerar den här resultat-XML:en:

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

Det här exemplet skapar XML för en sträng som innehåller det här JSON-objektet:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Och returnerar den här resultat-XML:en:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Kontrollera XML för noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera matchande noder eller värden. Ett XPath-uttryck, eller bara "XPath", hjälper dig att navigera i en XML-dokumentstruktur så att du kan välja noder eller beräkningsvärden i XML-innehållet.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Ja | Valfri | XML-strängen som ska söka efter noder eller värden som matchar ett XPath-uttrycksvärde |
| <*Xpath*> | Ja | Valfri | XPath-uttrycket som används för att hitta matchande XML-noder eller -värden |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | En XML-nod när endast en enskild nod matchar det angivna XPath-uttrycket |
| <*Värde*> | Valfri | Värdet från en XML-nod när endast ett enda värde matchar det angivna XPath-uttrycket |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>\- eller - </br>[<*value1*>, <*value2*>, ...] | Matris | En matris med XML-noder eller värden som matchar det angivna XPath-uttrycket |
||||

*Exempel 1*

Anta att du har den här `'items'` XML-strängen: 

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

Det här exemplet skickar XPath-uttrycket, , för att hitta noderna som matchar noden i XML-strängen och returnerar en `'/produce/item/name'` `<name></name>` matris med dessa `'items'` nodvärden:

`xpath(xml(parameters('items')), '/produce/item/name')`

I exemplet används även funktionen [parameters()](#parameters) för att hämta XML-strängen från och konvertera strängen till XML-format med hjälp `'items'` av funktionen [xml().](#xml)

Här är resultatmatrisen med noderna som matchar `<name></name` :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exempel 2*

I exempel 1 skickar det här exemplet XPath-uttrycket, , för att hitta det första elementet som `'/produce/item/name[1]'` `name` är `item` underelementet.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Här är resultatet: `Gala`

*Exempel 3*

I exempel 1 skickar det här exemplet XPath-uttrycket, , för att hitta det sista elementet som `'/produce/item/name[last()]'` `name` är `item` underelementet.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Här är resultatet: `Honeycrisp`

*Exempel 4*

I det här exemplet antar vi att `items` XML-strängen även innehåller attributen `expired='true'` och `expired='false'` :

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

Det här exemplet skickar XPath-uttrycket, `'//name[@expired]'` , för att hitta alla element som har `name` `expired` attributet :

`xpath(xml(parameters('items')), '//name[@expired]')`

Här är resultatet: `[ Gala, Honeycrisp ]`

*Exempel 5*

Anta i det här exemplet att `items` XML-strängen endast innehåller det här `expired = 'true'` attributet:

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

Det här exemplet skickar XPath-uttrycket, `'//name[@expired = 'true']'` , för att hitta alla element som har `name` attributet `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Här är resultatet: `[ Gala ]`

*Exempel 6*

Anta i det här exemplet att `items` XML-strängen även innehåller dessa attribut: 

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

Det här exemplet skickar XPath-uttrycket, `'//name[price>35]'` , för att hitta alla element som har `name` `price > 35` :

`xpath(xml(parameters('items')), '//name[price>35]')`

Här är resultatet: `Honeycrisp`

*Exempel 7*

I det här exemplet antar vi att `items` XML-strängen är samma som i exempel 1:

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

Det här exemplet hittar noder som matchar `<count></count>` noden och lägger till dessa nodvärden med `sum()` funktionen :

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Här är resultatet: `30`

*Exempel 8*

I det här exemplet antar vi att du har den här XML-strängen, som innehåller XML-dokumentets `xmlns="https://contoso.com"` namnområde:

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

De här uttrycken använder antingen `/*[name()="file"]/*[name()="location"]` XPath-uttrycket `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` eller , för att hitta noder som matchar `<location></location>` noden. De här exemplen visar den syntax som du använder i Logikappdesignern eller i uttrycksredigeraren:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Här är resultatnoden som matchar `<location></location>` noden: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Om du arbetar i kodvyn kan du undvika det dubbla citattecknet (") med hjälp av omsnedstreckstecknet ( \\ ). 
> Du måste till exempel använda escape-tecken när du serialiserar ett uttryck som en JSON-sträng. 
> Men om du arbetar i Logikappdesignern eller uttrycksredigeraren behöver du inte undvika det dubbla citattecknet eftersom omsnedstreckstecknet läggs till automatiskt i den underliggande definitionen, till exempel:
> 
> * Kodvy: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Uttrycksredigeraren: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Exempel 9*

I exempel 8 använder det här exemplet XPath-uttrycket, `'string(/*[name()="file"]/*[name()="location"])'` , för att hitta värdet i `<location></location>` noden:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Här är resultatet: `Paris`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [definitionsspråket för arbetsflöden](../logic-apps/logic-apps-workflow-definition-language.md)
