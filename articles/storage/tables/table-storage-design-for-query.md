---
title: Designa Azure Table Storage för frågor | Microsoft Docs
description: Design tabeller för frågor i Azure Table Storage. Välj lämplig partitionsnyckel, optimera frågor och sortera data för Table service.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93316154"
---
# <a name="design-for-querying"></a>Utforma för frågor
Table service lösningar kan vara av intensiv, skriv intensiv eller en blandning av de två. Den här artikeln fokuserar på saker som du bör tänka på när du utformar Table service som stöder Läs åtgärder effektivt. Vanligt vis är en design som stöder Läs åtgärder effektiv för Skriv åtgärder. Det finns dock ytterligare saker att tänka på när du utformar för att stödja Skriv åtgärder, som beskrivs i artikeln [design för data ändringar](table-storage-design-for-modification.md).

En bra utgångs punkt för att utforma din Table service-lösning så att du kan läsa data effektivt är att fråga "vilka frågor som mitt program måste köra för att hämta de data som krävs från Table service?"  

> [!NOTE]
> Med Table service är det viktigt att du får fram rätt design eftersom det är svårt och dyrt att ändra det senare. I en Relations databas är det ofta möjligt att åtgärda prestanda problem genom att lägga till index i en befintlig databas: Detta är inte ett alternativ med Table service.  
> 
> 

Det här avsnittet fokuserar på de viktiga problem som du måste åtgärda när du utformar dina tabeller för frågor. De avsnitt som beskrivs i det här avsnittet är:

* [Hur du väljer PartitionKey och RowKey påverkar frågans prestanda](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Välja en lämplig PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimera frågor för Table service](#optimizing-queries-for-the-table-service)
* [Sortera data i Table service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur du väljer PartitionKey och RowKey påverkar frågans prestanda
I följande exempel förutsätts att tabell tjänsten lagrar anställdas entiteter med följande struktur (de flesta exempel utesluter egenskapen **timestamp** för klarhet):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelnings namn) |Sträng |
| **RowKey** (medarbetar-ID) |Sträng |
| **FirstName** |Sträng |
| **LastName** |Sträng |
| **Age** |Integer |
| **EmailAddress** |Sträng |

I artikeln [Översikt över Azure Table Storage](table-storage-overview.md) beskrivs några av de viktigaste funktionerna i Azure Table service som direkt påverkar utformningen av frågor. Detta resulterar i följande allmänna rikt linjer för att utforma Table service frågor. Observera att den filter-syntax som används i exemplen nedan är från Table service REST API. mer information finns i [fråga om entiteter](/rest/api/storageservices/Query-Entities).  

* En ***Point-fråga** _ är den mest effektiva sökningen som används och rekommenderas för att användas för hög volyms ökningar eller sökningar som kräver lägsta latens. En sådan fråga kan använda indexen för att hitta en enskild entitet effektivt genom att ange värdena _ *PartitionKey** och **RowKey** . Exempel: $filter = (PartitionKey EQ ' Sales ') och (RowKey EQ 2)  
* Andra bästa är en ***Range-fråga** _ som använder _ *PartitionKey** och filter på ett intervall med **RowKey** -värden för att returnera mer än en entitet. **PartitionKey** -värdet identifierar en viss partition och **RowKey** -värdena identifierar en delmängd av entiteterna i den partitionen. Exempel: $filter = PartitionKey EQ ' Sales ' och RowKey ge ' och RowKey lt '  
* Tredje bästa är en ***partitions ökning** _ som använder _ *PartitionKey** och filter på en annan icke-nyckel egenskap och som kan returnera fler än en entitet. **PartitionKey** -värdet identifierar en viss partition och egenskaps värden väljer för en delmängd av entiteterna i den partitionen. Exempel: $filter = PartitionKey EQ ' Sales ' och LastName ' Svensson '  
* En ***tabells ökning** _ inkluderar inte _ *PartitionKey** och är väldigt ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen i tur och följd för matchande entiteter. En tabells ökning utförs oavsett om filtret använder **RowKey** eller inte. Exempel: $filter = LastName EQ ' Johansson '  
* Frågor som returnerar flera entiteter returnerar dem sorterade i **PartitionKey** -och **RowKey** -ordningen. Välj en **RowKey** som definierar den vanligaste sorterings ordningen för att undvika att enheterna i klienten används.  

Observera att om du använder en "**eller**" för att ange ett filter baserat på **RowKey** -värden resulterar det i en partitions ökning och behandlas inte som en områdes fråga. Därför bör du undvika frågor som använder filter som: $filter = PartitionKey EQ ' Sales ' och (RowKey EQ ' 121 ' eller RowKey EQ ' 322 ')  

Exempel på kod på klient sidan som använder lagrings klient biblioteket för att köra effektiva frågor finns i:  

* [Köra en punkt fråga med lagrings klient biblioteket](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Hämta flera entiteter med LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projektion på Server Sidan](table-storage-design-patterns.md#server-side-projection)  

Exempel på kod på klient sidan som kan hantera flera enhets typer som lagras i samma tabell finns i:  

* [Arbeta med heterogena entitetstyper](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Välja en lämplig PartitionKey
Valet av **PartitionKey** bör balansera behovet av att möjliggöra användningen av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Med en extrem enhet kan du lagra alla entiteter i en enda partition, men detta kan begränsa din lösnings skalbarhet och förhindra att tabell tjänsten kan läsa in-balans-begäranden. På de andra Extreme kan du lagra en entitet per partition, som skulle vara mycket skalbar och som gör det möjligt för tabell tjänsten att belastningsutjämna begär Anden, men som hindrar dig från att använda enhets grupp transaktioner.  

En idealisk **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräckligt med partitioner för att säkerställa att din lösning är skalbar. Normalt kommer du att se att dina entiteter har en lämplig egenskap som distribuerar dina entiteter över tillräckligt många partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kan UserID till exempel vara en felfri PartitionKey. Du kan ha flera entiteter som använder ett angivet UserID som partitionsnyckel. Varje entitet som lagrar data om en användare grupperas i en enda partition, så dessa entiteter är tillgängliga via enhets grupp transaktioner, samtidigt som de fortfarande är mycket skalbara.
> 
> 

Det finns ytterligare saker du behöver tänka på när du väljer **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort entiteter. Mer information finns i [utforma tabeller för data ändring](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för Table service
Table service indexerar automatiskt dina entiteter med hjälp av värdena **PartitionKey** och **RowKey** i ett enda grupperat index, och därför är orsaken till att frågor är den mest effektiva att använda. Det finns dock inga andra index än det i det grupperade indexet på **PartitionKey** och **RowKey**.

Många design komponenter måste uppfylla kraven för att kunna söka efter entiteter baserat på flera kriterier. Du kan till exempel hitta anställdas entiteter baserat på e-post, medarbetar-ID eller efter namn. Mönstren som beskrivs i [tabell design mönster](table-storage-design-patterns.md) behandlar de här typerna av krav och beskriver olika sätt att arbeta runt det faktum att Table service inte tillhandahåller sekundära index:  

* [Sekundärt index mönster för sekundär partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* [Sekundärt index mönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* [Mönster för index entiteter](table-storage-design-patterns.md#index-entities-pattern) – underhålla index enheter för att möjliggöra effektiva sökningar som returnerar listor med entiteter.  

## <a name="sorting-data-in-the-table-service"></a>Sortera data i Table service
Table service returnerar entiteter sorterade i stigande ordning baserat på **PartitionKey** och sedan efter **RowKey**. Dessa nycklar är sträng värden och säkerställer att numeriska värden sorteras korrekt genom att konvertera dem till en fast längd och fylla dem med nollor. Om till exempel det medarbetar-ID-värde som du använder som **RowKey** är ett heltals värde, bör du konvertera anställnings-ID **123** till **00000123**.  

Många program har krav för att använda data sorterade i olika ordningar: till exempel att sortera anställda efter namn eller genom att ansluta till datum. Följande mönster hanterar hur du kan använda alternativa sorterings ordningar för dina entiteter:  

* [Sekundärt index mönster för sekundär partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika RowKey-värden.  
* [Sekundärt index mönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika RowKey-värden.
* [Logg änden-mönster](table-storage-design-patterns.md#log-tail-pattern) – hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar efter omvänt datum-och tids ordning.  

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)