---
title: Använda parametrar och uttryck i Azure Data Factory
description: Den här artikeln innehåller information om uttryck och funktioner som du kan använda för att skapa Data Factory-entiteter.
author: ssabat
ms.author: susabat
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 8f22645eafa0969eac3d6c4c0645909f8c650cad
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199819"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Använda parametrar, uttryck och funktioner i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuell version](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I det här dokumentet fokuserar vi främst på att lära dig grundläggande koncept med olika exempel för att utforska möjligheten att skapa parameterstyrda data pipelines i Azure Data Factory. Parameterisering och dynamiska uttryck är sådana som är viktiga för ADF eftersom de kan spara mycket tid och tillåta en mycket mer flexibel extraherings-, transformerings-, inläsnings-eller extraherings-, belastnings-, transformerings-eller ELT-lösning () som dramatiskt minskar kostnaden för lösnings underhåll och påskyndar implementeringen av nya funktioner i befintliga pipeliner. Dessa vinster beror på att Parameterisering minimerar mängden hård kodning och ökar antalet återanvändbara objekt och processer i en lösning.

## <a name="azure-data-factory-ui-and-parameters"></a>Användar gränssnitt och parametrar för Azure Data Factory

Om du inte har använt Azure Data Factory-parametern i ADF-användargränssnittet kan du granska [Data Factory-gränssnittet för länkade tjänster med parametrar](https://docs.microsoft.com/azure/data-factory/parameterize-linked-services#data-factory-ui)  och [Data Factory-användargränssnitt för metadata driven pipeline med parametrar](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization#data-factory-ui) för visuell förklaring.

## <a name="parameter-and-expression-concepts"></a>Begrepp för parameter och uttryck 

Du kan använda parametrar för att skicka externa värden till pipelines, data uppsättningar, länkade tjänster och data flöden. När parametern har skickats till resursen kan den inte ändras. Genom att parameterstyrda resurser kan du återanvända dem med olika värden varje gång. Parametrar kan användas individuellt eller som en del av uttryck. JSON-värden i definitionen kan vara literala eller uttryck som utvärderas vid körning.

Exempel:  
  
```json
"name": "value"
```

 eller  
  
```json
"name": "@pipeline().parameters.password"
```

Uttryck kan finnas var som helst i ett JSON-sträng värde och resulterar alltid i ett annat JSON-värde. Här är *lösen ordet* en pipeline-parameter i uttrycket. Om ett JSON-värde är ett uttryck extraheras bröd texten i uttrycket genom att ta bort at-tecknet ( \@ ). Om en tecken sträng krävs som börjar med \@ måste den föregås av \@ \@ . I följande exempel visas hur uttryck utvärderas.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>Exempel på användning av parametrar i uttryck 

### <a name="complex-expression-example"></a>Exempel på komplexa uttryck
Exemplet nedan visar ett komplext exempel som refererar till ett djup under fält med aktivitets utdata. Om du vill referera till en pipeline-parameter som utvärderas till ett under fält använder du []-syntaxen i stället för punkt (.)-operatorn (som i händelse av subfield1 och subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Redigeraren för dynamiskt innehåll

I redigeraren för dynamiskt innehåll undantas automatiskt tecken i innehållet när du är klar med redigeringen. Följande innehåll i innehålls redigeraren är till exempel en sträng interpolation med två uttrycks funktioner. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Redigeraren för dynamiskt innehåll konverterar över innehåll till uttryck `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Resultatet av det här uttrycket är en JSON-format sträng som visas nedan.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>En data uppsättning med parametrar

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

### <a name="a-pipeline-with--parameters"></a>En pipeline med parametrar

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

  
## <a name="calling-functions-within-expressions"></a>Anropa funktioner i uttryck 

Du kan anropa Functions i uttryck. I följande avsnitt finns information om de funktioner som kan användas i ett uttryck.  

### <a name="string-functions"></a>Strängfunktioner  

Om du vill arbeta med strängar kan du använda dessa sträng funktioner och även vissa [samlings funktioner](#collection-functions).
Sträng funktioner fungerar bara för strängar.

| Funktionen String | Uppgift |
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

### <a name="collection-functions"></a>Samlingsfunktioner

Om du vill arbeta med samlingar, vanligt vis matriser, strängar och ibland kan du använda de här samlings funktionerna.

| Samlings funktion | Uppgift |
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

### <a name="logical-functions"></a>Logiska funktioner  

Dessa funktioner är användbara i villkor, de kan användas för att utvärdera vilken typ av logik som helst.  
  
| Funktion för logisk jämförelse | Uppgift |
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
  
### <a name="conversion-functions"></a>Konverteringsfunktioner  

 Dessa funktioner används för att konvertera mellan var och en av de ursprungliga typerna på språket:  
-   sträng
-   heltal
-   flyt
-   boolean
-   lagringsmatriser
-   lista

| Konverterings funktion | Uppgift |
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

### <a name="math-functions"></a>Matematiska funktioner  
 Dessa funktioner kan användas för antingen typer av tal: **heltal** och **flyttal**.  

| Matematik funktion | Uppgift |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Returnera resultatet från att lägga till två tal. |
| [div](control-flow-expression-language-functions.md#div) | Returnera resultatet från att dividera två tal. |
| [bekräftat](control-flow-expression-language-functions.md#max) | Returnera det högsta värdet från en uppsättning tal eller en matris. |
| [min](control-flow-expression-language-functions.md#min) | Returnera det lägsta värdet från en uppsättning tal eller en matris. |
| [rest](control-flow-expression-language-functions.md#mod) | Returnera resten från att dividera två tal. |
| [mul](control-flow-expression-language-functions.md#mul) | Returnera produkten från att multiplicera två tal. |
| [slump](control-flow-expression-language-functions.md#rand) | Returnera ett slumpmässigt heltal från ett angivet intervall. |
| [intervall](control-flow-expression-language-functions.md#range) | Returnera en heltals mat ris som börjar från ett angivet heltal. |
| [Build](control-flow-expression-language-functions.md#sub) | Returnera resultatet från att subtrahera det andra talet från det första talet. |
  
### <a name="date-functions"></a>Datumfunktioner  

| Funktionen datum/tid | Uppgift |
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

## <a name="detailed-examples-for-practice"></a>Detaljerade exempel på praxis

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Detaljerad kopia av Azure Data Factory-pipeline med parametrar 

Den här [Azure Data Factory-parametern för att skicka självstudier](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vägleder dig genom hur du skickar parametrar mellan en pipeline och aktivitet samt mellan aktiviteterna.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Detaljerad data flödes pipeline för mappning med parametrar 

Följ [mappnings data flödet med parametrar](https://docs.microsoft.com/azure/data-factory/parameters-data-flow) för omfattande exempel på hur du använder parametrar i data flödet.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Detaljerade metadata drivna pipeline med parametrar

Följ [de metadata drivna pipelinen med parametrar](https://docs.microsoft.com/azure/data-factory/how-to-use-trigger-parameterization) för att lära dig mer om hur du använder parametrar för att utforma metadata drivna pipeliner. Detta är ett populärt användnings fall för parametrar.


## <a name="next-steps"></a>Nästa steg
En lista över systemvariabler som du kan använda i uttryck finns i [Systemvariabler](control-flow-system-variables.md).
