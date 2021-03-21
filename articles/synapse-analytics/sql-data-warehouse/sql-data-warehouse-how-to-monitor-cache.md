---
title: Optimera din Gen2-cache
description: Lär dig hur du övervakar Gen2-cachen med hjälp av Azure Portal.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: fed3ed2c87342d557872e97bfc2a6c4d142b5a3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585629"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Så här övervakar du adaptiv cache

Den här artikeln beskriver hur du övervakar och felsöker långsam frågans prestanda genom att fastställa om din arbets belastning optimalt utnyttjar den anpassade cachen för dedikerade SQL-pooler.

Den dedikerade arkitekturen för SQL-poolens lagrings nivå lagrar automatiskt dina mest efterfrågade columnstore-segment i en cache som finns på NVMe-baserade SSD. Du får bättre prestanda när dina frågor hämtar segment som är bosatta i cacheminnet.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med hjälp av Azure Portal

Du kan använda Azure Monitor för att visa mått för cachelagring för att felsöka frågans prestanda. Gå först till Azure Portal och klicka på **övervakaren**, **mått** och **+ Välj ett omfång**:

![Skärm bild som visar Välj ett omfång som valts från mått i Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Använd Sök-och list fälten för att hitta din dedikerade SQL-pool. Välj sedan Använd.

![Skärm bild som visar fönstret Välj ett omfång där du kan välja ditt informations lager.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Nyckel måtten för fel sökning **av cacheminnet är cacheträffar** och **cache som används i procent**. Välj **procent andels träff i procent** och Använd sedan knappen **Lägg till mått** för att lägga till **cache-använt procent**. 

![Cache-mått](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Cacheträffar och Använd procent andel

I matrisen nedan beskrivs scenarier som baseras på värdena för cache-måtten:

|                                | **Procent andel vid hög cacheträffar** | **Procent andel låg cacheträffar** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent andel Använd hög cache** |          Scenario 1           |          Scenario 2          |
| **Procent andel låg cache som används**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** Du använder din cache optimalt. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan vara långsamma för dina frågor.

**Scenario 2:** Din aktuella arbets data uppsättning får inte plats i cachen, vilket orsakar en procent andel av cacheträffar på grund av fysiska läsningar. Överväg att skala upp prestanda nivån och kör arbets belastningen igen för att fylla i cacheminnet.

**Scenario 3:** Det är troligt att frågan körs långsamt på grund av orsaker som inte är relaterade till cacheminnet. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan vara långsamma för dina frågor. Du kan också välja att [skala ned din instans](sql-data-warehouse-manage-monitor.md) för att minska storleken på cacheminnet för att spara kostnader. 

**Scenario 4:** Du hade ett kallt cacheminne som kan vara orsaken till att din fråga var långsam. Överväg att köra frågan igen eftersom din arbets data uppsättning nu ska cachelagras. 

> [!IMPORTANT]
> Om antalet träffar i procent eller i cachen som används inte uppdateras efter att du har kört om din arbets belastning, kan din arbets minne redan finnas i minnet. Endast grupperade columnstore-tabeller cachelagras.

## <a name="next-steps"></a>Nästa steg
Mer information om prestanda justering för allmänna frågor finns i [övervaka frågekörningen](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
