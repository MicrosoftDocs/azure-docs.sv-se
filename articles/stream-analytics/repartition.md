---
title: Använd ompartitionering för att optimera Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du använder ompartitionering för att optimera Azure Stream Analytics jobb som inte kan vara parallella.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182544"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Använd ompartitionering för att optimera bearbetningen med Azure Stream Analytics

Den här artikeln visar hur du använder ompartitionering för att skala din Azure Stream Analytics fråga efter scenarier som inte kan vara helt [parallella](stream-analytics-scale-jobs.md).

Du kanske inte kan använda [parallellisering](stream-analytics-parallelization.md) om:

* Du styr inte partitionsnyckel för indataströmmen.
* Källan "sprayar" i flera partitioner som senare behöver slås samman.

Ompartitionering eller reshuffling krävs när du bearbetar data på en data ström som inte är shardade enligt ett naturligt indata schema, till exempel **PartitionID** för Event Hubs. När du partitionerar om kan varje Shard bearbetas separat, vilket gör att du kan skala ut den strömmande pipelinen linjärt. 

## <a name="how-to-repartition"></a>Partitionera om
Du kan partitionera om dina inaktuella ingångar på två sätt:
1. Använd ett separat Stream Analytics jobb som gör ompartitionering
2. Använd ett enskilt jobb men gör om ompartitionering först innan din anpassade analys logik

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Skapa ett separat Stream Analytics jobb för att partitionera om inmatade
Du kan skapa ett jobb som läser in indata och skriver till en Event Hub-utdata med en partitionsnyckel. Den här Händelsehubben kan sedan fungera som inmatare för ett annat Stream Analytics jobb där du implementerar din analys logik. När du konfigurerar den här Event Hub-utdata i jobbet måste du ange den partitionsnyckel som Stream Analytics kommer att partitionera om dina data. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>Partitionera om indatamängden inom ett enda Stream Analytics jobb
Du kan också lägga till ett steg i din fråga som först partitionerar om inaktuella inaktuella inmatade och kan sedan användas av andra steg i frågan. Om du till exempel vill partitionera om indatamängden baserat på **DeviceID**, skulle din fråga vara:
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

Följande exempel fråga ansluter två strömmar av ompartitionerade data. När du ansluter två strömmar av ompartitionerade data måste strömmarna ha samma partitionsnyckel och antal. Resultatet är en ström som har samma partitionsschema.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Utmatnings schema ska matcha nyckeln Stream schema och Count så att varje under data ström kan tömmas oberoende av varandra. Data strömmen kan också sammanfogas och partitioneras om igen av ett annat schema innan du tömmer, men du bör undvika den metoden eftersom den lägger till i den allmänna svars tiden för bearbetningen och ökar resursutnyttjande.

## <a name="streaming-units-for-repartitions"></a>Enheter för strömning för ompartitionering

Experimentera och studera resursanvändningen för jobbet för att fastställa det exakta antalet partitioner du behöver. Antalet enheter för [strömning (SU)](stream-analytics-streaming-unit-consumption.md) måste justeras enligt de fysiska resurser som krävs för varje partition. I allmänhet behövs sex SUs för varje partition. Om det inte finns tillräckligt med resurser kopplade till jobbet, kommer systemet bara att använda ompartitionen om den har nytta av jobbet.

## <a name="repartitions-for-sql-output"></a>Ompartitionering för SQL-utdata

När jobbet använder SQL Database för utdata använder du explicit ompartitionering för att matcha det optimala antalet partitioner för att maximera data flödet. Eftersom SQL fungerar bäst med åtta skrivare, behöver du partitionera om flödet till åtta innan du tömmer eller någonstans ytterligare överströms, vilket kan dra nytta av jobbets prestanda. 

Om det finns fler än 8 inpartitioner är det inte säkert att du väljer att ärva schemat för inpartitionering. Överväg att [använda i frågan för att](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) uttryckligen ange antalet utgående skrivare. 

Följande exempel läser från indata, oavsett om de är naturligt partitionerade och partitionerar om Stream-tiofaldig enligt DeviceID-dimensionen och tömmer data till utdata. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Stream Analytics](stream-analytics-introduction.md)
* [Använd Query parallellisering i Azure Stream Analytics](stream-analytics-parallelization.md)
