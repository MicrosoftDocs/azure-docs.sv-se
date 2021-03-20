---
title: Hantera historiska data med bevarande princip – Azure SQL Edge
description: Lär dig hur du hanterar historiska data med bevarande principer i Azure SQL Edge
keywords: SQL Edge, data kvarhållning
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392375"
---
# <a name="manage-historical-data-with-retention-policy"></a>Hantera historiska data med bevarande princip

Datakvarhållning kan aktive ras på databasen och någon av de underliggande tabellerna individuellt, så att användarna kan skapa flexibla ålders principer för sina tabeller och databaser. Det är enkelt att använda datakvarhållning: det kräver bara att en parameter anges vid skapande av tabell eller som en del av en Alter Table-åtgärd. 

När data bevarande principen är användardefinierade för en databas och den underliggande tabellen, körs en tidsinställd bakgrunds tids period för att ta bort eventuella inaktuella poster från tabellen som är aktive rad för datakvarhållning. Identifiering av matchande rader och borttagning av dem från tabellen sker transparent i bakgrunds aktiviteten som schemaläggs och körs av systemet. Ålders villkor för tabell rader kontrol leras baserat på kolumnen som används som `filter_column` i tabell definitionen. Om kvarhållningsperioden exempelvis är inställt på en vecka, uppfyller tabell rader som är berättigade till rensning något av följande villkor: 

- Om filter kolumnen använder data typen DATETIMEOFFSET, är villkoret `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- Annars är villkoret `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Rensnings faser för data kvarhållning

Rensnings åtgärden för datakvarhållning består av två faser. 
- Identifierings fas – i den här fasen identifierar rensnings åtgärden alla tabeller i användar databaserna för att bygga en lista för rensning. Identifieringen körs en gång om dagen.
- Rensnings fas – i den här fasen körs rensningen mot alla tabeller med begränsad datakvarhållning som identifieras i identifierings fasen. Om rensningen inte kan utföras på en tabell, hoppas tabellen över i den aktuella körningen och kommer att göras om i nästa iteration. Följande principer används under rensning
    - Om en föråldrad rad är låst av en annan transaktion hoppas raden över. 
    - Rensnings körningar med standard inställningen på 5 sekunders lås tids gräns. Om låsen inte går att hämta i tabellerna inom tids gränsen, hoppas tabellen över i den aktuella körningen och kommer att göras om i nästa iteration.
    - Om det uppstår ett fel när en tabell rensas, hoppas tabellen över och hämtas i nästa iteration.

## <a name="manual-cleanup"></a>Manuell rensning

Beroende på inställningarna för datakvarhållning i en tabell och typen av arbets belastning på databasen, är det möjligt att den automatiska rensnings tråden inte tar bort alla inaktuella rader under körningen. Den `sys.sp_cleanup_data_retention` lagrade proceduren har introducerats i Azure SQL Edge för att hjälpa till med detta och tillåta användare att manuellt ta bort inaktuella rader. 

Den här lagrade proceduren tar tre parametrar. 
    - Schema namn – namnet på det ägande schemat för tabellen. Detta är en obligatorisk parameter. 
    - Tabell namn – namnet på den tabell för vilken manuell rensning körs. Detta är en obligatorisk parameter. 
    - rowCount (output) – utgående variabel. Returnerar antalet rader som rensats av den manuella rensningen SP. Detta är en valfri parameter. 

I följande exempel visas körningen av den manuella rensningen av SP för tabell `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Hur inaktuella rader tas bort

Rensnings processen beror på tabellens index layout. En bakgrunds aktivitet skapas för att utföra föråldrad data rensning för alla tabeller med begränsad kvarhållningsperiod. Rensa logiken för rowstore-indexet (B-Tree eller heap) tar bort föråldrad rad i mindre segment (upp till 10 000) minimerar trycket på databas loggen och i/o-undersystemet. Även om rensnings logik använder det obligatoriska B-träd indexet, kan ordningen på borttagningarna för de rader som är äldre än kvarhållningsperioden inte garanteras ordentligt. Ta därför inget beroende på rensnings ordningen i dina program.

Rensnings aktiviteten för grupperade columnstore tar bort hela rad grupper samtidigt (innehåller vanligt vis 1 000 000 rader), vilket är mycket effektivt, särskilt när data genereras och åldras i hög takt.

![Rensning av data kvarhållning](./media/data-retention-cleanup/data-retention-cleanup.png)

Med utmärkt data komprimering och effektiv kvarhållning av rensning görs ett grupperat columnstore-index ett perfekt val för scenarier när arbets belastningen snabbt genererar stora mängder data.

> [!Note]
> Om det gäller B-träd index och heapar kör datakvarhållning en borttagnings fråga i de underliggande tabellerna, vilket kan vara i konflikt med borttagnings utlösare för tabellerna. Vi rekommenderar att du antingen tar bort borttagnings utlösare från tabellerna eller inte aktiverar datakvarhållning för tabeller som har Delete DML-utlösare.

## <a name="monitoring-data-retention-cleanup"></a>Övervaka rensning av datakvarhållning

Rensnings åtgärder för data lagrings principer kan övervakas med utökade händelser (XEvents) i Azure SQL Edge. Mer information om utökade händelser finns i [Översikt över XEvents](/sql/relational-databases/extended-events/extended-events). 

Följande sex utökade händelser hjälper till att spåra status för rensnings åtgärderna. 

| Name | Beskrivning |
|------| ------------|
| data_retention_task_started  | Inträffar när bakgrunds aktiviteten för rensning av tabeller med bevarande princip startar. |
| data_retention_task_completed  | Inträffar när bakgrunds aktiviteten för rensning av tabeller med bevarande princip upphör. |
| data_retention_task_exception  | Inträffar när bakgrunds aktiviteten för rensning av tabeller med bevarande princip inte går utanför den rensnings process som är speciell för tabellen. |
| data_retention_cleanup_started  | Inträffar när rensnings processen för tabell med data lagrings princip startar. |
| data_retention_cleanup_exception  | Det går inte att rensa processen för tabell med bevarande princip. |
| data_retention_cleanup_completed  | Inträffar när rensnings processen för tabellen med data lagrings principen upphör. |  

Dessutom har en ny typ `RING_BUFFER_DATA_RETENTION_CLEANUP` av ringbufferten som heter lagts till i sys.dm_os_ring_buffers dynamisk hanterings vy. Den här vyn kan användas för att övervaka rensnings åtgärder för data kvarhållning. 


## <a name="next-steps"></a>Nästa steg
- [Princip för data bevarande](data-retention-overview.md)
- [Aktivera och inaktivera data lagrings principer](data-retention-enable-disable.md)