---
title: Uttryck och funktioner i Azure Data Factory
description: Den här artikeln innehåller information om uttryck och funktioner som du kan använda för att skapa Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 1b10146e59cefb17ff267eb0b470dd83004a7c2a
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976579"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Uttryck och funktioner i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuell version](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller information om uttryck och funktioner som stöds av Azure Data Factory. 

## <a name="expressions"></a>Uttryck

JSON-värden i definitionen kan vara literala eller uttryck som utvärderas vid körning. Ett exempel:  
  
```json
"name": "value"
```

 eller  
  
```json
"name": "@pipeline().parameters.password"
```

Uttryck kan finnas var som helst i ett JSON-sträng värde och resulterar alltid i ett annat JSON-värde. Om ett JSON-värde är ett uttryck extraheras bröd texten i uttrycket genom att ta bort at-tecknet ( \@ ). Om en tecken sträng krävs som börjar med \@ måste den föregås av \@ \@ . I följande exempel visas hur uttryck utvärderas.  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|komponentparametrar|Tecknens parametrar returneras.|  
|"parametrar [1]"|Tecknens parametrar [1] returneras.|  
|"\@\@"|En 1-tecken sträng som innehåller \@ returnerar.|  
|" \@"|En 2-tecken sträng som innehåller \@ returnerar.|  
  
 Uttryck kan också visas i strängar med hjälp av en funktion som kallas *String-interpolation* där uttryck är omslutna `@{ ... }` . Exempelvis: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Med hjälp av String-interpolation är resultatet alltid en sträng. Anta att jag har definierat `myNumber` as `42` och  `myString` som  `foo` :  
  
|JSON-värde|Resultat|  
|----------------|------------|  
|" \@ pipelining (). Parameters. unstring"| Returnerar `foo` som en sträng.|  
|\@{pipeliner (). Parameters. unstring}| Returnerar `foo` som en sträng.|  
|" \@ pipeline (). Parameters. Number"| Returnerar `42` som ett *tal*.|  
|\@{pipeliner (). Parameters. Number}| Returnerar `42` som en *sträng*.|  
|"Svar är: @ {pipeline (). Parameters. Number}"| Returnerar strängen `Answer is: 42` .|  
|" \@ concat (" svar är:, String (pipeline (). Parameters. Number)) "| Returnerar strängen `Answer is: 42`|  
|"Svar är: \@ \@ {pipeline (). Parameters. Number}"| Returnerar strängen `Answer is: @{pipeline().parameters.myNumber}` .|  
  
## <a name="examples"></a>Exempel

### <a name="complex-expression-example"></a>Exempel på komplexa uttryck
Exemplet nedan visar ett komplext exempel som refererar till ett djup under fält med aktivitets utdata. Om du vill referera till en pipeline-parameter som utvärderas till ett under fält använder du []-syntaxen i stället för punkt (.)-operatorn (som i händelse av subfield1 och subfield2)

@activity('*activityName*'). output. *subfield1*. *subfield2*[pipeline (). Parameters.*subfield3*]. *subfield4*

### <a name="a-dataset-with-a-parameter"></a>En data uppsättning med en parameter
I följande exempel tar BlobDataset en parameter med namnet **Path**. Värdet används för att ange ett värde för egenskapen **folderPath** med hjälp av uttrycket: `dataset().path` . 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>En pipeline med en parameter
I följande exempel använder pipelinen **inputPath** -och **outputPath** -parametrar. **Sökvägen** för den parameterstyrda BLOB-datauppsättningen anges med hjälp av värden för dessa parametrar. Syntaxen som används här är: `pipeline().parameters.parametername` . 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Självstudier
Den här [självstudien](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vägleder dig genom hur du skickar parametrar mellan en pipeline och aktivitet samt mellan aktiviteterna.

  
## <a name="functions"></a>Functions

Du kan anropa Functions i uttryck. I följande avsnitt finns information om de funktioner som kan användas i ett uttryck.  

## <a name="string-functions"></a>Strängfunktioner  

Om du vill arbeta med strängar kan du använda dessa sträng funktioner och även vissa [samlings funktioner](#collection-functions).
Sträng funktioner fungerar bara för strängar.

| Funktionen String | Aktivitet |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Kombinera två eller fler strängar och returnera den kombinerade strängen. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Kontrol lera om en sträng slutar med den angivna under strängen. |
| [guid](control-flow-expression-language-functions.md#guid) | Generera en globalt unik identifierare (GUID) som en sträng. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Returnera start positionen för en under sträng. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Returnera start positionen för den sista förekomsten av en under sträng. |
| [bytt](control-flow-expression-language-functions.md#replace) | Ersätt en under sträng med den angivna strängen och returnera den uppdaterade strängen. |
| [del](control-flow-expression-language-functions.md#split) | Returnera en matris som innehåller del strängar, avgränsade med kommatecken, från en större sträng baserat på ett angivet avgränsnings tecken i den ursprungliga strängen. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Kontrol lera om en sträng börjar med en speciell under sträng. |
| [under sträng](control-flow-expression-language-functions.md#substring) | Returnera tecken från en sträng, från angiven position. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Returnera en sträng med gemener. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Returnera en sträng i versal format. |
| [reducera](control-flow-expression-language-functions.md#trim) | Ta bort inledande och avslutande blank steg från en sträng och returnera den uppdaterade strängen. |

## <a name="collection-functions"></a>Samlingsfunktioner

Om du vill arbeta med samlingar, vanligt vis matriser, strängar och ibland kan du använda de här samlings funktionerna.

| Samlings funktion | Aktivitet |
| ------------------- | ---- |
| [ingår](control-flow-expression-language-functions.md#contains) | Kontrol lera om en samling har ett angivet objekt. |
| [tomt](control-flow-expression-language-functions.md#empty) | Kontrol lera om en samling är tom. |
| [förstagångskörningen](control-flow-expression-language-functions.md#first) | Returnera det första objektet från en samling. |
| [överlappning](control-flow-expression-language-functions.md#intersection) | Returnera en samling som *endast* innehåller gemensamma objekt i de angivna samlingarna. |
| [ansluta](control-flow-expression-language-functions.md#join) | Returnera en sträng som innehåller *alla* objekt från en matris, separerade med det angivna specialtecknet. |
| [pågå](control-flow-expression-language-functions.md#last) | Returnera det sista objektet från en samling. |
| [length](control-flow-expression-language-functions.md#length) | Returnera antalet objekt i en sträng eller matris. |
| [Ignorera](control-flow-expression-language-functions.md#skip) | Ta bort objekt från början av en samling och returnera *alla andra* objekt. |
| [gå](control-flow-expression-language-functions.md#take) | Returnera objekt från början av en samling. |
| [Union](control-flow-expression-language-functions.md#union) | Returnera en samling som innehåller *alla* objekt från de angivna samlingarna. | 

## <a name="logical-functions"></a>Logiska funktioner  

Dessa funktioner är användbara i villkor, de kan användas för att utvärdera vilken typ av logik som helst.  
  
| Funktion för logisk jämförelse | Aktivitet |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Kontrol lera om alla uttryck är sanna. |
| [är lika med](control-flow-expression-language-functions.md#equals) | Kontrol lera om båda värdena är likvärdiga. |
| [större än](control-flow-expression-language-functions.md#greater) | Kontrol lera om det första värdet är större än det andra värdet. |
| [större än eller lika med](control-flow-expression-language-functions.md#greaterOrEquals) | Kontrol lera om det första värdet är större än eller lika med det andra värdet. |
| [eventuella](control-flow-expression-language-functions.md#if) | Kontrol lera om ett uttryck är sant eller falskt. Returnera ett angivet värde baserat på resultatet. |
| [minskad](control-flow-expression-language-functions.md#less) | Kontrol lera om det första värdet är mindre än det andra värdet. |
| [mindre än eller lika med](control-flow-expression-language-functions.md#lessOrEquals) | Kontrol lera om det första värdet är mindre än eller lika med det andra värdet. |
| [Ogiltigt](control-flow-expression-language-functions.md#not) | Kontrol lera om ett uttryck är falskt. |
| [eller](control-flow-expression-language-functions.md#or) | Kontrol lera om minst ett uttryck är sant. |
  
## <a name="conversion-functions"></a>Konverteringsfunktioner  

 Dessa funktioner används för att konvertera mellan var och en av de ursprungliga typerna på språket:  
-   sträng
-   heltal
-   flyt
-   boolean
-   lagringsmatriser
-   lista

| Konverterings funktion | Aktivitet |
| ------------------- | ---- |
| [matris](control-flow-expression-language-functions.md#array) | Returnera en matris från en angiven Indatatyp. För flera indata, se [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Returnera Base64-kodad version för en sträng. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Returnera den binära versionen för en Base64-kodad sträng. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Returnera sträng versionen för en Base64-kodad sträng. |
| [binär](control-flow-expression-language-functions.md#binary) | Returnera den binära versionen för ett indatavärde. |
| [boolesk](control-flow-expression-language-functions.md#bool) | Returnera den booleska versionen för ett indatavärde. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Returnera det första värdet som inte är null från en eller flera parametrar. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Returnera en matris från flera indata. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Returnera data-URI: n för ett indatavärde. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Returnera den binära versionen för en data-URI. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Returnera sträng versionen för en data-URI. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Returnera sträng versionen för en Base64-kodad sträng. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Returnera den binära versionen för en data-URI. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Returnera en sträng som ersätter escape-tecken med avkodade versioner. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Returnera en sträng som ersätter URL-osäkra tecken med escape-tecken. |
| [float](control-flow-expression-language-functions.md#float) | Returnera ett flytt ALS nummer för ett indatavärde. |
| [int](control-flow-expression-language-functions.md#int) | Returnera heltals versionen för en sträng. |
| [utgör](control-flow-expression-language-functions.md#json) | Returnera värdet för JavaScript Object Notation (JSON) av typen eller objektet för en sträng eller XML. |
| [sträng](control-flow-expression-language-functions.md#string) | Returnera sträng versionen för ett indatavärde. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Returnera den URI-kodade versionen för ett indatavärde genom att ersätta URL-osäkra tecken med escape-tecken. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Returnera den binära versionen för en URI-kodad sträng. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Returnera sträng versionen för en URI-kodad sträng. |
| [fil](control-flow-expression-language-functions.md#xml) | Returnera XML-versionen för en sträng. |
| [XPath](control-flow-expression-language-functions.md#xpath) | Kontrol lera XML för noder eller värden som matchar ett XPath-uttryck (XML Path Language) och returnera matchande noder eller värden. |

## <a name="math-functions"></a>Matematiska funktioner  
 Dessa funktioner kan användas för antingen typer av tal: **heltal** och **flyttal**.  

| Matematik funktion | Aktivitet |
| ------------- | ---- |
| [skapa](control-flow-expression-language-functions.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](control-flow-expression-language-functions.md#div) | Returnera resultatet från att dividera två tal. |
| [bekräftat](control-flow-expression-language-functions.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [minimum](control-flow-expression-language-functions.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [rest](control-flow-expression-language-functions.md#mod) | Returnera resten från att dividera två tal. |
| [mul](control-flow-expression-language-functions.md#mul) | Returnera produkten från att multiplicera två tal. |
| [slump](control-flow-expression-language-functions.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [intervall](control-flow-expression-language-functions.md#range) | Returnera en heltals mat ris som börjar från ett angivet heltal. |
| [Build](control-flow-expression-language-functions.md#sub) | Returnera resultatet från att subtrahera det andra talet från det första talet. |
  
## <a name="date-functions"></a>Datumfunktioner  

| Funktionen datum/tid | Aktivitet |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Lägg till ett antal dagar i en tidstämpel. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Lägg till ett antal timmar i en tidsstämpel. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Lägg till ett antal minuter i en tidsstämpel. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Lägg till ett antal sekunder i en tidsstämpel. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Lägg till ett antal tidsenheter i en tidsstämpel. Se även [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Konvertera en tidsstämpel från Universal Time Coordinated (UTC) till mål tids zonen. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Konvertera en tidsstämpel från käll tids zonen till mål tids zonen. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konvertera en tidsstämpel från käll tids zonen till koordinerad Universal-tid (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Returnera dag i månads komponenten från en tidsstämpel. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Returnera dag i vecko komponenten från en tidsstämpel. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Returnera dagen på års komponenten från en tidsstämpel. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Returnera tidstämpeln som en sträng i valfritt format. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Returnera den aktuella tidsstämpeln och de angivna tidsenheterna. Se även [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. Se även [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Returnera början på dagen för en tidsstämpel. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Returnera början på timmen för en tidsstämpel. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Returnera början på månaden för en tidsstämpel. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtrahera ett antal tidsenheter från en tidsstämpel. Se även [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Returnera `ticks` egenskap svärdet för en angiven tidstämpel. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Returnera den aktuella tidstämpeln som en sträng. |

## <a name="function-reference"></a>Funktions referens

I det här avsnittet visas alla tillgängliga funktioner i alfabetisk ordning.

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Tidsstämpeln plus det angivna antalet dagar  |
||||

*Exempel 1*

I det här exemplet läggs 10 dagar till i den angivna tidsstämpeln:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Och returnerar följande resultat: `"2018-03-25T00:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem dagar från den angivna tidsstämpeln:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Och returnerar följande resultat: `"2018-03-10T00:00:0000000Z"`

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

Och returnerar följande resultat: `"2018-03-15T10:00:0000000Z"`

*Exempel 2*

Det här exemplet subtraherar fem timmar från den angivna tidsstämpeln:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Och returnerar följande resultat: `"2018-03-15T10:00:0000000Z"`

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

Kontrol lera om båda uttrycken är sanna.
Returnera true när båda uttrycken är sanna eller returnera falskt när minst ett uttryck är falskt.

```
and(<expression1>, <expression2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*Uttryck2*> | Ja | Boolesk | De uttryck som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | -----| ----------- |
| sant eller falskt | Boolesk | Returnera true när båda uttrycken är sanna. Returnera falskt om minst ett uttryck är falskt. |
||||

*Exempel 1*

De här exemplen kontrollerar om de angivna booleska värdena båda är sanna:

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

De här exemplen kontrollerar om de angivna uttrycken båda är sanna:

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

Returnera sträng versionen för en Base64-kodad sträng, och avkoda den base64-strängen på ett effektivt sätt.
Använd den här funktionen i stället för [decodeBase64 ()](#decodeBase64).
Även om båda funktionerna fungerar på samma sätt, `base64ToString()` är föredra.

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

<a name="bool"></a>

### <a name="bool"></a>boolesk

Returnera den booleska versionen för ett värde.

```
bool(<value>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Valfri | Värdet som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Den booleska versionen för det angivna värdet |
||||

*Exempel*

I de här exemplen konverteras de angivna värdena till booleska värden:

```
bool(1)
bool(0)
```

Och returnerar följande resultat:

* Första exemplet: `true`
* Andra exempel: `false`

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

Och returnerar följande resultat: `"2018-01-01T00:00:00Z"`

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

Och returnerar följande resultat: `3`

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

### <a name="decodebase64"></a>decodeBase64

Returnera sträng versionen för en Base64-kodad sträng, och avkoda den base64-strängen på ett effektivt sätt.
Överväg att använda [base64ToString ()](#base64ToString) i stället för `decodeBase64()` .
Även om båda funktionerna fungerar på samma sätt, `base64ToString()` är föredra.

```
decodeBase64('<value>')
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

I det här exemplet skapas en sträng för en Base64-kodad sträng:

```
decodeBase64('aGVsbG8=')
```

Och returnerar följande resultat: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Returnera den binära versionen för en URI (data Uniform Resource Identifier).
Överväg att använda [dataUriToBinary ()](#dataUriToBinary)i stället för `decodeDataUri()` .
Även om båda funktionerna fungerar på samma sätt, `dataUriToBinary()` är föredra.

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
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Och returnerar följande resultat: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Returnera heltals resultatet från att dividera två tal.
För att få resten av resultatet, se [mod ()](#mod).

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
| <*kvot-resultat*> | Integer | Heltals resultatet från att dividera det första talet med det andra talet |
||||

*Exempel*

Båda exemplen delar det första talet med det andra talet:

```
div(10, 5)
div(11, 5)
```

Och returnera följande resultat: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Returnera en URI-kodad (Uniform Resource Identifier) version för en sträng genom att ersätta URL-osäkra tecken med escape-tecken.
Överväg att använda [uriComponent ()](#uriComponent)i stället för `encodeUriComponent()` .
Även om båda funktionerna fungerar på samma sätt, `uriComponent()` är föredra.

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

Och returnerar följande resultat: `"http%3A%2F%2Fcontoso.com"`

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | En enkel [formats specifikation](/dotnet/api/system.guid.tostring) för det returnerade GUID: t. Som standard är formatet "D", men du kan använda "N", "D", "B", "P" eller "X". |
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

Kontrol lera om ett uttryck är sant eller falskt.
Returnera ett angivet värde baserat på resultatet.

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

<a name="json"></a>

### <a name="json"></a>json

Returnera värdet för JavaScript Object Notation (JSON) av typen eller objektet för en sträng eller XML.

```
json('<value>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*värde*> | Ja | Sträng eller XML | Strängen eller XML som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*JSON-resultat*> | Inbyggd JSON-typ eller-objekt | Det interna JSON-värdet eller objektet för den angivna strängen eller XML-filen. Om strängen är null returnerar funktionen ett tomt objekt. |
||||

*Exempel 1*

I det här exemplet konverteras den här strängen till JSON-värdet:

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

```
{
  "fullName": "Sophia Owen"
}
```

*Exempel 3*

I det här exemplet konverteras XML till JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Och returnerar följande resultat:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
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

Returnera start positionen eller index värde för den sista förekomsten av en under sträng.
Den här funktionen är inte Skift läges känslig och index börjar med siffran 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen som innehåller under strängen som ska hittas |
| <*searchText*> | Ja | Sträng | Under strängen som ska hittas |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*slut index-värde*> | Integer | Start position eller index värde för den sista förekomsten av den angivna under strängen. <p>Returnera talet-1 om strängen inte hittas. |
||||

*Exempel*

I det här exemplet hittas start indexet för den sista förekomsten av under strängen "World" i strängen "Hello World":

```
lastIndexOf('hello world', 'world')
```

Och returnerar följande resultat: `6`

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
Returnera true när minst ett uttryck är sant, eller returnera falskt om båda är falskt.

```
or(<expression1>, <expression2>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*uttryck1*>, <*Uttryck2*> | Ja | Boolesk | De uttryck som ska kontrol leras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| sant eller falskt | Boolesk | Returnera true när minst ett uttryck är sant. Returnera falskt när båda uttrycken är falska. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*Uppdaterad – tidsstämpel*> | Sträng | Den angivna tidsstämpeln men från den första dagen i månaden vid noll-timmarsformat |
||||

*Exempel*

Det här exemplet returnerar början på månaden för den här tidsstämpeln:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Och returnerar följande resultat: `"2018-03-01T00:00:00.0000000Z"`

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
| <*värde*> | Ja | Valfri | Värdet som ska konverteras |
|||||

| Returvärde | Typ | Beskrivning |
| ------------ | ---- | ----------- |
| <*sträng-värde*> | Sträng | Sträng versionen för det angivna värdet |
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

Returnera tecken från en sträng, från angiven position eller index.
Index värden börjar med siffran 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*information*> | Ja | Sträng | Strängen vars tecken du vill ha |
| <*Start*> | Ja | Integer | Ett positivt tal som är lika med eller större än 0 som du vill använda som start position eller index värde |
| <*krävande*> | Ja | Integer | Ett positivt antal tecken som du vill ha i under strängen |
|||||

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
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

Och returnerar följande resultat: `"2018-01-01T00:00:00:0000000Z"`

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

Returnera `ticks` egenskap svärdet för en angiven tidstämpel.
Ett *Tick* är ett 100-nanosekunder-intervall.

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

Och returnerar följande resultat: `"http%3A%2F%2Fcontoso.com"`

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
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
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
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Och returnerar följande resultat: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Returnera den aktuella tidsstämpeln.

```
utcNow('<format>')
```

Alternativt kan du ange ett annat format med parametern <*format*>.

| Parameter | Krävs | Typ | Beskrivning |
| --------- | -------- | ---- | ----------- |
| <*formatering*> | Inga | Sträng | Antingen en [enskild format identifierare](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller ett [anpassat format mönster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Standardformat för tidsstämpeln är ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (ÅÅÅÅ-MM-ddTHH: mm: SS: fffffffK), som följer [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) och bevarar tids zons information. |
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

I exemplet nedan hittar det här exemplet noder som matchar `<count></count>` noden och lägger till noderna med `sum()` funktionen:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Och returnerar följande resultat: `30`

*Exempel 2*

I det här exemplet hittar båda uttrycken noder som matchar `<location></location>` noden, i de angivna argumenten, som innehåller XML med ett namn område. Uttrycks tecknet använder det omvända snedstrecket ( \\ ) som ett escape-tecken för det dubbla citat tecknet (").

* *Uttryck 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Uttryck 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Följande är argumenten:

* Denna XML, som innehåller XML-dokumentets namnrymd, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Antingen XPath-uttryck här:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Här är den resulterande noden som matchar `<location></location>` noden:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exempel 3*

I följande exempel hittar det här exemplet värdet i `<location></location>` noden:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Och returnerar följande resultat: `"Paris"`

## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [Systemvariabler](control-flow-system-variables.md).
