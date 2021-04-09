---
title: Användardefinierade JavaScript-mängder i Azure Stream Analytics
description: Den här artikeln beskriver hur du utför avancerade Mechanics med JavaScript-användardefinierade agg regeringar i Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: e9695a268d1c6ec43a737afd36536e4ec2a41a41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012486"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics användardefinierade JavaScript-mängder
 
Azure Stream Analytics stöder användardefinierade agg regeringar (UDA) som skrivits i Java Script, så att du kan implementera komplex tillstånds känslig affärs logik. I UDA har du fullständig kontroll över tillstånds data strukturen, delstats ackumulering, delsummering av tillstånd och sammanställd resultat beräkning. Artikeln introducerar två olika JavaScript-UDA gränssnitt, steg för att skapa en UDA och hur du använder UDA med window-baserade åtgärder i Stream Analytics fråga.

## <a name="javascript-user-defined-aggregates"></a>Användardefinierade JavaScript-mängder

En användardefinierad mängd används ovanpå en tids periods specifikation för att aggregera händelser i fönstret och skapa ett enda resultat värde. Det finns två typer av UDA-gränssnitt som Stream Analytics stöder idag, AccumulateOnly och AccumulateDeaccumulate. Båda typerna av UDA kan användas av rullande-, hoppande-, glid-och session-fönster. AccumulateDeaccumulate UDA fungerar bättre än AccumulateOnly UDA när de används tillsammans med hoppande-, glid-och session-fönstret. Du väljer en av de två typerna utifrån den algoritm som du använder.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly-mängder

AccumulateOnly-mängder kan bara ackumulera nya händelser till sitt tillstånd, algoritmen tillåter inte deackumulering av värden. Välj den här sammanställda typen när du deackumulerar en händelse information från State-värdet är omöjligt att implementera. Följande är JavaScript-mallen för AccumulatOnly-mängder:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate-mängder

Med AccumulateDeaccumulate-agg regeringar kan du ta bort ett nyckel/värde-par från en lista över händelse värden eller subtrahera ett värde från ett tillstånd med sum-mängd i ett tidigare ackumulerat värde. Följande är JavaScript-mallen för AccumulateDeaccumulate-mängder:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA – JavaScript-funktions deklaration

Varje JavaScript-UDA definieras av en funktions objekts deklaration. Följande är de viktigaste elementen i en UDA-definition.

### <a name="function-alias"></a>Funktions Ali Aset

Funktions Ali Aset är UDA-identifieraren. När det anropas i Stream Analytics fråga ska du alltid använda UDA-alias tillsammans med en "Uda". .

### <a name="function-type"></a>Funktions typ

För UDA ska funktions typen vara JavaScript- **Uda**.

### <a name="output-type"></a>Utdatatyp

En speciell typ som Stream Analytics jobb som stöds, eller "any" om du vill hantera typen i din fråga.

### <a name="function-name"></a>Funktionsnamn

Namnet på det här funktions objektet. Funktions namnet måste matcha UDA-aliaset.

### <a name="method---init"></a>Metod-init ()

Metoden init () initierar status för mängden. Den här metoden anropas när Window startas.

### <a name="method--accumulate"></a>Metod – ackumulera ()

Metoden ackumulerad () beräknar UDA-tillstånd baserat på föregående tillstånd och de aktuella händelse värdena. Den här metoden anropas när en händelse går in i ett tids fönster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW eller SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metod – deackumulera ()

Metoden deackumulera () beräknar om status baserat på föregående tillstånd och de aktuella händelse värdena. Den här metoden anropas när en händelse lämnar en SLIDINGWINDOW eller SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metod – deaccumulateState ()

Metoden deaccumulateState () beräknar om status baserat på föregående tillstånd och status för ett hopp. Den här metoden anropas när en uppsättning händelser lämnar en HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metod – computeResult ()

Metoden computeResult () returnerar mängd resultat baserat på det aktuella läget. Den här metoden anropas i slutet av ett tids fönster (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW eller SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript-UDA som stöder indata och utdata för utdata
För Java Script UDA-datatyper, se avsnittet **Stream Analytics och JavaScript-typ konvertering** av [integrera JavaScript-UDF: er](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Lägga till en JavaScript-UDA från Azure Portal

Nedan går vi igenom processen med att skapa en UDA från portalen. Exemplet som vi använder här beräknar tids viktnings medelvärden.

Nu ska vi skapa en JavaScript-UDA under ett befintligt ASA-jobb genom att följa stegen nedan.

1. Logga in på Azure Portal och leta upp ditt befintliga Stream Analytics-jobb.
1. Klicka sedan på Functions-länken under **jobb sto pol Ogin**.
1. Klicka på ikonen **Lägg** till för att lägga till en ny funktion.
1. I den nya Function-vyn väljer du **Java Script Uda** som funktions typ. därefter visas en standard-Uda-mall i redigeraren.
1. Fyll i "TWA" som UDA-alias och ändra funktions implementeringen enligt följande:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. När du klickar på knappen "Spara" visas UDA i funktions listan.

1. Klicka på den nya funktionen "TWA", så kan du kontrol lera funktions definitionen.

## <a name="calling-javascript-uda-in-asa-query"></a>Anropar JavaScript-UDA i ASA-fråga

I Azure Portal och öppnar jobbet, redigera frågan och anropa TWA ()-funktionen med ett uppdrag-prefix "Uda". Exempel:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testa fråga med UDA

Skapa en lokal JSON-fil med innehållet nedan, ladda upp filen till Stream Analytics jobb och testa ovanstående fråga.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vår [sida om Microsoft Q&en fråga för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språk referens för Azure Stream Analytics-fråga](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics hanterings REST API](/rest/api/streamanalytics/)