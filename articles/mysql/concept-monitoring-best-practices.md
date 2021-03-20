---
title: Metod tips för övervakning – Azure Database for MySQL
description: I den här artikeln beskrivs de bästa metoderna för att övervaka Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96355054"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Metod tips för övervakning Azure Database for MySQL-enskild server

Lär dig mer om de bästa metoderna som kan användas för att övervaka databas åtgärderna och se till att prestandan inte komprometteras när data storleken växer. När vi lägger till nya funktioner till plattformen kommer vi att fortsätta att förfina de bästa metoderna som beskrivs i det här avsnittet.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Layout för det aktuella övervaknings verktyget

Azure Database for MySQL innehåller verktyg och metoder som du kan använda för att övervaka användningen enkelt, lägga till eller ta bort resurser (till exempel processor, minne eller I/O), felsöka potentiella problem och hjälpa till att förbättra prestandan för en databas. Du kan [övervaka prestanda mått](concepts-monitoring.md#metrics) regelbundet för att se medelvärde, högsta och lägsta värden för en mängd olika tidsintervall.

Du kan [ställa in aviseringar](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) för ett mått tröskelvärde så att du får information om servern har nått dessa gränser och vidtar lämpliga åtgärder.  

Övervaka databas servern för att se till att resurserna som är kopplade till databasen kan hantera programmets arbets belastning. Om databasen når resurs gränser bör du tänka på följande:
    * Identifiera och optimera de mest resurs krävande frågorna. 
    * Lägga till fler resurser genom att uppgradera tjänst nivån.

### <a name="cpu-utilization"></a>CPU-användning
Övervaka CPU-användning och om databasen förbrukar processor resurser. Om CPU-användningen är 90% eller mer än kan du skala upp beräkningen genom att öka antalet virtuella kärnor eller skala till nästa pris nivå.  Se till att data flödet eller samtidigheten är som förväntat när du skalar upp/ned processorn. 

### <a name="memory"></a>Minne 
Mängden minne som är tillgängligt för databas servern är proportionell till [antalet virtuella kärnor](concepts-pricing-tiers.md). Kontrol lera att minnet räcker för arbets belastningen. Läs in testa ditt program för att kontrol lera att minnet räcker för Läs-och skriv åtgärder. Om databasens minnes förbrukning ofta blir större än en definierad tröskel, indikerar detta att du bör uppgradera instansen genom att öka virtuella kärnor eller högre prestanda nivå. Använd [query Store](concepts-query-store.md)och [fråga prestanda rekommendationer](concepts-performance-recommendations.md) för att identifiera frågor med den längsta varaktigheten som körs. Utforska möjligheter att optimera. 

### <a name="storage"></a>Storage 
[Mängden lagrings utrymme](howto-create-manage-server-portal.md#scale-compute-and-storage) som har allokerats för MySQL-servern fastställer IOPS för servern. Lagrings utrymmet som används av tjänsten innehåller databasfiler, transaktions loggar, Server loggar och ögonblicks bilder av säkerhets kopior. Se till att det förbrukade disk utrymmet inte ständigt överskrider 85 procent av det totala allokerade disk utrymmet. I så fall måste du ta bort eller arkivera data från databas servern för att frigöra utrymme. 

### <a name="network-traffic"></a>Nätverkstrafik 

**Data flöde för nätverks mottagning, data flöde för nätverks överföring** – hastigheten för nätverks trafik till och från MySQL-instansen i megabyte per sekund. Du måste utvärdera data flödes kravet för Server och kontinuerligt övervaka trafiken om data flödet är lägre än förväntat. 

### <a name="database-connections"></a>Databas anslutningar 
**Databas anslutningar** – antalet klientsessioner som är anslutna till Azure Database for MySQL ska justeras med [anslutnings gränserna för den valda SKU](concepts-server-parameters.md#max_connections) -storleken. 


## <a name="next-steps"></a>Nästa steg

- [Bästa praxis för prestanda för Azure Database for MySQL](concept-performance-best-practices.md)
- [Bästa praxis för Server åtgärder med hjälp av Azure Database for MySQL](concept-operation-excellence-best-practices.md)
