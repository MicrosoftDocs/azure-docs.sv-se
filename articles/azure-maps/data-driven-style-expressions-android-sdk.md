---
title: Data drivna format uttryck i Android Maps | Microsoft Azure Maps
description: Lär dig mer om data drivna format uttryck. Se hur du använder dessa uttryck i Azure Maps Android SDK för att justera formaten i Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 61d7a295d86fd7da74dee03cd35c79feea0218ed
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681733"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Data drivna format uttryck (Android SDK)

Med uttryck kan du tillämpa affärs logik för att formatera alternativ som ser till att egenskaperna som definierats i varje form i en data källa. Uttryck kan filtrera data i en data källa eller ett lager. Uttryck kan bestå av villkorsstyrd logik, t. ex. if-Statements. Och kan användas för att manipulera data med hjälp av: sträng operatorer, logiska operatorer och matematiska operatorer.

Data drivna format minskar mängden kod som behövs för att implementera affärs logik kring formatering. När det används med lager utvärderas uttryck vid åter givnings tiden i en separat tråd. Den här funktionen ger ökad prestanda jämfört med att utvärdera affärs logiken i UI-tråden.

Azure Maps Android SDK har stöd för nästan alla samma format uttryck som Azure Maps webb-SDK, så alla samma koncept som beskrivs i de [data drivna stil uttrycken (webb-SDK)](data-driven-style-expressions-web-sdk.md) kan överföras till en Android-app. Alla format uttryck i Azure Maps Android SDK är tillgängliga under `com.microsoft.azure.maps.mapcontrol.options.Expression` namn området. Det finns många olika typer av format uttryck.

| Typ av uttryck | Beskrivning |
|---------------------|-------------|
| [Booleska uttryck](#boolean-expressions) | Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser. |
| [Färg uttryck](#color-expressions) | Färg uttryck gör det enklare att skapa och ändra färg värden. |
| [Villkorliga uttryck](#conditional-expressions) | Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements. |
| [Data uttryck](#data-expressions) | Ger åtkomst till egenskaps data i en funktion. |
| [Interpolerade och steg uttryck](#interpolate-and-step-expressions) | Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. |
| [JSON-baserade uttryck](#json-based-expressions) | Gör det enkelt att återanvända Style RAW JSON-baserade uttryck som skapats för webb-SDK med Android SDK. |  
| [Lagerbaserat uttryck](#layer-specific-expressions) | Särskilda uttryck som endast gäller för ett enskilt lager. |
| [Matematiska uttryck](#math-expressions) | Innehåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket. |
| [Uttryck för sträng operator](#string-operator-expressions) | Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. |
| [Typuttryck](#type-expressions) | Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden. |
| [Uttryck för variabel bindning](#variable-binding-expressions) | Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel och refereras till i ett uttryck flera gånger utan att behöva beräkna om det lagrade värdet. |
| [Uttryck för zoomning](#zoom-expression) | Hämtar den aktuella zoomnings nivån för kartan vid åter givnings tiden. |

Alla exempel i det här avsnittet av dokumentet använder följande funktion för att demonstrera olika sätt att använda dessa uttryck.

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

Följande kod visar hur du manuellt skapar den här interjson-funktionen i en app.

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

Följande kod visar hur du deserialiserar stringified-versionen av JSON-objektet till en interjson-funktion i en app.

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>JSON-baserade uttryck

Azure Maps Web SDK stöder också data drivna format uttryck som representeras med en JSON-matris. Samma uttryck kan återskapas med hjälp av den inbyggda `Expression` klassen i Android SDK. Alternativt kan dessa JSON-baserade uttryck konverteras till en sträng med hjälp av en webb funktion som `JSON.stringify` och skickas till- `Expression.raw(String rawExpression)` metoden. Ta till exempel följande JSON-uttryck.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

Stringified-versionen av uttrycket ovan skulle vara `"['get','title']"` och kan läsas in i Android SDK på följande sätt.

```java
Expression exp = Expression.raw("['get','title']")
```

Med den här metoden kan du enkelt återanvända format uttryck mellan mobil-och webbappar som använder Azure Maps.

Den här videon ger en översikt över data driven format i Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Data uttryck

Data uttryck ger till gång till egenskaps data i en funktion.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `accumulated()` | antal | Hämtar värdet för en kluster egenskap som har ackumulerats hittills. |
| `at(number | Expression, Expression)` | värde | Hämtar ett objekt från en matris. |
| `geometryType()` | sträng | Hämtar funktionens geometri typ: punkt, MultiPoint, lin Est ring, MultiLineString, polygon, multipolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | värde | Hämtar egenskap svärdet från egenskaperna för det angivna objektet. Returnerar null om den begärda egenskapen saknas. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Anger om egenskaperna för en funktion har den angivna egenskapen. |
| `id()` | värde | Hämtar funktionens ID om det har en sådan. |
| `in(string | number | Expression, Expression)` | boolean | Anger om ett objekt finns i en matris |
| `length(string | Expression)` | antal | Hämtar längden för en sträng eller en matris. |
| `properties()`| värde | Hämtar objektet med funktions egenskaper. |

Följande webb-SDK-formatmallar stöds inte i Android SDK:

- index-för
- sektor

**Exempel**

Egenskaperna för en funktion kan nås direkt i ett uttryck med hjälp av ett `get` uttryck. I det här exemplet används `zoneColor` värdet för funktionen för att ange färg egenskapen för ett bubbel lager.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

Ovanstående exempel fungerar bra om alla punkt funktioner har `zoneColor` egenskapen. Om de inte gör det kommer färgen förmodligen att återgå till "svart". Om du vill ändra återställnings färgen använder du ett `switchCase` uttryck i kombination med `has` uttrycket för att kontrol lera om egenskapen finns. Om egenskapen inte finns returnerar du en återställnings färg.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

Bubbeldiagram och symbol lager kommer att återge koordinaterna för alla former i en data källa som standard. Det här beteendet kan markera hörnen för en polygon eller en linje. `filter`Alternativet för skiktet kan användas för att begränsa geometri typen för de funktioner som återges med hjälp av ett `geometryType` uttryck i ett booleskt uttryck. I följande exempel begränsas ett bubbel lager så att endast `Point` funktioner återges.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

I följande exempel kan både `Point` och- `MultiPoint` funktionerna återges. 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

På samma sätt kommer konturen för polygoner att återges i linje lager. Om du vill inaktivera det här beteendet i ett linje lager lägger du till ett filter som endast tillåter `LineString` och- `MultiLineString` funktioner.  

Här följer några ytterligare exempel på hur du använder data uttryck:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Matematiska uttryck

Matematiska uttryck tillhandahåller matematiska operatorer för att utföra data drivna beräkningar i uttrycks ramverket.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `abs(number | Expression)` | antal | Beräknar det absoluta värdet för det angivna talet. |
| `acos(number | Expression)` | antal | Beräknar arcus cosinus för det angivna talet. |
| `asin(number | Expression)` | antal | Beräknar arcus sinus för det angivna talet. |
| `atan(number | Expression)` | antal | Beräknar arcus tangens för det angivna talet. |
| `ceil(number | Expression)` | antal | Avrundar talet uppåt till närmaste heltal. |
| `cos(number | Expression)` | antal | Beräknar cos för det angivna talet. |
| `division(number, number)` \| `division(Expression, Expression)` | antal | Dividerar det första talet med det andra talet. Uttryck för Web SDK-motsvarighet: `/` |
| `e()` | antal | Returnerar den matematiska konstanten `e` . |
| `floor(number | Expression)` | antal | Avrundar talet nedåt till föregående heltal. |
| `log10(number | Expression)` | antal | Beräknar basen 10-logaritmen för det angivna talet. |
| `log2(number | Expression)` | antal | Beräknar basen – två logaritmen för det angivna talet. |
| `ln(number | Expression)` | antal | Beräknar den naturliga logaritmen för det angivna talet. |
| `ln2()` | antal | Returnerar den matematiska konstanten `ln(2)` . |
| `max(numbers... | expressions...)` | antal | Beräknar det maximala antalet i den angivna uppsättningen tal. |
| `min(numbers... | expressions...)` | antal | Beräknar det lägsta antalet i den angivna uppsättningen tal. |
| `mod(number, number)` \| | `mod(Expression, Expression)` | antal | Beräknar resten när det första talet divideras med det andra talet. Uttryck för Web SDK-motsvarighet: `%` |
| `pi()` | antal | Returnerar den matematiska konstanten `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | antal | Beräknar värdet för det första värdet upphöjt till det andra värdets potens. |
| `product(numbers... | expressions...)` | antal | Multiplicerar de angivna talen tillsammans. Uttryck för Web SDK-motsvarighet: `*` |
| `round(number | Expression)` | antal | Avrundar talet till närmaste heltal. Värdena för halvvägs avrundas från noll. `round(-1.5)`Utvärdera till exempel `-2` . |
| `sin(number | Expression)` | antal | Beräknar sinus för det angivna talet. |
| `sqrt(number | Expression)` | antal | Beräknar kvadratroten ur det angivna talet. |
| `subtract(number | Expression` | antal | Subtraherar 0 efter det angivna talet. |
| `subtract(number | Expression, number | Expression)` | antal | Subtraherar de första talen med det andra talet. |
| `sum(numbers... | expressions...)` | antal | Beräknar summan av de angivna talen. |
| `tan(number | Expression)` | antal | Beräknar tangens för det angivna talet. |

## <a name="boolean-expressions"></a>Booleska uttryck

Booleska uttryck tillhandahåller en uppsättning booleska operator uttryck för att utvärdera booleska jämförelser.

När värden jämförs skrivs jämförelsen strikt. Värden av olika typer betraktas alltid som likvärdiga. De fall där typerna är kända för olika tidpunkter för parsar betraktas som ogiltiga och genererar ett parsningsfel.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Returnerar `true` om alla indata är `true` , `false` annars. |
| `any(Expression...)` | boolean | Returnerar `true` om någon av indatan är `true` , `false` annars. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` Om indatavärdena är identiska, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` om den första indatamängden är exakt större än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` om den första indatamängden är större än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` om den första indatamängden är strikt mindre än den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` om den första indatamängden är mindre än eller lika med den andra, `false` annars. Argumenten måste vara antingen båda strängarna eller båda talen. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Returnerar `true` Om indatavärdena inte är lika, `false` annars. |
| `not(Expression | boolean)` | boolean | Logisk negation. Returnerar `true` Om indatatypen är `false` och `false` Om indatatypen är `true` . |

## <a name="conditional-expressions"></a>Villkorliga uttryck

Villkors uttryck tillhandahåller logik åtgärder som liknar IF-Statements.

Följande uttryck utför villkorliga logik åtgärder på indata. `switchCase`Uttrycket innehåller exempelvis logiken "if/then/Else" medan `match` uttrycket liknar en "switch-sats". 

### <a name="switch-case-expression"></a>Uttryck för växel fall

Ett `switchCase` uttryck är en typ av villkors uttryck som tillhandahåller "om/sedan/Else"-logik. Den här typen av uttrycks steg i en lista över booleska villkor. Det returnerar utmatning svärdet för det första booleska villkoret för att utvärdera till sant.

Följande pseudocode definierar strukturen för `switchCase` uttrycket.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Exempel**

I följande exempel går vi igenom olika booleska villkor tills den hittar en som utvärderas till `true` och returnerar sedan det associerade värdet. Om inget booleskt villkor utvärderas till `true` returneras ett återställnings värde.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Matchnings uttryck

Ett `match` uttryck är en typ av villkors uttryck som ger switch-instruktion som logik. Inmatade värden kan vara ett uttryck `get( "entityType")` som returnerar en sträng eller ett tal. Varje stopp måste ha en etikett som antingen är ett enda litteralt värde eller en matris med litterala värden, vars värden måste vara alla strängar eller alla siffror. Inmatarna matchar om något av värdena i mat ris matchningen. Varje stopp etikett måste vara unik. Om indatatypen inte matchar typen av etiketter, blir resultatet standard återställnings värde.

Följande pseudocode definierar strukturen för `match` uttrycket.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Exempel**

Följande exempel tittar på `entityType` egenskapen för en punkt funktion i ett Bubble-lager söker efter en matchning. Om en matchning hittas returneras det angivna värdet eller returneras återställnings värde.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

I följande exempel används en matris för att visa en uppsättning etiketter som alla ska returnera samma värde. Den här metoden är mycket mer effektiv än att lista varje etikett individuellt. I detta fall `entityType` returneras färgen "röd" om egenskapen är "restaurang" eller "grocery_store".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>Sammanslagnings uttryck

Ett `coalesce` uttrycks steg genom en uppsättning uttryck tills det första värdet som inte är null hämtas och returnerar värdet.

Följande pseudocode definierar strukturen för `coalesce` uttrycket.

```java
coalesce(Expression... input)
```

**Exempel**

I följande exempel används ett `coalesce` uttryck för att ange `textField` alternativet för ett symbol lager. Om `title` egenskapen saknas i funktionen eller om den är inställd på `null` , försöker uttrycket sedan att söka efter `subTitle` egenskapen, om den saknas eller `null` , kommer den att återgå till en tom sträng. 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>Typuttryck

Typ uttryck innehåller verktyg för att testa och konvertera olika data typer, t. ex. strängar, siffror och booleska värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `array(Expression)` | Objekt [] | Förutsätter att indatatypen är en matris. |
| `bool(Expression)` | boolean | Förutsätter att indatavärdet är ett booleskt värde. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | uppsamlare | Returnerar en sorterings hanterare för användning i språkrelaterade jämförelse åtgärder. De Skift läges känsliga och dia kritiska alternativen standard är falskt. Argumentet locale anger IETF-språktaggen för det språk som ska användas. Om inget anges används standard språket. Om det begärda språket inte är tillgängligt använder Sorteraren en systemdefinierad reserv språk inställning. Använd matchad – språkvariant för att testa resultatet av den nationella återställnings funktionen.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | objekt för Boolean Number-String-objekt \| \| \| \| [] | Returnerar en literal matris eller ett objekt värde. Använd det här uttrycket för att förhindra att en matris eller ett objekt utvärderas som ett uttryck. Detta är nödvändigt när en matris eller ett objekt måste returneras av ett uttryck. |
| `number(Expression)` | antal | Förutsätter att indatavärdet är ett tal. |
| `object(Expression)` | Objekt | Förutsätter att indatavärdet är ett objekt. |
| `string(Expression)` | sträng | Förutsätter att indatavärdet är en sträng. |
| `toArray(Expression)` | Objekt [] | Konverterar uttrycket till en JSON-objekt mat ris. |
| `toBool(Expression)` | boolean | Konverterar indatavärdet till ett booleskt värde. |
| `toNumber(Expression)` | antal | Konverterar indatavärdet till ett tal, om möjligt. |
| `toString(Expression)` | sträng | Konverterar indatavärdet till en sträng. |
| `typeoOf(Expression)` | sträng | Returnerar en sträng som beskriver typen för det aktuella värdet. |

## <a name="color-expressions"></a>Färg uttryck

Färg uttryck gör det enklare att skapa och ändra färg värden.

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `color(int)` | color | Konverterar ett färg heltals värde till ett färg uttryck. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Skapar ett färg värde från *röda*, *gröna* och *blå* komponenter som måste vara mellan `0` och och `255` en alpha-komponent i `1` . Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Skapar ett färg värde från *röda*, *gröna*, *blå* komponenter som måste vara mellan `0` och och `255` en alpha-komponent inom ett intervall av `0` och `1` . Om någon komponent är utanför intervallet är uttrycket ett fel. |
| `toColor(Expression)` | color  | Konverterar indatavärdet till en färg. |
| `toRgba(Expression)` | color | Returnerar en matris med fyra element som innehåller indatamängdens röda, gröna, blå och alpha-komponenter i den ordningen. |

**Exempel**

I följande exempel skapas ett RGB-färgvärde som har ett *rött* värde av `255` och de *gröna* och *blå* värdena som beräknas genom att multipliceras `2.5` med `temperature` egenskapens värde. När temperaturen ändras ändras färgen till olika nyanser av *rött*.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

Om alla färg parametrar är siffror behöver du inte figursätta dem med `literal` uttrycket. Exempel:

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> Sträng färg värden kan konverteras till en färg med hjälp av `android.graphics.Color.parseColor` metoden. Följande konverterar en hexadecimal färg sträng till ett färg uttryck som kan användas med ett lager.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Uttryck för sträng operator

Uttryck för sträng operatorer utför konverterings åtgärder på strängar som sammanfogning och konvertering av ärendet. 

| Uttryck | Returtyp | Beskrivning |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | sträng | Sammanfogar flera strängar. Varje värde måste vara en sträng. Använd `toString` typ uttrycket för att konvertera andra värde typer till sträng vid behov. |
| `downcase(string)` \| `downcase(Expression)` | sträng | Konverterar den angivna strängen till gemener. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Anger om Indatasträngen använder en tecken uppsättning som stöds av den aktuella teckensnitts stacken. Exempelvis: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | sträng | Returnerar IETF-språktaggen för språket som används av den angivna Sorteraren. Detta kan användas för att fastställa systemets standard språk, eller för att avgöra om ett begärt språk har lästs in. |
| `upcase(string)` \| `upcase(Expression)` | sträng | Konverterar den angivna strängen till versaler. |

**Exempel**

I följande exempel konverteras `temperature` egenskapen för punkt-funktionen till en sträng och sedan sammanfogas "°F" till slutet av den.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

Uttrycket ovan återger en PIN-kod på kartan med texten "64 °F" som står ovanpå den som visas på bilden nedan.

![Uttrycks exempel för sträng operator](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Interpolerade och steg uttryck

Interpolerade och steg uttryck kan användas för att beräkna värden längs en interpolerad kurva eller en steg funktion. Dessa uttryck tar i ett uttryck som returnerar ett numeriskt värde som indatatyp, till exempel `get("temperature")` . Indatavärdet utvärderas mot par av indata-och utdataparametrar, för att avgöra vilket värde som passar bäst för funktionen interpolerad kurva eller steg. Värdena för utdata kallas för "stoppa". Indatavärdena för varje stopp måste vara ett tal och vara i stigande ordning. Värdena för utdata måste vara ett tal, en matris med tal eller en färg.

### <a name="interpolate-expression"></a>Interpolerat uttryck

Ett `interpolate` uttryck kan användas för att beräkna en kontinuerlig, mjuk uppsättning värden genom interpolating mellan stopp värden. Ett `interpolate` uttryck som returnerar färg värden skapar en färg toning där resultat värden väljs. `interpolate`Uttrycket har följande format:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Det finns tre typer av interpolation-metoder som kan användas i ett `interpolate` uttryck:

| Name | Beskrivning | 
|------|-------------|
| `linear()` | Interpolerar linjärt mellan stopp paret.  |
| `exponential(number)` \| `exponential(Expression)` | Interpolerar exponentiellt mellan stoppen. En "base" anges och styr den hastighet som utdata ökar. Högre värden gör att utdata ökar till den övre delen av intervallet. Ett "bas"-värde nära 1 ger en utmatning som ökar linjärt.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpolerar med en [kubikmeter](https://developer.mozilla.org/docs/Web/CSS/timing-function) som definieras av de angivna kontroll punkterna. |

`stop`Uttrycket har formatet `stop(stop, value)` .
 
Här är ett exempel på hur de olika typerna av interpolerar ser ut. 

| Linjär  | Exponentiellt | Kubisk Bezier |
|---------|-------------|--------------|
| ![Diagram över linjär interpolation](media/how-to-expressions/linear-interpolation.png) | ![Diagram över exponentiell interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Diagram över kubisk bezierinterpolation](media/how-to-expressions/bezier-curve-interpolation.png) |

**Exempel**

I följande exempel används ett `linear interpolate` uttryck för att ange `bubbleColor` egenskapen för ett bubbel-lager baserat på `temperature` egenskapen för punkt funktionen. Om `temperature` värdet är mindre än 60 kommer "blått" att returneras. Om det är mellan 60 och mindre än 70 kommer gult att returneras. Om det är mellan 70 och mindre än 80 kommer "orange" ( `#FFA500` ) att returneras. Om det är 80 eller högre returneras "röd".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Följande bild visar hur färgerna väljs för uttrycket ovan.

![Exempel på interpolerat uttryck](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Steg uttryck

Ett `step` uttryck kan användas för att beräkna diskreta, stegvisa resultat värden genom att utvärdera en [PieceWise-konstant funktion](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) som definieras av stopp. 

`interpolate`Uttrycket har följande format:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Steg uttryck returnerar utdata för stoppet precis före indatavärdet eller det första indatavärdet om indata är mindre än det första steget. 

**Exempel**

I följande exempel används ett `step` uttryck för att ange `bubbleColor` egenskapen för ett bubbel-lager baserat på `temperature` egenskapen för punkt funktionen. Om `temperature` värdet är mindre än 60 kommer "blått" att returneras. Om det är mellan 60 och mindre än 70 returneras "gult". Om det är mellan 70 och mindre än 80 kommer "orange" att returneras. Om det är 80 eller högre returneras "röd".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Följande bild visar hur färgerna väljs för uttrycket ovan.
 
![Exempel på steg uttryck](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Lager-/regionsspecifika uttryck

Särskilda uttryck som endast gäller för specifika lager.

### <a name="heat-map-density-expression"></a>Uttryck för termisk kart täthet

Ett termiskt kart Täthets uttryck hämtar värdet för värme kartan för varje pixel i ett värme kart skikt och definieras som `heatmapDensity` . Det här värdet är ett tal mellan `0` och `1` . Den används i kombination med ett `interpolation` eller- `step` uttryck för att definiera färg toningen som används för att färga värme kartan. Det här uttrycket kan bara användas i `heatmapColor` alternativ för värme kart skiktet.

> [!TIP]
> Färgen vid index 0, i ett interpolation-uttryck eller standard färgen för en steg färg definierar färgen för det utrymme där det inte finns några data. Färgen vid index 0 kan användas för att definiera en bakgrunds färg. Många föredrar att ange det här värdet som transparent eller en halv genomskinlig svart.

**Exempel**

I det här exemplet används ett liner-interpolation-uttryck för att skapa en mjuk färg toning för rendering av värme kartan. 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

Förutom att använda en mjuk toning för att färga en värme karta kan färger anges inom en uppsättning intervall med hjälp av ett `step` uttryck. `step`Om du använder ett uttryck för att färgsätta värme kartan visas det visuellt i områden som liknar en profil-eller radar format karta.  

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

Mer information finns i dokumentationen för att [lägga till en termisk kart skikt](map-add-heat-map-layer-android.md) .

### <a name="text-field-format-expression"></a>Format uttryck för text fält

`format`Uttrycket kan användas med `textField` alternativet för symbol skiktet för att ge blandad textformatering. Det här uttrycket tar i ett eller flera `formatEntry` uttryck som anger en sträng och en uppsättning som `formatOptions` ska läggas till i textfältet.

| Uttryck | Beskrivning |
|------------|-------------|
| `format(Expression...)` | Returnerar formaterad text som innehåller anteckningar för användning i text fält i blandat format. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Returnerar en formaterad sträng post som används i `format` uttrycket. |

Följande format alternativ är tillgängliga:

| Uttryck | Beskrivning |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Anger skalnings faktorn för tecken storleken. Om det här värdet anges åsidosätts `textSize` egenskapen för den enskilda strängen. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Anger en färg som ska användas för en text vid åter givning. |

**Exempel**

I följande exempel formateras textfältet genom att lägga till ett fetstilt teckensnitt och skala upp tecken storleken för `title` egenskapen i funktionen. I det här exemplet läggs även `subTitle` egenskapen för funktionen till på en ny rad med en skalad nedåt tecken storlek.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

Det här lagret återger punkt funktionen som visas på bilden nedan:

![Bild av punkt funktion med formaterat textfält](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Uttryck för zoomning

Ett `zoom` uttryck används för att hämta den aktuella zoomnings nivån för kartan vid åter givnings tiden och definieras som `zoom()` . Det här uttrycket returnerar ett tal mellan kartans minsta och högsta zoomnings nivå intervall. Azure Maps interaktiva kart kontroller för webb-och Android-stöd 25 zoomnings nivåer, numrerade 0 till 24. Genom att använda `zoom` uttrycket kan du ändra formaten dynamiskt när kart nivån för kartan ändras. `zoom`Uttrycket får bara användas med och- `interpolate` `step` uttryck.

**Exempel**

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När kartan är insamlad är data aggarna tillsammans och värme kart lagret annorlunda. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnings nivå så att varje data punkt täcker samma fysiska område i kartan. Det gör att värme kart lagret ser mer statisk och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. Om du skalar radien, så att den dubbleras med varje zoomnivå, skapas en värme karta som ser konsekvent ut på alla zoomnings nivåer. Det kan åstadkommas med hjälp av `zoom` uttrycket med ett `base 2 exponential interpolation` uttryck, med bild punkts radien för den minsta zoomnings nivån och en skalad radie för den högsta zoomnings nivån som `2 * Math.pow(2, minZoom - maxZoom)` visas nedan.

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>Uttryck för variabel bindning

Variabel bindnings uttryck lagrar resultatet av en beräkning i en variabel. Så att beräknings resultaten kan refereras till någon annan stans i ett uttryck flera gånger. Det är en bra optimering för uttryck som omfattar många beräkningar.

| Uttryck | Returtyp | Beskrivning |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Lagrar ett eller flera värden som variabler som används av `var` uttrycket i det underordnade uttrycket som returnerar resultatet. |
| `var(Expression expression)` \| `var(string variableName)` | Objekt | Refererar till en variabel som skapats med `let` uttrycket. |

**Exempel**

I det här exemplet används ett uttryck som beräknar intäkterna relativt temperatur förhållandet och använder sedan ett `case` uttryck för att utvärdera olika booleska åtgärder för det här värdet. `let`Uttrycket används för att lagra intäkterna i förhållande till temperatur förhållandet, så att det bara behöver beräknas en gång. `var`Uttrycket refererar till den här variabeln så ofta som det behövs utan att du behöver beräkna om den.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de lager som stöder uttryck:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägg till en värme karta](map-add-heat-map-layer-android.md)
