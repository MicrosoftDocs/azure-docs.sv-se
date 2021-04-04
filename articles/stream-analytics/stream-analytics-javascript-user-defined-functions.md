---
title: Användardefinierade funktioner i Azure Stream Analytics JavaScript
description: Den här artikeln är en introduktion till användardefinierade JavaScript-funktioner i Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020391"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Användardefinierade JavaScript-funktioner i Azure Stream Analytics
 
Azure Stream Analytics stödjer användardefinierade JavaScript-funktioner. Med den omfattande uppsättningen **sträng**-, **regexp**-, **matematik**-, **matris**-och **datum** metoder som Java Script tillhandahåller, blir det enklare att skapa komplexa data transformationer med Stream Analytics jobb.

## <a name="overview"></a>Översikt

Användardefinierade JavaScript-funktioner har stöd för tillstånds lösa, endast Compute-skalära funktioner som inte kräver extern anslutning. Returvärdet för en funktion kan endast vara ett skalärvärde (enkelt). När du lägger till en användardefinierad JavaScript-funktion i ett jobb kan använda funktionen var som helst i frågan, som en inbyggd skalärfunktion.

Här följer några scenarier där användardefinierade JavaScript-funktioner kan vara användbara:
* Tolka och manipulera strängar som har funktioner med reguljära uttryck, till exempel **Regexp_Replace()** och **Regexp_Extract()**
* Avkoda och koda data, till exempel konvertering av binärt format till hexadecimalt
* Utföra matematiska-beräkningar med JavaScript- **matematiska** funktioner
* Utföra mat ris åtgärder som att sortera, delta, hitta och fylla

Här är några saker som du inte kan göra med en användardefinierad JavaScript-funktion i Stream Analytics:
* Anropa externa REST-slutpunkter, till exempel genom att göra omvänd IP-sökning eller hämta referens data från en extern källa
* Utför anpassade serialisering eller avserialisering av händelseformat på indata/utdata
* Skapa anpassade samlingar

Även om functions som **date. GetDate ()** eller **Math. Random ()** inte är blockerade i funktions definitionen, bör du undvika att använda dem. Dessa funktioner returnerar **inte** samma resultat varje gång du anropar dem och Azure Stream Analytics tjänsten behåller inte en journal över funktions anrop och returnerade resultat. Om en funktion returnerar olika resultat för samma händelser, garanteras repeterbarhet inte när ett jobb startas om av dig eller av tjänsten Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Lägg till en användardefinierad JavaScript-funktion i jobbet

> [!NOTE]
> De här stegen fungerar på Stream Analytics jobb som kon figurer ATS för att köras i molnet. Om Stream Analytics jobbet är konfigurerat för att köras på Azure IoT Edge ska du i stället använda Visual Studio och [skriva den användardefinierade funktionen med C#](stream-analytics-edge-csharp-udf.md).

Om du vill skapa en användardefinierad JavaScript-funktion i Stream Analytics jobb väljer du **funktioner** under **jobb sto pol Ogin**. Välj sedan **JavaScript UDF** från List menyn **+ Lägg till** . 

![Lägg till Java Script UDF](./media/javascript/stream-analytics-jsudf-add.png)

Du måste ange följande egenskaper och välja **Spara**.

|Egenskap|Beskrivning|
|--------|-----------|
|Funktions Ali Aset|Ange ett namn för att anropa funktionen i frågan.|
|Utdatatyp|Typ som ska returneras av den användardefinierade JavaScript-funktionen till din Stream Analytics-fråga.|
|Funktions definition|Implementering av JavaScript-funktionen som ska köras varje gången UDF-funktionen anropas från din fråga.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testa och felsöka JavaScript-UDF: er 

Du kan testa och felsöka din JavaScript UDF-logik i valfri webbläsare. Det finns för närvarande inte stöd för att felsöka och testa logiken för dessa användardefinierade funktioner i Stream Analytics Portal. När funktionen fungerar som förväntat kan du lägga till den i Stream Analytics-jobbet som nämnts ovan och sedan anropa den direkt från din fråga. Du kan testa din fråge logik med Java Script UDF med [Stream Analytics verktyg för Visual Studio](./stream-analytics-tools-for-visual-studio-install.md).

JavaScript-körningsfel betraktas som allvarliga och exponeras via aktivitetsloggen. För att hämta loggen i Azure Portal går du till jobbet och väljer **aktivitetsloggen**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Anropa en användardefinierad JavaScript-funktion i en fråga

Du kan enkelt anropa JavaScript-funktionen i din fråga med hjälp av funktions Ali Aset prefixet med **UDF**. Här är ett exempel på en JavaScript UDF som konverterar hexadecimala värden till heltal som anropas i en Stream Analytics fråga.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>JavaScript-objekt som stöds

Användardefinierade JavaScript-funktioner i Azure Stream Analytics stödjer inbyggda standardobjekt i JavaScript. En lista över de här objekten finns i [Globala objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics och JavaScript typkonvertering

Det finns skillnader i vilka typer som Stream Analytics frågespråk och JavaScript stödjer. Den här tabellen visar konverteringsmappningar mellan dessa:

Stream Analytics | JavaScript
--- | ---
bigint | Siffra (JavaScript kan bara representera heltal upp till exakt 2^53)
DateTime | Datum (JavaScript stöder endast millisekunder)
double | Antal
nvarchar(MAX) | Sträng
Post | Objekt
Matris | Matris
NULL | Null

Här är konverteringarna från JavaScript till Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Antal | Bigint (om talet är avrundat och mellan long.MinValue och long.MaxValue, i annat fall stöds det inte)
Datum | DateTime
Sträng | nvarchar(MAX)
Objekt | Post
Matris | Matris
Null, odefinierad | NULL
Annan typ (till exempel en funktion eller fel) | Stöds inte (resulterar i körningsfel)

JavaScript-språket är Skift läges känsligt och Skift läge för objekt fälten i JavaScript-koden måste matcha versaler och gemener i fälten i inkommande data. Jobb med kompatibilitetsnivå 1,0 kommer att konvertera fält från SQL SELECT-instruktionen till gemener. Under kompatibilitetsnivå 1,1 och högre har fält från SELECT-instruktionen samma Skift läge som anges i SQL-frågan.

## <a name="other-javascript-user-defined-function-patterns"></a>Andra mönster för användardefinierade JavaScript-funktioner

### <a name="write-nested-json-to-output"></a>Skriva kapslad JSON till utdata

Om du har ett bearbetningssteg för uppföljning som använder Stream Analytics-jobbets utdata som indata, och det kräver ett JSON-format, kan du skriva en JSON-sträng till utdata. I nästa exempel anropar den funktionen **JSON.stringify()** för att packa alla namn-/värdepar för indata och sedan skriva dem som ett enda strängvärde i utdata.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Exempel fråga:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Omvandla sträng till JSON-objekt som ska bearbetas

Om du har ett sträng fält som är JSON och vill konvertera det till ett JSON-objekt för bearbetning i ett JavaScript-skript UDF, kan du använda funktionen **JSON. parse ()** för att skapa ett JSON-objekt som sedan kan användas.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Exempel fråga:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Använd try/catch för fel hantering

Try/catch-block kan hjälpa dig att identifiera problem med felformaterade indata som skickas till en JavaScript UDF.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Exempel fråga: skicka hela posten som första parameter så att den kan returneras om det uppstår ett fel.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
Metoden **toLocaleString** i Java Script kan användas för att returnera en språk känslig sträng som representerar datum/tid-data från där den här metoden anropas.
Även om Azure Stream-analys endast tar emot UTC-datum tid som systemstämpel, kan den här metoden användas för att konvertera systemets tidstämpel till en annan språk version och tidszon.
Den här metoden följer samma implementerings beteende som den som finns tillgänglig i Internet Explorer.

**Definiera en användardefinierad JavaScript-funktion:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Exempel fråga: Skicka ett datetime-värde som indatavärde**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

Utdata från den här frågan blir indata-datetime i **de-de** med de angivna alternativen.
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>Användar loggning
Med loggnings metoden kan du samla in anpassad information medan ett jobb körs. Du kan använda loggdata för att felsöka eller utvärdera rätthet för den anpassade koden i real tid. Den här mekanismen är tillgänglig via Console. log ()-metoden.

```javascript
console.log('my error message');
```

Du kan komma åt logg meddelanden via [diagnostikloggar](data-errors.md).
## <a name="next-steps"></a>Nästa steg

* [Machine Learning UDF](./machine-learning-udf.md)
* [UDF för C#](./stream-analytics-edge-csharp-udf-methods.md)
