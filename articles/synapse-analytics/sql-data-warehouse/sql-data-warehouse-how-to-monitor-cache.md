---
title: Optimera din Gen2-cache
description: Lär dig hur du övervakar din Gen2-cache med hjälp av Azure Portal.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9de795c54f55295fa69ed7fcb5dd894e2963385b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566638"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Så här övervakar du den anpassningsbara cachen

Den här artikeln beskriver hur du övervakar och felsöker långsamma frågeprestanda genom att avgöra om din arbetsbelastning optimalt utnyttjar den anpassningsbara cachen för dedikerade SQL-pooler.

Den dedikerade lagringsarkitekturen för SQL-pooler nivåindelade automatiskt dina mest efterfrågade columnstore-segment i ett cacheminne som finns på NVMe-baserade SSD:er. Du får bättre prestanda när dina frågor hämtar segment som finns i cacheminnet.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med hjälp av Azure Portal

Du kan använda Azure Monitor för att visa cachemått för att felsöka frågeprestanda. Gå först till Azure Portal och klicka **på Övervaka,** **Mått och** + Välj ett **omfång:**

![Skärmbild som visar Välj ett omfång som valts från Mått i Azure Portal.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Använd sök- och listrutan för att hitta din dedikerade SQL-pool. Välj sedan Tillämpa.

![Skärmbild som visar fönstret Välj ett omfång där du kan välja ditt informationslager.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Viktiga mått för felsökning av cacheminnet är Träffprocent **för cache och** Procentandel cache som **används.** Välj **Cache träffprocent och** använd sedan knappen Lägg till **mått** för att lägga till **cacheanvändningsprocent**. 

![Cachemått](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Cacheträff och använt procentvärde

Matrisen nedan beskriver scenarier baserat på värdena för cachemåtten:

|                                | **Hög träffprocent för cache** | **Låg procentandel cacheträff** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Hög procentandel använt cacheminne** |          Scenario 1           |          Scenario 2          |
| **Låg procentandel använt cacheminne**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** Du använder cacheminnet optimalt. [Felsök](sql-data-warehouse-manage-monitor.md) andra områden som kan göra dina frågor långsammare.

**Scenario 2:** Din aktuella arbetsdatauppsättning får inte plats i cacheminnet, vilket orsakar en låg procentandel cacheträff på grund av fysiska läsningar. Överväg att skala upp prestandanivån och köra arbetsbelastningen igen för att fylla cachen.

**Scenario 3:** Det är troligt att frågan körs långsamt på grund av orsaker som inte är relaterade till cachen. [Felsök](sql-data-warehouse-manage-monitor.md) andra områden som kan göra dina frågor långsammare. Du kan också överväga att [skala ned din instans för](sql-data-warehouse-manage-monitor.md) att minska cachestorleken för att spara kostnader. 

**Scenario 4:** Du har en kall cache, vilket kan vara orsaken till att frågan var långsam. Överväg att köra om frågan eftersom din arbetsdatauppsättning nu ska vara cachelagrad. 

> [!IMPORTANT]
> Om procentandelen cache träffar eller procentandelen cache som används inte uppdateras efter att arbetsbelastningen har körts igen kan din arbetsuppsättning redan finnas i minnet. Endast klustrade columnstore-tabeller cachelagras.

## <a name="next-steps"></a>Nästa steg
Mer information om allmän justering av frågeprestanda finns i [Övervaka frågekörning.](sql-data-warehouse-manage-monitor.md#monitor-query-execution)
