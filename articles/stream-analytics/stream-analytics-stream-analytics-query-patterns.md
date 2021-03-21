---
title: Vanliga fråge mönster i Azure Stream Analytics
description: I den här artikeln beskrivs flera vanliga fråge mönster och design som är användbara i Azure Stream Analytics-jobb.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-js
ms.openlocfilehash: 8fcda77858b3feb78a04971a7ad1f20c0ea51fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020000"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Vanliga fråge mönster i Azure Stream Analytics

Frågor i Azure Stream Analytics uttrycks i ett SQL-liknande frågespråk. Språk konstruktionerna dokumenteras i referens hand boken för [Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference) . 

Frågans design kan uttrycka enkel direkt sändnings logik för att flytta händelse data från en indataströmmen till ett utgående data lager, eller så kan den utföra omfattande mönster matchning och temporal analys för att beräkna mängder över olika tidsfönster som i [skapa en IoT-lösning med hjälp av Stream Analytics](stream-analytics-build-an-iot-solution-using-stream-analytics.md) guide. Du kan koppla data från flera indata till att kombinera strömmande händelser och du kan göra sökningar mot statiska referens data för att utöka händelse värden. Du kan också skriva data till flera utdata.

Den här artikeln beskriver lösningar på flera vanliga fråge mönster baserade på verkliga scenarier.

## <a name="supported-data-formats"></a>Data format som stöds

Azure Stream Analytics stöder bearbetning av händelser i CSV-, JSON-och Avro data format.

Både JSON och Avro kan innehålla komplexa typer som kapslade objekt (poster) eller matriser. Mer information om hur du arbetar med dessa komplexa data typer finns i artikeln [parsa JSON och Avro data](stream-analytics-parsing-json.md) .

## <a name="send-data-to-multiple-outputs"></a>Skicka data till flera utdata

Flera **Select** -uttryck kan användas för att mata ut data till olika utgående mottagare. En **Select** kan till exempel generera en tröskel-baserad avisering medan en annan kan spara händelser till Blob Storage.

**Inmatade**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Utgående ArchiveOutput**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Utgående AlertOutput**:

| Modell | Tid | Antal |
| --- | --- | --- |
| Make2 |2015-01-01T00:00:10.0000000 Z |3 |

**Fråga**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**Into** -satsen visar Stream Analytics vilka utdata som data ska skrivas till. Den första **Select** definierar en direkt fråga som tar emot data från indata och skickar den till utdata med namnet **ArchiveOutput**. Den andra frågan gör viss enkel agg regering och filtrering innan resultatet skickas till en underordnad varnings system utdata som kallas **AlertOutput**.

Observera att **with** -satsen kan användas för att definiera flera under frågor. Det här alternativet har fördelen att öppna färre läsare till Indatakällan.

**Fråga**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

Mer information finns i [ **with** -satsen](/stream-analytics-query/with-azure-stream-analytics).

## <a name="simple-pass-through-query"></a>Enkel direkt fråga

Du kan använda en enkel direkt sändnings fråga för att kopiera data Ströms data till utdata. Om t. ex. en data ström som innehåller information om vehikel måste sparas i en SQL-databas för bokstavs analys, kommer en enkel direkt fråga att utföra jobbet.

**Inmatade**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Utdata**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Fråga**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

En **Select** *-fråga projekterar alla fält i en inkommande händelse och skickar dem till utdata. På samma sätt kan du också **välja** att endast använda projekt som krävs för att ange fält från indatatyperna. I det här exemplet, om fordons *märke* och- *tid* är de enda obligatoriska fält som ska sparas, kan dessa fält anges i **Select** -instruktionen.

**Inmatade**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Utdata**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:04.0000000 Z |

**Fråga**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>Sträng matchning med gilla och inte gilla

**Gilla** och **inte gilla** kan användas för att kontrol lera om ett fält matchar ett visst mönster. Ett filter kan till exempel skapas för att endast returnera de licens plattor som börjar med bokstaven A och slutar med siffran 9.

**Inmatade**:

| Modell | License_plate | Tid |
| --- | --- | --- |
| Make1 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC – 369 |2015-01-01T00:00:03.0000000 Z |

**Utdata**:

| Modell | License_plate | Tid |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make3 |ABC – 369 |2015-01-01T00:00:03.0000000 Z |

**Fråga**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

Använd **like** -instruktionen för att kontrol lera värdet för **License_plate** fältet. Den måste börja med bokstaven "A" och har en sträng på noll eller flera tecken, som slutar med siffran 9.

## <a name="calculation-over-past-events"></a>Beräkning över tidigare händelser

Du kan använda funktionen **fördröjning** för att titta på tidigare händelser i ett tids fönster och jämföra dem med den aktuella händelsen. Till exempel kan den aktuella bil-varan vara utlämnad om den skiljer sig från den sista bilen som gick igenom avgifts meddelandet.

**Inmatade**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Utdata**:

| Modell | Tid |
| --- | --- |
| Make2 |2015-01-01T00:00:02.0000000 Z |

**Fråga**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

Använd **fördröjning** för att Visa indataströmmen en händelse tillbaka, hämtar värdet och jämför det *med värdet för* att *göra* -värdet för den aktuella händelsen och mata ut händelsen.

Mer information finns i [**fördröjning**](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="return-the-last-event-in-a-window"></a>Returnera den sista händelsen i ett fönster

När händelser används av systemet i real tid, finns det ingen funktion som kan avgöra om en händelse är den sista som ska tas emot för tids perioden. För att uppnå detta måste indataströmmen vara ansluten till en annan där tiden för en händelse är den maximala tiden för alla händelser i fönstret.

**Inmatade**:

| License_plate | Modell | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Utdata**:

| License_plate | Modell | Tid |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Fråga**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

Det första steget i frågan hittar den maximala tidsstämpeln i 10-minuters fönster, det vill säga tidsstämpeln för den senaste händelsen för fönstret. I det andra steget kopplas resultatet från den första frågan med den ursprungliga data strömmen för att hitta den händelse som matchar de senaste tidsstämplar i varje fönster. 

**DateDiff** är en programbestämd funktion som jämför och returnerar tids skillnaden mellan två DATETIME-fält, för mer information, se [datum funktioner](/stream-analytics-query/date-and-time-functions-azure-stream-analytics).

Mer information om att ansluta till strömmar finns i [**delta**](/stream-analytics-query/join-azure-stream-analytics).

## <a name="data-aggregation-over-time"></a>Data agg regering över tid

Om du vill beräkna information över ett tidsfönster kan data aggregeras tillsammans. I det här exemplet beräknas ett antal under de senaste 10 sekundernas tid för varje speciell bil.

**Inmatade**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |1000 |
| Make1 |2015-01-01T00:00:02.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |1500 |

**Utdata**:

| Modell | Antal |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**Fråga**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Den här agg regeringen grupperar bilaren genom *att göra* och räknar dem var tionde sekund. Resultatet har *fabrikatet* och *antalet* bilar som gick via avgift.

TumblingWindow är en fönster funktion som används för att gruppera händelser tillsammans. En agg regering kan tillämpas på alla grupperade händelser. Mer information finns i [fönster funktioner](stream-analytics-window-functions.md).

Mer information om agg regering finns i [mängd funktioner](/stream-analytics-query/aggregate-functions-azure-stream-analytics).

## <a name="periodically-output-values"></a>Jämna ut värden

I händelse av oregelbundna eller saknade händelser kan ett vanligt intervall med utdata genereras från en mer sparse-indata. Du kan till exempel generera en händelse var femte sekund som rapporterar den senast visade data punkten.

**Inmatade**:

| Tid | Värde |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Utdata (första 10 raderna)**:

| Window_end | Last_event. Tid | Last_event. Värde |
| --- | --- | --- |
| 2014-01-01T14:01:00.000 Z |2014-01-01T14:01:00.000 Z |1 |
| 2014-01-01T14:01:05.000 Z |2014-01-01T14:01:05.000 Z |2 |
| 2014-01-01T14:01:10 000 Z |2014-01-01T14:01:10 000 Z |3 |
| 2014-01-01T14:01:15.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:20.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:25.000 Z |2014-01-01T14:01:15.000 Z |4 |
| 2014-01-01T14:01:30.000 Z |2014-01-01T14:01:30.000 Z |5 |
| 2014-01-01T14:01:35.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:40.000 Z |2014-01-01T14:01:35.000 Z |6 |
| 2014-01-01T14:01:45.000 Z |2014-01-01T14:01:35.000 Z |6 |

**Fråga**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

Den här frågan genererar händelser var femte sekund och matar ut den senaste händelsen som togs emot tidigare. **HOPPINGWINDOW** varaktighet avgör hur långt tillbaka frågan ser ut för att hitta den senaste händelsen.

Mer information finns i hoppande- [fönstret](/stream-analytics-query/hopping-window-azure-stream-analytics).

## <a name="correlate-events-in-a-stream"></a>Korrelera händelser i en ström

Att korrelera händelser i samma data ström kan göras genom att titta på tidigare händelser med funktionen **lag** . Till exempel kan en utmatning skapas varje gång två på varandra följande bilar *från samma sätt* gå igenom avgift under de senaste 90 sekunderna.

**Inmatade**:

| Modell | License_plate | Tid |
| --- | --- | --- |
| Make1 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |
| Make1 |AAA-999 |2015-01-01T00:00:02.0000000 Z |
| Make2 |DEF-987 |2015-01-01T00:00:03.0000000 Z |
| Make1 |GHI – 345 |2015-01-01T00:00:04.0000000 Z |

**Utdata**:

| Modell | Tid | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:00:02.0000000 Z |AAA-999 |ABC – 123 |2015-01-01T00:00:01.0000000 Z |

**Fråga**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**Fördröjnings** funktionen kan titta på data strömmen en händelse tillbaka och *Hämta värdet för att jämföra* med att *göra* -värdet för den aktuella händelsen.  När villkoret är uppfyllt kan data från föregående händelse projiceras med en **fördröjning** i **Select** -uttrycket.

Mer information finns i [fördröjning](/stream-analytics-query/lag-azure-stream-analytics).

## <a name="detect-the-duration-between-events"></a>Identifiera varaktigheten mellan händelser

Varaktigheten för en händelse kan beräknas genom att titta på den sista start händelsen när en slut händelse tas emot. Den här frågan kan vara användbar för att avgöra hur lång tid en användare lägger på en sida eller en funktion.

**Inmatade**:  

| User | Funktion | Händelse | Tid |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Start |2015-01-01T00:00:01.0000000 Z |
| user@location.com |RightMenu |Slut |2015-01-01T00:00:08.0000000 Z |

**Utdata**:  

| User | Funktion | Varaktighet |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Fråga**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

Den **sista** funktionen kan användas för att hämta den sista händelsen inom ett angivet villkor. I det här exemplet är villkoret en händelse av typen Start som partitionerar sökningen efter **partition efter** användare och funktion. På så sätt behandlas varje användare och funktion separat när du söker efter händelsen starta. **Gräns för tids gräns** begränsar Sök tiden till 1 timme mellan slut-och start händelserna.

## <a name="count-unique-values"></a>Räkna unika värden

**Count** och **DISTINCT** kan användas för att räkna antalet unika fält värden som visas i data strömmen i ett tids fönster. En fråga kan skapas för att beräkna hur *många unika bilar av bilar som skickas* genom väg-och ett 2-sekunders fönster.

**Inmatade**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make1 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Utdataparametrar**

| Count_make | Tid |
| --- | --- |
| 2 |2015-01-01T00:00:02.000 Z |
| 1 |2015-01-01T00:00:04.000 Z |

**Fråga:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**Count (DISTINCT-fabrikat)** returnerar antalet distinkta värden i kolumnen **skapa** i ett tids fönster.
Mer information finns i [mängd funktionen **Count**](/stream-analytics-query/count-azure-stream-analytics).

## <a name="retrieve-the-first-event-in-a-window"></a>Hämta den första händelsen i ett fönster

**IsFirst** kan användas för att hämta den första händelsen i ett tids fönster. Du kan till exempel placera den första bil informationen i intervallet 10: e minut.

**Inmatade**:

| License_plate | Modell | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| RMV 8282 |Make1 |2015-07-27T00:05:01.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| VFE 1616 |Make2 |2015-07-27T00:09:31.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Utdata**:

| License_plate | Modell | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |

**Fråga**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst** kan också partitionera data och beräkna den första händelsen för varje *enskild bil som du hittar* vid varje 10-minuters intervall.

**Utdata**:

| License_plate | Modell | Tid |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:05.0000000 Z |
| YZK 5704 |Make3 |2015-07-27T00:02:17.0000000 Z |
| YHN 6970 |Make2 |2015-07-27T00:06:00.0000000 Z |
| QYF 9358 |Make1 |2015-07-27T00:12:02.0000000 Z |
| MDR 6128 |Make4 |2015-07-27T00:13:45.0000000 Z |

**Fråga**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

Mer information finns på [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics).

## <a name="remove-duplicate-events-in-a-window"></a>Ta bort duplicerade händelser i ett fönster

När du utför en åtgärd, till exempel beräkning av genomsnitt över händelser inom ett angivet tidsintervall, ska duplicerade händelser filtreras. I följande exempel är den andra händelsen en dubblett av den första.

**Inmatade**:  

| DeviceId | Tid | Attribut | Värde |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatur |50 |
| 1 |2018-07-27T00:00:01.0000000 Z |Temperatur |50 |
| 2 |2018-07-27T00:00:01.0000000 Z |Temperatur |40 |
| 1 |2018-07-27T00:00:05.0000000 Z |Temperatur |60 |
| 2 |2018-07-27T00:00:05.0000000 Z |Temperatur |50 |
| 1 |2018-07-27T00:00:10.0000000 Z |Temperatur |100 |

**Utdata**:  

| AverageValue | DeviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**Fråga**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**Count (distinkt tid)** returnerar antalet distinkta värden i kolumnen Time i ett tids fönster. Resultatet från det första steget kan sedan användas för att beräkna genomsnitt per enhet genom att ta bort dubbletter.

Mer information finns i [Count (DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics).

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>Ange logik för olika fall/värden (CASE-instruktioner)

**Case** -instruktioner kan ge olika beräkningar för olika fält, baserat på specifika kriterier. Du kan till exempel tilldela Lane "A" till bilar av *Make1* och Lane "B" till något annat.

**Inmatade**:

| Modell | Tid |
| --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |
| Make2 |2015-01-01T00:00:02.0000000 Z |
| Make2 |2015-01-01T00:00:03.0000000 Z |

**Utdata**:

| Modell |Dispatch_to_lane | Tid |
| --- | --- | --- |
| Make1 |En |2015-01-01T00:00:01.0000000 Z |
| Make2 |T |2015-01-01T00:00:02.0000000 Z |

**Lösning**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**Case** -uttrycket jämför ett uttryck med en uppsättning enkla uttryck för att fastställa resultatet. I det här exemplet skickas fordon av *Make1* till Lane "A" medan fordon av andra fabrikat tilldelas Lane ' B '.

Mer information finns i Case- [uttryck](/stream-analytics-query/case-azure-stream-analytics).

## <a name="data-conversion"></a>Data konvertering

Data kan omvandlas i real tid med hjälp av **Cast** -metoden. Bil vikt kan till exempel konverteras från Type **nvarchar (max)** för att skriva **bigint** och användas i en numerisk beräkning.

**Inmatade**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |"1000" |
| Make1 |2015-01-01T00:00:02.0000000 Z |"2000" |

**Utdata**:

| Modell | Vikt |
| --- | --- |
| Make1 |3000 |

**Fråga**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

Använd en **Cast** -instruktion för att ange dess datatyp. Se listan över data typer som stöds i [data typer (Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics).

Mer information om [data konverterings funktioner](/stream-analytics-query/conversion-functions-azure-stream-analytics).

## <a name="detect-the-duration-of-a-condition"></a>Identifiera varaktigheten för ett villkor

För villkor som sträcker sig över flera händelser kan du använda funktionen **fördröjning** för att identifiera villkorets varaktighet. Anta till exempel att en bugg ledde till att alla bilar har en felaktig vikt (över 20 000 kg) och varaktigheten för denna bugg måste beräknas.

**Inmatade**:

| Modell | Tid | Vikt |
| --- | --- | --- |
| Make1 |2015-01-01T00:00:01.0000000 Z |2000 |
| Make2 |2015-01-01T00:00:02.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:03.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:04.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:05.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:06.0000000 Z |25000 |
| Make1 |2015-01-01T00:00:07.0000000 Z |26000 |
| Make2 |2015-01-01T00:00:08.0000000 Z |2000 |

**Utdata**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:00:02.000 Z |2015-01-01T00:00:07.000 Z |

**Fråga**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
Den första **Select** -instruktionen korrelerar den aktuella vikt mätningen med föregående mått, och projicerar den tillsammans med den aktuella mätningen. Den andra **väljer** ser tillbaka till den senaste händelsen där *previous_weight* är mindre än 20000, där den aktuella vikten är mindre än 20000 och *previous_weight* för den aktuella händelsen var större än 20000.

End_fault är den aktuella händelsen utan fel där föregående händelse var trasig och Start_fault är den senaste händelse som inte är fel före.

## <a name="process-events-with-independent-time-substreams"></a>Bearbeta händelser med oberoende tid (under strömmar)

Händelser kan komma in i försenat eller ur ordning på grund av fördröjningar mellan evenemangs producenter, klockor mellan partitioner eller nätverks fördröjning.
Till exempel är enhets klockan för *TollID* 2 fem sekunder bakom *TollID* 1 och enhets klockan för *TollID* 3 är tio sekunder bakom *TollID* 1. En beräkning kan ske oberoende för varje avgift, och endast överväger sina egna klock data som en tidsstämpel.

**Inmatade**:

| LicensePlate | Modell | Tid | TollID |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000 Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:00:05.0000000 Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000 Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000 Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000 Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000 Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000 Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000 Z | 3 |

**Utdata**:

| TollID | Antal |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**Fråga**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**Tidsstämpeln över efter** -satsen tittar på varje enhets tids linje oberoende av under strömmar. Händelsen utdata för varje *TollID* genereras när de beräknas, vilket innebär att händelserna är i ordning för varje *TollID* i stället för att omordnas som om alla enheter var på samma klocka.

Mer information finns i [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering).

## <a name="session-windows"></a>Session Windows

Ett sessions fönster är ett fönster som håller på att expanderas när händelser inträffar och stängs för beräkning om ingen händelse tas emot efter en angiven tids period eller om perioden når maximal varaktighet.
Det här fönstret är särskilt användbart när du beräknar användar interaktions data. Ett fönster startar när en användare börjar interagera med systemet och stängs när inga fler händelser observeras, vilket innebär att användaren har slutat fungera.
Till exempel är en användare som interagerar med en webb sida där antalet klickningar loggas, ett session-fönster som kan användas för att ta reda på hur lång tid användaren interagerar med webbplatsen.

**Inmatade**:

| User_id | Tid | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000 Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000 Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000 Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000 Z | "www.example.com/e.html" |

**Utdata**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000 Z | 2017-01-26T00:01:10.0000000 Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000 Z | 2017-01-26T00:01:15.0000000 Z | 20 |

**Fråga**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

I **Välj** projekt visas de data som är relevanta för användar interaktionen tillsammans med varaktigheten för interaktionen. Gruppera data efter användare och en **SessionWindow** som stängs om ingen interaktion sker inom 1 minut, med en maximal fönster storlek på 60 minuter.

Mer information om **SessionWindow** finns i session- [fönstret](/stream-analytics-query/session-window-azure-stream-analytics) .

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>Språk utöknings Bart med användardefinierad funktion i Java Script och C #

Azure Stream Analytics frågespråk kan utökas med anpassade funktioner skrivna antingen i Java Script eller C#-språk. UDF (User Defined Functions) är anpassade/komplexa beräkningar som inte lätt kan uttryckas med **SQL** -språket. Dessa UDF: er kan definieras en gång och användas flera gånger i en fråga. En UDF kan till exempel användas för att konvertera ett hexadecimalt *nvarchar (max)* -värde till ett *bigint* -värde.

**Inmatade**:

| Device_id | HexValue |
| --- | --- |
| 1 | Cell |
| 2 | "11B" |
| 3 | "121" |

**Utdata**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

Den användardefinierade funktionen beräknar *bigint* -värdet från HexValue vid varje händelse som används.

Mer information finns i [Java Script](./stream-analytics-javascript-user-defined-functions.md) och [C#](./stream-analytics-edge-csharp-udf.md).

## <a name="advanced-pattern-matching-with-match_recognize"></a>Avancerad mönster matchning med MATCH_RECOGNIZE

**MATCH_RECOGNIZE** är en avancerad mönster matchnings funktion som kan användas för att matcha en sekvens med händelser till ett väldefinierat mönster för reguljära uttryck.
Till exempel övervakas en ATM i real tid för fel, under driften av ATM om det finns två varnings meddelanden i följd som administratören måste meddelas.

**Inmatade**:

| ATM_id | Operation_id | Return_Code | Tid |
| --- | --- | --- | --- |
| 1 | "Ange PIN-kod" | Resultatet | 2017-01-26T00:10:00.0000000 Z |
| 2 | "Ingående Money-kortplats" | Resultatet | 2017-01-26T00:10:07.0000000 Z |
| 2 | "Sluten Money-fack" | Resultatet | 2017-01-26T00:10:11.0000000 Z |
| 1 | "Ange åter kallelse antal" | Resultatet | 2017-01-26T00:10:08.0000000 Z |
| 1 | "Ingående Money-kortplats" | Honom | 2017-01-26T00:10:14.0000000 Z |
| 1 | "Skriva ut bank saldo" | Honom | 2017-01-26T00:10:19.0000000 Z |

**Utdata**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Ingående Money-kortplats" | 2017-01-26T00:10:14.0000000 Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

Den här frågan matchar minst två efterföljande fel händelser och genererar ett larm när villkoren är uppfyllda.
**Pattern** definierar det reguljära uttryck som ska användas för matchning, i det här fallet ett valfritt antal lyckade åtgärder följt av minst två efterföljande avbrott.
Lyckade och misslyckade åtgärder definieras med Return_Code värde och när villkoret är uppfyllt projiceras **måtten** med *ATM_id*, den första varningen och första varnings tiden.

Mer information finns i [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics).

## <a name="geofencing-and-geospatial-queries"></a>Polystaket och geospatiala frågor

Azure Stream Analytics innehåller inbyggda geospatiala funktioner som kan användas för att implementera scenarier som hantering av flottan, delning av delning, anslutna bilar och till gångs spårning.
Geospatiala data kan matas in i antingen interjson-eller well-format som en del av händelse data strömmen eller referens data.
Till exempel ett företag som är specialiserat på Manufacturing Machines för att skriva ut Passport, leasa sina datorer till myndigheter och Consulates. Platsen för dessa datorer är kraftigt kontrollerad för att undvika problem med att placera och kunna använda för förfalskning av Passport. Varje dator är utrustad med en GPS-Spårare, den informationen vidarebefordras tillbaka till ett Azure Stream Analytics jobb.
Tillverkningen vill hålla koll på platsen för dessa datorer och bli aviserad om en av dem lämnar ett godkänt område, på det här sättet att de fjärrinaktiverar, varnar myndigheterna och hämtar utrustningen.

**Inmatade**:

| Equipment_id | Equipment_current_location | Tid |
| --- | --- | --- |
| 1 | "PUNKT (-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000 Z |
| 1 | "PUNKT (-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000 Z |
| 1 | "PUNKT (-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000 Z |
| 1 | "PUNKT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

**Referens data inmatning**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON ((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**Utdata**:

| Equipment_id | Equipment_alert_location | Tid |
| --- | --- | --- |
| 1 | "PUNKT (-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000 Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

Frågan gör att tillverkaren kan övervaka datorernas plats automatiskt och få aviseringar när en dator lämnar det tillåtna avgränsnings gränsen. Den inbyggda geospatiala funktionen gör det möjligt för användare att använda GPS-data i frågan utan bibliotek från tredje part.

Mer information finns i avsnittet om [avgränsnings-och geospatiala agg regerings scenarier med Azure Stream Analytics](geospatial-scenarios.md) artikel.

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)