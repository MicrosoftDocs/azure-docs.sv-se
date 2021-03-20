---
title: Fylla tid luckor och tillräknade värden som saknas – Azure SQL Edge
description: Lär dig mer om att fylla i tids luckor och tillräknade saknade värden i Azure SQL Edge
keywords: SQL Edge, timeseries
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185597"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Fylla tid luckor och tillräknade värden som saknas 

Vid hantering av tids serie data är det ofta möjligt att tids serie data saknar värden för attributen. Det är också möjligt att på grund av data typen, eller på grund av avbrott i data insamlingen, finns tids *luckor* i data uppsättningen.

Exempel: när du samlar in energi användnings statistik för en smart enhet, kommer det att finnas luckor i användnings statistiken när enheten inte fungerar. På samma sätt är det möjligt att de olika sensorer har kon figurer ATS för att generera data med olika frekvenser, vilket leder till att de saknade värdena för sensorerna i ett data insamlings scenario. Om det till exempel finns två sensorer, spänning och tryck, konfigurerade med 100 Hz och 10 Hz, genererar spännings sensorn data varje hundradel av en sekund, medan tryck sensorn endast genererar data var tionde av en sekund.

I följande tabell beskrivs en data uppsättning för dator telemetri som samlats in med ett sekunders intervall. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Det finns två viktiga egenskaper för föregående data uppsättning. 

- Data mängden innehåller inga data punkter relaterade till flera tidsstämplar `2020-09-07 06:14:47.000` ,,, `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` och `2020-09-07 06:14:55.000` . Dessa tidsstämplar är *luckor* i data uppsättningen.  
- Det finns saknade värden, representerade som `null` , för spännings-och tryck läsningar. 

## <a name="gap-filling"></a>Avstånds fyllning 

Mellanrum med mellanrum är en teknik som hjälper dig att skapa sammanhängande, ordnad uppsättning tidsstämplar för att under lätta analysen av tids serie data. I Azure SQL Edge är det enklaste sättet att fylla luckor i data uppsättningen för tids serier att definiera en temporär tabell med önskad tids fördelning och sedan utföra en `LEFT OUTER JOIN` eller en `RIGHT OUTER JOIN` åtgärd i data uppsättnings tabellen. 

Om du tar `MachineTelemetry` data som visas ovan som exempel kan du använda följande fråga för att skapa sammanhängande, ordnad uppsättning tidsstämplar för analys. 

> [!NOTE]
> Frågan nedan genererar de rader som saknas, med värdena för tidstämpel och `null` värden för attributen. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
Frågan ovan genererar följande utdata som innehåller alla tidsstämplar i *en sekund* i det angivna intervallet.

Här är resultat uppsättningen

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Mata in saknade värden

Föregående fråga skapade de saknade tidsstämplar för data analys, men ersätter inte något av de saknade värdena (representeras som null) för `voltage` och `pressure` läsningar. I Azure SQL Edge lades en ny syntax till i T-SQL `LAST_VALUE()` och `FIRST_VALUE()` functions, som innehåller mekanismer för att lägga till värden som saknas, baserat på föregående eller följande värden i data uppsättningen. 

Den nya syntaxen lägger till `IGNORE NULLS` och- `RESPECT NULLS` satsen i- `LAST_VALUE()` och- `FIRST_VALUE()` funktionerna. Följande fråga i `MachineTelemetry` data uppsättningen beräknar de saknade värdena med hjälp av funktionen last_value, där värden som saknas ersätts med det senast observerade värdet i data uppsättningen.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Här är resultat uppsättningen

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

Följande fråga tillräknar de saknade värdena med hjälp av `LAST_VALUE()` funktionerna och `FIRST_VALUE` . För visar kolumnen utdata `ImputedVoltage` att värdena som saknas ersätts av det senast observerade värdet, medan `ImputedPressure` värdena som saknas ersätts av nästa observerade värde i data uppsättningen. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Här är resultat uppsättningen

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> Frågan ovan använder `FIRST_VALUE()` funktionen för att ersätta saknade värden med nästa observerade värde. Samma resultat kan uppnås med hjälp av `LAST_VALUE()` funktionen med en- `ORDER BY <ordering_column> DESC` sats.

## <a name="next-steps"></a>Nästa steg 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Mängd funktioner (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)