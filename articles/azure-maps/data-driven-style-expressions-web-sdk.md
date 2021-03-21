---
title: Data drivna format uttryck i Azure Maps Web SDK | Microsoft Azure Maps
description: Lär dig mer om data drivna format uttryck. Se hur du använder dessa uttryck i Azure Maps Web SDK för att justera formaten i Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680065"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Uttryck för data drivna format (webb-SDK)

Med uttryck kan du tillämpa affärs logik för att formatera alternativ som ser till att egenskaperna som definierats i varje form i en data källa. Uttryck kan filtrera data i en data källa eller ett lager. Uttryck kan bestå av villkorsstyrd logik, t. ex. if-Statements. Och kan användas för att manipulera data med hjälp av: sträng operatorer, logiska operatorer och matematiska operatorer.

Data drivna format minskar mängden kod som behövs för att implementera affärs logik kring formatering. När det används med lager utvärderas uttryck vid åter givnings tiden i en separat tråd. Den här funktionen ger ökad prestanda jämfört med att utvärdera affärs logiken i UI-tråden.

Den här videon ger en översikt över data driven format i Azure Maps Web SDK.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

Uttryck visas som JSON-matriser. Det första elementet i ett uttryck i matrisen är en sträng som anger namnet på uttrycks operatorn. Till exempel "+" eller "Case". Nästa element (om det finns några) är argumenten för uttrycket. Varje argument är antingen ett litteralt värde (en sträng, en siffra, ett booleskt värde eller ett `null` ) eller en annan uttrycks mat ris. Följande pseudocode definierar den grundläggande strukturen i ett uttryck. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK stöder många typer av uttryck. Uttryck kan användas på egen hand eller i kombination med andra uttryck.

| Typ av uttryck | Beskrivning |
|---------------------|-------------|
| [Mängd uttryck](#aggregate-expression) | Ett uttryck som definierar en beräkning som bearbetas över en uppsättning data och som kan användas med `clusterProperties` alternativet i en `DataSource` . |
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser. |
| [Färg uttryck](#color-expressions) | Färg uttryck gör det enklare att skapa och ändra färg värden. |
| [Villkorliga uttryck](#conditional-expressions) | Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements. |
| [Data uttryck](#data-expressions) | Ger åtkomst till egenskaps data i en funktion. |
| [Interpolerade och steg uttryck](#interpolate-and-step-expressions) | Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. |
| [Lagerbaserat uttryck](#layer-specific-expressions) | Särskilda uttryck som endast gäller för ett enskilt lager. |
| [Matematiska uttryck](#math-expressions) | Innehåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket. |
| [Uttryck för sträng operator](#string-operator-expressions) | Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. |
| [Typuttryck](#type-expressions) | Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden. |
| [Uttryck för variabel bindning](#variable-binding-expressions) | Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel och refereras till i ett uttryck flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Uttryck för zoomning](#zoom-expression) | Hämtar den aktuella zoomnings nivån för kartan vid åter givnings tiden. |

I alla exempel i det här dokumentet används följande funktion för att demonstrera olika sätt som de olika typerna av uttryck kan användas på. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>Data uttryck

Data uttryck ger till gång till egenskaps data i en funktion. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['at', number, array]` | värde | Hämtar ett objekt från en matris. |
| `['geometry-type']` | sträng | Hämtar funktionens geometri typ: punkt, MultiPoint, lin Est ring, MultiLineString, polygon, multipolygon. |
| `['get', string]` | värde | Hämtar egenskap svärdet från den aktuella funktionens egenskaper. Returnerar null om den begärda egenskapen saknas. |
| `['get', string, object]` | värde | Hämtar egenskap svärdet från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `['has', string]` | boolean | Anger om egenskaperna för en funktion har den angivna egenskapen. |
| `['has', string, object]` | boolean | Anger om egenskaperna för objektet har den angivna egenskapen. |
| `['id']` | värde | Hämtar funktionens ID om det har en sådan. |
| `['in', boolean | string | number, array]` | boolean | Anger om ett objekt finns i en matris |
| `['in', substring, string]` | boolean | Anger om en under sträng finns i en sträng |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | antal | Returnerar den första positionen vid vilken ett objekt kan hittas i en matris eller en under sträng kan hittas i en sträng, eller `-1` om det inte går att hitta indatatypen. Accepterar ett valfritt index varifrån sökningen ska påbörjas. |
| `['length', string | array]` | antal | Hämtar längden för en sträng eller en matris. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | sträng \| mat ris | Returnerar ett objekt från en matris eller en under sträng från en sträng från ett angivet start index eller mellan ett start index och ett slut index om det har angetts. Returvärdet är inkluderat i Start indexet, men inte slut indexet. |

**Exempel**

Egenskaperna för en funktion kan nås direkt i ett uttryck med hjälp av ett `get` uttryck. I det här exemplet används `zoneColor` värdet för funktionen för att ange färg egenskapen för ett bubbel lager. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Ovanstående exempel fungerar bra om alla punkt funktioner har `zoneColor` egenskapen. Om de inte gör det kommer färgen förmodligen att återgå till "svart". Om du vill ändra återställnings färgen använder du ett `case` uttryck i kombination med `has` uttrycket för att kontrol lera om egenskapen finns. Om egenskapen inte finns returnerar du en återställnings färg.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Bubbeldiagram och symbol lager kommer att återge koordinaterna för alla former i en data källa som standard. Det här beteendet kan markera hörnen för en polygon eller en linje. `filter`Alternativet för skiktet kan användas för att begränsa geometri typen för de funktioner som återges med hjälp av ett `['geometry-type']` uttryck i ett booleskt uttryck. I följande exempel begränsas ett bubbel lager så att endast `Point` funktioner återges.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

I följande exempel kan både `Point` och- `MultiPoint` funktionerna återges. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

På samma sätt kommer konturen för polygoner att återges i linje lager. Om du vill inaktivera det här beteendet i ett linje lager lägger du till ett filter som endast tillåter `LineString` och- `MultiLineString` funktioner.  

Här följer några ytterligare exempel på hur du använder data uttryck:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Matematiska uttryck

Matematiska uttryck tillhandahåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['+', number, number, …]` | antal | Beräknar summan av de angivna talen. |
| `['-', number]` | antal | Subtraherar 0 efter det angivna talet. |
| `['-', number, number]` | antal | Subtraherar de första talen med det andra talet. |
| `['*', number, number, …]` | antal | Multiplicerar de angivna talen tillsammans. |
| `['/', number, number]` | antal | Dividerar det första talet med det andra talet. |
| `['%', number, number]` | antal | Beräknar resten när det första talet divideras med det andra talet. |
| `['^', number, number]` | antal | Beräknar värdet för det första värdet upphöjt till det andra värdets potens. |
| `['abs', number]` | antal | Beräknar det absoluta värdet för det angivna talet. |
| `['acos', number]` | antal | Beräknar arcus cosinus för det angivna talet. |
| `['asin', number]` | antal | Beräknar arcus sinus för det angivna talet. |
| `['atan', number]` | antal | Beräknar arcus tangens för det angivna talet. |
| `['ceil', number]` | antal | Avrundar talet uppåt till närmaste heltal. |
| `['cos', number]` | antal | Beräknar cos för det angivna talet. |
| `['e']` | antal | Returnerar den matematiska konstanten `e` . |
| `['floor', number]` | antal | Avrundar talet nedåt till föregående heltal. |
| `['ln', number]` | antal | Beräknar den naturliga logaritmen för det angivna talet. |
| `['ln2']` | antal | Returnerar den matematiska konstanten `ln(2)` . |
| `['log10', number]` | antal | Beräknar basen 10-logaritmen för det angivna talet. |
| `['log2', number]` | antal | Beräknar basen – två logaritmen för det angivna talet. |
| `['max', number, number, …]` | antal | Beräknar det maximala antalet i den angivna uppsättningen tal. |
| `['min', number, number, …]` | antal | Beräknar det lägsta antalet i den angivna uppsättningen tal. |
| `['pi']` | antal | Returnerar den matematiska konstanten `PI` . |
| `['round', number]` | antal | Avrundar talet till närmaste heltal. Värdena för halvvägs avrundas från noll. `['round', -1.5]`Utvärdera till exempel `-2` . |
| `['sin', number]` | antal | Beräknar sinus för det angivna talet. |
| `['sqrt', number]` | antal | Beräknar kvadratroten ur det angivna talet. |
| `['tan', number]` | antal | Beräknar tangens för det angivna talet. |

## <a name="aggregate-expression"></a>Mängd uttryck

Ett mängd uttryck definierar en beräkning som bearbetas över en uppsättning data och kan användas med `clusterProperties` alternativet för en `DataSource` . Utdata från dessa uttryck måste vara ett tal eller ett booleskt värde. 

Ett agg regerings uttryck tar i tre värden: ett operator värde och ett start värde och ett uttryck för att hämta en egenskap från varje funktion i en data för att tillämpa den sammanställda åtgärden. Det här uttrycket har följande format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: en uttrycks funktion som sedan tillämpas på alla värden som beräknas av `mapExpression` för varje punkt i klustret. Operatorer som stöds: 
    - För siffror: `+` , `*` , `max` , `min`
    - För booleska värden: `all` , `any`
- initialValue: ett initialt värde där det första beräknade värdet aggregeras mot.
- mapExpression: ett uttryck som tillämpas mot varje punkt i data uppsättningen.

**Exempel**

Om alla funktioner i en data uppsättning har en `revenue` -egenskap, vilket är ett tal. Sedan kan den totala intäkten för alla punkter i ett kluster, som skapas från data uppsättningen, beräknas. Den här beräkningen görs med följande mängd uttryck: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Ackumulerat uttryck

`accumulated`Uttrycket hämtar värdet för en kluster egenskap som har ackumulerats hittills. Detta kan endast användas i `clusterProperties` en klustrad `DataSource` källas alternativ.

**Användning**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser.

När värden jämförs skrivs jämförelsen strikt. Värden av olika typer betraktas alltid som likvärdiga. De fall där typerna är kända för olika tidpunkter för parsar betraktas som ogiltiga och genererar ett parsningsfel. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Logisk negation. Returnerar `true` Om indatatypen är `false` och `false` Om indatatypen är `true` . |
| `['!=', value, value]` | boolean | Returnerar `true` Om indatavärdena inte är lika, `false` annars. |
| `['<', value, value]` | boolean | Returnerar `true` om den första indatamängden är strikt mindre än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['<=', value, value]` | boolean | Returnerar `true` om den första indatamängden är mindre än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['==', value, value]` | boolean | Returnerar `true` Om indatavärdena är identiska, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>', value, value]` | boolean | Returnerar `true` om den första indatamängden är exakt större än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['>=' value, value]` | boolean | Returnerar `true` om den första indatamängden är större än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `['all', boolean, boolean, …]` | boolean | Returnerar `true` om alla indata är `true` , `false` annars. |
| `['any', boolean, boolean, …]` | boolean | Returnerar `true` om någon av indatan är `true` , `false` annars. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | Returnerar `true` om den utvärderade funktionen är helt innesluten i en begränsning av en indatatyp, annars FALSE. Indatavärdet kan vara ett giltigt interjson-värde av typen `Polygon` , `MultiPolygon` , `Feature` eller `FeatureCollection` . Funktioner som stöds för utvärdering:<br/><br/>-Point: returnerar `false` om en punkt är på kanten eller faller utanför gränserna.<br/>-Lin Est ring: returnerar `false` om någon del av en linje faller utanför gränserna, överlappar linjen eller en linjes slut punkt på kanten. |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements.

Följande uttryck utför villkorliga logik åtgärder på indata. `case`Uttrycket innehåller exempelvis logiken "if/then/Else" medan `match` uttrycket liknar en "switch-sats". 

### <a name="case-expression"></a>Case-uttryck

Ett `case` uttryck är en typ av villkors uttryck som tillhandahåller "om/sedan/Else"-logik. Den här typen av uttrycks steg i en lista över booleska villkor. Det returnerar utmatning svärdet för det första booleska villkoret för att utvärdera till sant.

Följande pseudocode definierar strukturen för `case` uttrycket. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exempel**

I följande exempel går vi igenom olika booleska villkor tills den hittar en som utvärderas till `true` och returnerar sedan det associerade värdet. Om inget booleskt villkor utvärderas till `true` returneras ett återställnings värde. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Matchnings uttryck

Ett `match` uttryck är en typ av villkors uttryck som ger switch-instruktion som logik. Inmatade värden kan vara ett uttryck `['get', 'entityType']` som returnerar en sträng eller ett tal. Varje etikett måste antingen vara ett enstaka litteralt värde eller en matris med litterala värden, vars värden måste vara alla strängar eller alla siffror. Inmatarna matchar om något av värdena i mat ris matchningen. Varje etikett måste vara unik. Om indatatypen inte matchar typen av etiketter, blir resultatet värdet fallback.

Följande pseudocode definierar strukturen för `match` uttrycket. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exempel**

Följande exempel tittar på `entityType` egenskapen för en punkt funktion i ett Bubble-lager söker efter en matchning. Om en matchning hittas returneras det angivna värdet eller returneras återställnings värde.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

I följande exempel används en matris för att visa en uppsättning etiketter som alla ska returnera samma värde. Den här metoden är mycket mer effektiv än att lista varje etikett individuellt. I detta fall `entityType` returneras färgen "röd" om egenskapen är "restaurang" eller "grocery_store".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Sammanslagnings uttryck

Ett `coalesce` uttrycks steg genom en uppsättning uttryck tills det första värdet som inte är null hämtas och returnerar värdet. 

Följande pseudocode definierar strukturen för `coalesce` uttrycket. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exempel**

I följande exempel används ett `coalesce` uttryck för att ange `textField` alternativet för ett symbol lager. Om `title` egenskapen saknas i funktionen eller om den är inställd på `null` , försöker uttrycket sedan att söka efter `subTitle` egenskapen, om den saknas eller `null` , kommer den att återgå till en tom sträng. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

I följande exempel används ett `coalesce` uttryck för att hämta den första tillgängliga bild ikonen som är tillgänglig i kartan Sprite från en lista med angivna avbildnings namn.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Typuttryck

Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Objekt [] | Förutsätter att indatatypen är en matris. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | Förutsätter att indatavärdet är ett booleskt värde. Om flera värden anges utvärderas var och en i ordning tills ett booleskt värde hämtas. Om ingen av indatana är booleska värden är uttrycket ett fel. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | uppsamlare | Returnerar en sorterings hanterare för användning i språkrelaterade jämförelse åtgärder. De Skift läges känsliga och dia kritiska alternativen standard är falskt. Argumentet locale anger IETF-språktaggen för det språk som ska användas. Om inget anges används standard språket. Om det begärda språket inte är tillgängligt använder Sorteraren en systemdefinierad reserv språk inställning. Använd matchad – språkvariant för att testa resultatet av den nationella återställnings funktionen. |
| `['literal', array]`<br/><br/>`['literal', object]` | mat ris \| objekt | Returnerar en literal matris eller ett objekt värde. Använd det här uttrycket för att förhindra att en matris eller ett objekt utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt måste returneras av ett uttryck. |
| `['image', string]` | sträng | Kontrollerar om ett angivet bild-ID läses in i Maps-avbildningen Sprite. Om så är fallet returneras ID, annars returneras null. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | antal | Förutsätter att indatavärdet är ett tal. Om flera värden anges utvärderas var och en i ordning tills ett tal hämtas. Om ingen av indatana är siffror är uttrycket ett fel. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Objekt | Förutsätter att indatavärdet är ett objekt.  Om flera värden anges utvärderas var och en i ordning tills ett objekt hämtas. Om ingen av indatana är objekt är uttrycket ett fel. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | sträng | Förutsätter att indatavärdet är en sträng. Om flera värden anges utvärderas var och en i ordning tills en sträng hämtas. Om ingen av indatana är strängar är uttrycket ett fel. |
| `['to-boolean', value]` | boolean | Konverterar indatavärdet till ett booleskt värde. Resultatet är `false` när indatatypen är en tom sträng,,, `0` `false` `null` eller `NaN` ; i övrigt `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konverterar indatavärdet till en färg. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | antal | Konverterar indatavärdet till ett tal, om möjligt. Om indatatypen är `null` eller `false` , är resultatet 0. Om indatatypen är är `true` resultatet 1. Om indatatypen är en sträng konverteras den till ett tal med hjälp av funktionen [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) -sträng i språk specifikationen ECMAScript. Om flera värden anges utvärderas var och en i ordning tills den första lyckade konverteringen erhålls. Om ingen av indatana kan konverteras, är uttrycket ett fel. |
| `['to-string', value]` | sträng | Konverterar indatavärdet till en sträng. Om indatatypen är är `null` resultatet `""` . Om indatatypen är ett booleskt värde är resultatet `"true"` eller `"false"` . Om indatatypen är ett tal, konverteras den till en sträng med hjälp av funktionen [toString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number i språk specifikationen ECMAScript. Om indatatypen är en färg konverteras den till CSS RGBA-färg sträng `"rgba(r,g,b,a)"` . Annars konverteras inmatarna till en sträng med hjälp av [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) -funktionen i språk specifikationen ECMAScript. |
| `['typeof', value]` | sträng | Returnerar en sträng som beskriver typen för det aktuella värdet. |

> [!TIP]
> Om ett fel meddelande liknar det `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` som visas i webb läsar konsolen betyder det att det finns ett uttryck någonstans i koden som har en matris som inte har en sträng för det första värdet. Om du vill att uttrycket ska returnera en matris omsluter du matrisen med `literal` uttrycket. I följande exempel anges ikon `offset` alternativet för ett symbol lager, som måste vara en matris som innehåller två siffror, genom att använda ett `match` uttryck för att välja mellan två offset-värden baserat på värdet för  `entityType` egenskapen för punkt funktionen.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Färg uttryck

Färg uttryck gör det enklare att skapa och ändra färg värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Skapar ett färg värde från *röda*, *gröna* och *blå* komponenter som måste vara mellan `0` och och `255` en alpha-komponent i `1` . Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['rgba', number, number, number, number]` | color | Skapar ett färg värde från *röda*, *gröna*, *blå* komponenter som måste vara mellan `0` och och `255` en alpha-komponent inom ett intervall av `0` och `1` . Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `['to-rgba']` | \[Number, Number, Number, Number\] | Returnerar en matris med fyra element som innehåller indatamängdens *röda*, *gröna*, *blå* och *alpha* -komponenter i den ordningen. |

**Exempel**

I följande exempel skapas ett RGB-färgvärde som har ett *rött* värde av `255` och de *gröna* och *blå* värdena som beräknas genom att multipliceras `2.5` med `temperature` egenskapens värde. När temperaturen ändras ändras färgen till olika nyanser av *rött*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Uttryck för sträng operator

Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. 

| Uttryck | Returtyp | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | sträng | Sammanfogar flera strängar. Varje värde måste vara en sträng. Använd `to-string` typ uttrycket för att konvertera andra värde typer till sträng vid behov. |
| `['downcase', string]` | sträng | Konverterar den angivna strängen till gemener. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | Anger om Indatasträngen använder en tecken uppsättning som stöds av den aktuella teckensnitts stacken. Exempelvis: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | sträng | Returnerar IETF-språktaggen för språket som används av den angivna Sorteraren. Detta kan användas för att fastställa systemets standard språk, eller för att avgöra om ett begärt språk har lästs in. |
| `['upcase', string]` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel konverteras `temperature` egenskapen för punkt-funktionen till en sträng och sedan sammanfogas "°F" till slutet av den.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Uttrycket ovan återger en PIN-kod på kartan med texten "64 °F" som står ovanpå den som visas på bilden nedan.

<center>

![Uttrycks exempel ](media/how-to-expressions/string-operator-expression.png) för sträng operator </center>

## <a name="interpolate-and-step-expressions"></a>Interpolerade och steg uttryck

Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. Dessa uttryck tar i ett uttryck som returnerar ett numeriskt värde som indatatyp, till exempel `['get',  'temperature']` . Indatavärdet utvärderas mot par av indata-och utdataparametrar, för att avgöra vilket värde som passar bäst för funktionen interpolerad kurva eller steg. Värdena för utdata kallas för "stoppa". Indatavärdena för varje stopp måste vara ett tal och vara i stigande ordning. Värdena för utdata måste vara ett tal, en matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolerat uttryck

Ett `interpolate` uttryck kan användas för att beräkna en kontinuerlig, mjuk uppsättning värden genom interpolating mellan stopp värden. Ett `interpolate` uttryck som returnerar färg värden skapar en färg toning där resultat värden väljs.

Det finns tre typer av interpolation-metoder som kan användas i ett `interpolate` uttryck:
 
* `['linear']` – Interpolerar linjärt mellan stopp paret.
* `['exponential', base]` – Interpolerar exponentiellt mellan stoppen. `base`Värdet styr den hastighet som utdata ökar. Högre värden gör att utdata ökar till den övre delen av intervallet. Ett `base` värde nära 1 ger utdata som ökar linjärt.
* `['cubic-bezier', x1, y1, x2, y2]` – Interpolerar med en [kubikmeter-kurva](https://developer.mozilla.org/docs/Web/CSS/timing-function) som definieras av de angivna kontroll punkterna.

Här är ett exempel på hur de olika typerna av interpolerar ser ut. 

| Linjär  | Exponentiellt | Kubisk Bezier |
|---------|-------------|--------------|
| ![Diagram över linjär interpolation](media/how-to-expressions/linear-interpolation.png) | ![Diagram över exponentiell interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Diagram över kubisk bezierinterpolation](media/how-to-expressions/bezier-curve-interpolation.png) |

Följande pseudocode definierar strukturen för `interpolate` uttrycket. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exempel**

I följande exempel används ett `linear interpolate` uttryck för att ange `color` egenskapen för ett bubbel-lager baserat på `temperature` egenskapen för punkt funktionen. Om `temperature` värdet är mindre än 60 kommer "blått" att returneras. Om det är mellan 60 och mindre än 70 kommer gult att returneras. Om det är mellan 70 och mindre än 80 kommer "orange" att returneras. Om det är 80 eller högre returneras "röd".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Följande bild visar hur färgerna väljs för uttrycket ovan.
 
<center>

![Exempel ](media/how-to-expressions/interpolate-expression-example.png) på interpolerat uttryck </center>

### <a name="step-expression"></a>Steg uttryck

Ett `step` uttryck kan användas för att beräkna diskreta, stegvisa resultat värden genom att utvärdera en [PieceWise-konstant funktion](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) som definieras av stopp. 

Följande pseudocode definierar strukturen för `step` uttrycket. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Steg uttryck returnerar utdata för stoppet precis före indatavärdet eller det första indatavärdet om indata är mindre än det första steget. 

**Exempel**

I följande exempel används ett `step` uttryck för att ange `color` egenskapen för ett bubbel-lager baserat på `temperature` egenskapen för punkt funktionen. Om `temperature` värdet är mindre än 60 kommer "blått" att returneras. Om det är mellan 60 och mindre än 70 returneras "gult". Om det är mellan 70 och mindre än 80 kommer "orange" att returneras. Om det är 80 eller högre returneras "röd".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

Följande bild visar hur färgerna väljs för uttrycket ovan.
 
<center>

![Exempel på steg uttryck](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Lager-/regionsspecifika uttryck

Särskilda uttryck som endast gäller för specifika lager.

### <a name="heat-map-density-expression"></a>Uttryck för termisk kart täthet

Ett termiskt kart Täthets uttryck hämtar värdet för värme kartan för varje pixel i ett värme kart skikt och definieras som `['heatmap-density']` . Det här värdet är ett tal mellan `0` och `1` . Den används i kombination med ett `interpolation` eller- `step` uttryck för att definiera färg toningen som används för att färga värme kartan. Det här uttrycket kan bara användas i [färg alternativet](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) för värme kart skiktet.

> [!TIP]
> Färgen vid index 0, i ett interpolation-uttryck eller standard färgen för en steg färg definierar färgen för det utrymme där det inte finns några data. Färgen vid index 0 kan användas för att definiera en bakgrunds färg. Många föredrar att ange det här värdet som transparent eller en halv genomskinlig svart.

**Exempel**

I det här exemplet används ett liner-interpolation-uttryck för att skapa en mjuk färg toning för rendering av värme kartan. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Förutom att använda en mjuk toning för att färga en värme karta kan färger anges inom en uppsättning intervall med hjälp av ett `step` uttryck. `step`Om du använder ett uttryck för att färgsätta värme kartan visas det visuellt i områden som liknar en profil-eller radar format karta.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Mer information finns i dokumentationen för att [lägga till en termisk kart skikt](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Uttryck för linje förlopp

Ett uttryck för linje förlopp hämtar förloppet längs en övertoningsfyllning i ett linje lager och definieras som `['line-progress']` . Värdet är ett tal mellan 0 och 1. Den används i kombination med ett `interpolation` or- `step` uttryck. Det här uttrycket kan bara användas med [alternativet strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) för rad skiktet. 

> [!NOTE]
> Alternativet `strokeGradient` för linje skiktet kräver att du `lineMetrics` anger alternativet för den data källa som ska anges till `true` .

**Exempel**

I det här exemplet används `['line-progress']` uttrycket för att tillämpa en färg toning på linjen för en linje.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Se live-exempel](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Format uttryck för text fält

Format uttrycket för textfält kan användas med `textField` alternativet för egenskapen symbol lager `textOptions` för att ge blandad textformatering. Med det här uttrycket kan du ange en uppsättning med indatatyps strängar och formaterings alternativ. Följande alternativ kan anges för varje Indatasträngen i det här uttrycket.

 * `'font-scale'` – Anger skalnings faktorn för tecken storleken. Om det här värdet anges åsidosätts `size` egenskapen för för den `textOptions` enskilda strängen.
 * `'text-font'` -Anger en eller flera teckensnitts familjer som ska användas för den här strängen. Om det här värdet anges åsidosätts `font` egenskapen för för den `textOptions` enskilda strängen.

Följande pseudocode definierar strukturen för text fältets format uttryck. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Exempel**

I följande exempel formateras textfältet genom att lägga till ett fetstilt teckensnitt och skala upp tecken storleken för `title` egenskapen i funktionen. I det här exemplet läggs även `subTitle` egenskapen för funktionen till på en ny rad med en skalad nedåt tecken storlek.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Det här lagret återger punkt funktionen som visas på bilden nedan:
 
<center>

![Bild av punkt funktion med formaterat textfält ](media/how-to-expressions/text-field-format-expression.png)</center>

### <a name="number-format-expression"></a>Uttryck för tal format

`number-format`Uttrycket kan bara användas med `textField` ett symbol skikts alternativ. Det här uttrycket konverterar det angivna talet till en formaterad sträng. Det här uttrycket radbryter JavaScript-funktionen [number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) och har stöd för följande uppsättning alternativ.

 * `locale` – Ange det här alternativet för att konvertera siffror till strängar på ett sätt som justeras med det angivna språket. Skicka en [language-tagg för BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) till det här alternativet.
 * `currency` – Om du vill konvertera talet till en sträng som representerar en valuta. Möjliga värden är [ISO 4217-valuta koderna](https://en.wikipedia.org/wiki/ISO_4217), till exempel "USD" för amerikanska dollar, "EUR" för euron, eller "CNY" för kinesiska RMB.
 * `'min-fraction-digits'` – Anger det minsta antalet decimaler som ska ingå i sträng versionen av talet.
 * `'max-fraction-digits'` -Anger det maximala antalet decimaler som ska ingå i sträng versionen av talet.

Följande pseudocode definierar strukturen för text fältets format uttryck. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exempel**

I följande exempel används ett `number-format` uttryck för att ändra hur `revenue` egenskapen för punkt funktionen återges i `textField` alternativet för ett symbol lager så att det visas som ett US-dollar värde.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Det här lagret återger punkt funktionen som visas på bilden nedan:

<center>

![Uttrycks exempel ](media/how-to-expressions/number-format-expression.png) för tal format </center>

### <a name="image-expression"></a>Bild uttryck

Ett bild uttryck kan användas med `image` `textField` alternativen och för ett symbol lager och `fillPattern` alternativet för polygon-lagret. Det här uttrycket kontrollerar att den begärda avbildningen finns i formatet och returnerar antingen det matchade bild namnet eller `null` , beroende på om bilden för närvarande är i formatet. Den här verifierings processen är synkron och kräver att avbildningen har lagts till i formatet innan den kan begäras i argumentet avbildning.

**Exempel**

I följande exempel används ett `image` uttryck för att lägga till en ikon infogad med text i ett symbol lager. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Det här lagret återger textfältet i symbol skiktet som visas på bilden nedan:

<center>

![Exempel ](media/how-to-expressions/image-expression.png) på bild uttryck </center>

## <a name="zoom-expression"></a>Uttryck för zoomning

Ett `zoom` uttryck används för att hämta den aktuella zoomnings nivån för kartan vid åter givnings tiden och definieras som `['zoom']` . Det här uttrycket returnerar ett tal mellan kartans minsta och högsta zoomnings nivå intervall. Azure Maps interaktiva kart kontroller för webb-och Android-stöd 25 zoomnings nivåer, numrerade 0 till 24. Genom att använda `zoom` uttrycket kan du ändra formaten dynamiskt när kart nivån för kartan ändras. `zoom`Uttrycket får bara användas med och- `interpolate` `step` uttryck.

**Exempel**

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När kartan är insamlad är data aggarna tillsammans och värme kart lagret annorlunda. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnings nivå så att varje data punkt täcker samma fysiska område i kartan. Det gör att värme kart lagret ser mer statisk och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. Om du skalar radien, så att den dubbleras med varje zoomnivå, skapas en värme karta som ser konsekvent ut på alla zoomnings nivåer. Det kan åstadkommas med hjälp av `zoom` uttrycket med ett `base 2 exponential interpolation` uttryck, med bild punkts radien för den minsta zoomnings nivån och en skalad radie för den högsta zoomnings nivån som `2 * Math.pow(2, minZoom - maxZoom)` visas nedan.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Se live-exempel](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Uttryck för variabel bindning

Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel. Så att beräknings resultaten kan refereras till någon annan stans i ett uttryck flera gånger. Det är en bra optimering för uttryck som omfattar många beräkningar.

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: sträng,<br/>&nbsp;&nbsp;&nbsp;&nbsp;värde2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Lagrar ett eller flera värden som variabler som används av `var` uttrycket i det underordnade uttrycket som returnerar resultatet. |
| `['var', name: string]` | valfri | Refererar till en variabel som skapats med `let` uttrycket. |

**Exempel**

I det här exemplet används ett uttryck som beräknar intäkterna relativt temperatur förhållandet och använder sedan ett `case` uttryck för att utvärdera olika booleska åtgärder för det här värdet. `let`Uttrycket används för att lagra intäkterna i förhållande till temperatur förhållandet, så att det bara behöver beräknas en gång. `var`Uttrycket refererar till den här variabeln så ofta som det behövs utan att du behöver beräkna om den.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fler kod exempel som implementerar uttryck:

> [!div class="nextstepaction"] 
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)

Läs mer om de lager alternativ som stöder uttryck:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)