---
title: Hanterbarhet och övervakning – frågeaktivitet, resursutnyttjande
description: 'Lär dig vilka funktioner som är tillgängliga för att hantera och övervaka Azure Synapse Analytics. Använd de Azure Portal dynamiska hanteringsvyerna (DMV: er) för att förstå frågeaktiviteten och resursanvändningen för ditt informationslager.'
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7b103991e22ffab8ed5bd2b3c10400330e1d09b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568411"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Övervaka resursutnyttjande och frågeaktivitet i Azure Synapse Analytics

Azure Synapse Analytics ger en omfattande övervakningsupplevelse i Azure Portal att få insikter om din informationslagerarbetsbelastning. Den Azure Portal är det rekommenderade verktyget när du övervakar ditt informationslager eftersom det tillhandahåller konfigurerbara kvarhållningsperioder, aviseringar, rekommendationer och anpassningsbara diagram och instrumentpaneler för mått och loggar. Portalen gör det också möjligt att integrera med andra Azure-övervakningstjänster som Azure Monitor (loggar) med Log Analytics för att ge en holistisk övervakningsupplevelse för både ditt informationslager och hela Azure Analytics-plattformen för en integrerad övervakningsupplevelse. Den här dokumentationen beskriver vilka övervakningsfunktioner som är tillgängliga för att optimera och hantera din analysplattform Synapse SQL.

## <a name="resource-utilization"></a>Resursutnyttjande

Följande mått är tillgängliga i Azure Portal för Synapse SQL. Dessa mått visas via [Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Måttnamn             | Description                                                  | Sammansättningstyp |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-procent          | CPU-användning över alla noder för informationslagret      | Avg, Min, Max    |
| Data IO-procent      | I/O-användning över alla noder för informationslagret       | Avg, Min, Max    |
| Minnesprocent       | Minnesanvändning (SQL Server) över alla noder för informationslagret | Avg, Min, Max   |
| Aktiva frågor          | Antal aktiva frågor som körs på systemet             | Sum              |
| Köade frågor          | Antal köade frågor som väntar på att börja köras          | Sum              |
| Lyckade anslutningar  | Antal lyckade anslutningar (inloggningar) mot databasen | Summa, antal       |
| Misslyckade anslutningar      | Antal misslyckade anslutningar (inloggningar) mot databasen | Summa, antal       |
| Blockerad av brandvägg     | Antal inloggningar till informationslagret som har blockerats     | Summa, antal       |
| DWU-gräns               | Servicenivåmål för informationslagret                | Genomsnittlig, Min, Max    |
| DWU-procent          | Maximalt mellan CPU-procent och data-IO-procent        | Genomsnittlig, Min, Max    |
| DWU används                | DWU-gräns * DWU-procent                                   | Genomsnittlig, Min, Max    |
| Cache träffprocent    | (cacheträffar/cachemiss) * 100 där cacheträffar är summan av alla träffar i columnstore-segment i den lokala SSD-cachen och cachemiss är columnstore-segmentmissarna i den lokala SSD-cachen summerad över alla noder | Genomsnittlig, Min, Max    |
| Procent använt cacheminne   | (cache används/cachekapacitet) * 100 där cacheminnet som används är summan av alla byte i den lokala SSD-cachen över alla noder och cachekapacitet är summan av lagringskapaciteten för den lokala SSD-cachen över alla noder | Avg, Min, Max    |
| Lokal tempdb-procent | Lokal tempdb-användning över alla beräkningsnoder – värden genereras var femte minut | Avg, Min, Max    |

Saker att tänka på när du visar mått och ställer in aviseringar:

- DWU som används representerar endast en övergripande representation av användningen i **SQL-poolen** och är inte avsedd att vara en omfattande indikator på användning. För att avgöra om du ska skala upp eller ned bör du tänka på alla faktorer som kan påverkas av DWU, till exempel samtidighet, minne, tempdb och anpassningsbar cachekapacitet. Vi rekommenderar [att du kör din arbetsbelastning i olika DWU-inställningar](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) för att avgöra vad som fungerar bäst för att uppfylla dina affärsmål.
- Misslyckade och lyckade anslutningar rapporteras för ett visst informationslager – inte för själva servern.
- Minnesprocenten återspeglar användningen även om informationslagret är inaktivt – det återspeglar inte minnesförbrukningen för aktiva arbetsbelastningar. Använd och spåra det här måttet tillsammans med andra (tempdb, gen2-cache) för att fatta ett holistiskt beslut om skalning för ytterligare cachekapacitet ökar arbetsbelastningens prestanda för att uppfylla dina krav.

## <a name="query-activity"></a>Frågeaktivitet

För en programmatisk upplevelse vid övervakning Synapse SQL via T-SQL tillhandahåller tjänsten en uppsättning dynamiska hanteringsvyer (DMV:er). Dessa vyer är användbara när du aktivt felsöker och identifierar prestandaflaskhalsar med din arbetsbelastning.

Om du vill visa en lista över DMV:er som Synapse SQL finns i den här [dokumentationen](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Mått- och diagnostikloggning 

Både mått och loggar kan exporteras till Azure Monitor, särskilt [Azure Monitor-loggkomponenten](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och kan nås programmatiskt via [loggfrågor](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). Loggfördröjningen för Synapse SQL är cirka 10–15 minuter. Mer information om de faktorer som påverkar svarstiden finns i följande dokumentation.

## <a name="next-steps"></a>Nästa steg

I följande guide beskrivs vanliga scenarier och användningsfall när du övervakar och hanterar ditt informationslager:

- [Övervaka din arbetsbelastning för informationslager med DMV:er](sql-data-warehouse-manage-monitor.md)