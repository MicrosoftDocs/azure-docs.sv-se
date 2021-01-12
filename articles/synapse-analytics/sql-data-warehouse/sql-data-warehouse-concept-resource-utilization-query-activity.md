---
title: Hantering och övervakning – fråga aktivitet, resursutnyttjande
description: 'Lär dig vilka funktioner som är tillgängliga för hantering och övervakning av Azure Synapse Analytics. Använd Azure Portal-och DMV: er (Dynamic Management views) för att förstå frågans aktivitet och resursutnyttjande för ditt informations lager.'
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 75c0058f2d4878016281656935cbd82f79b859fd
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120010"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Övervaka resursutnyttjande och fråga aktivitet i Azure Synapse Analytics

Azure Synapse Analytics ger en omfattande övervaknings upplevelse inom Azure Portal till Surface Insights för arbets belastningen för informations lagret. Azure Portal är det rekommenderade verktyget när du övervakar ditt informations lager eftersom det tillhandahåller konfigurerbara kvarhållningsperiod, varningar, rekommendationer och anpassningsbara diagram och instrument paneler för mått och loggar. Portalen gör det också möjligt att integrera med andra Azure Monitoring-tjänster som Azure Monitor (loggar) med Log Analytics för att ge en helhets övervakning av inte bara ditt informations lager, utan även hela din Azure Analytics-plattform för en integrerad övervaknings upplevelse. Den här dokumentationen beskriver vilka övervaknings funktioner som är tillgängliga för att optimera och hantera din analys plattform med Synapse SQL.

## <a name="resource-utilization"></a>Resursutnyttjande

Följande mått är tillgängliga i Azure Portal för SQL i Synapse. Dessa mått är uppdelade via [Azure Monitor](../../azure-monitor/platform/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Måttnamn             | Description                                                  | Sammansättningstyp |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-procent          | CPU-användning över alla noder för data lagret      | Genomsn, min, max    |
| Data IO-procent      | I/o-användning över alla noder för data lagret       | Genomsn, min, max    |
| Minnes procent       | Minnes användning (SQL Server) över alla noder för data lagret | Genomsn, min, max   |
| Aktiva frågor          | Antal aktiva frågor som körs på systemet             | Sum              |
| Köade frågor          | Antal köade frågor som väntar på att börja köras          | Sum              |
| Lyckade anslutningar  | Antal lyckade anslutningar (inloggningar) mot databasen | Sum, antal       |
| Misslyckade anslutningar      | Antal misslyckade anslutningar (inloggningar) mot databasen | Sum, antal       |
| Blockerad av brandvägg     | Antal inloggningar till data lagret som blockerades     | Sum, antal       |
| DWU-gräns               | Informations lagrets service nivå mål                | Genomsn, min, max    |
| DWU procent          | Maximalt mellan CPU-procent och data IO-procent        | Genomsn, min, max    |
| DWU som används                | DWU-gräns * DWU procent                                   | Genomsn, min, max    |
| Procent andel cacheträffar    | (cacheträffar/cache missar) * 100 där cacheträffar är summan av alla träffar i det lokala SSD-cacheminnet och cache missar är columnstore-segmenten missar i den lokala SSD-cachen som summeras för alla noder | Genomsn, min, max    |
| Procent andel som används   | (cache-använt/cache-kapacitet) * 100 där cache används är summan av alla byte i den lokala SSD-cachen på alla noder och cache-kapacitet är summan av lagrings kapaciteten för den lokala SSD-cachen på alla noder | Genomsn, min, max    |
| Lokal tempdb-procent | Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut | Genomsn, min, max    |

Saker att tänka på när du visar mått och ställer in aviseringar:

- DWU som används representerar en övergripande **representation av användningen** i SQL-poolen och är inte avsedd att vara en omfattande indikator för användning. För att avgöra om du vill skala upp eller ned bör du ta hänsyn till alla faktorer som kan påverkas av DWU, till exempel samtidighet, minne, tempdb och adaptiv cache-kapacitet. Vi rekommenderar att du [Kör arbets belastningen på olika DWU-inställningar](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) för att avgöra vad som fungerar bäst för att uppfylla dina affärs mål.
- Misslyckade och lyckade anslutningar rapporteras för ett visst informations lager – inte för själva servern.
- Minnes procent visar användningen även om informations lagret är i inaktivt läge – det visar inte den aktiva minnes användningen för arbets belastningen. Använd och spåra det här måttet tillsammans med andra (tempdb, Gen2 cache) för att fatta ett holistiskt beslut om skalning för ytterligare cache-kapacitet ökar arbets belastnings prestandan för att uppfylla dina krav.

## <a name="query-activity"></a>Fråga aktivitet

För en programmerings upplevelse vid övervakning av Synapse SQL via T-SQL tillhandahåller tjänsten en uppsättning dynamiska hanterings vyer (DMV: er). Dessa vyer är användbara när du aktivt ska felsöka och identifiera Flask halsar i prestanda med din arbets belastning.

Om du vill visa en lista över DMV: er som gäller för Synapse SQL, se den här [dokumentationen](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Mått- och diagnostikloggning 

Både mått och loggar kan exporteras till Azure Monitor, särskilt [Azure Monitor loggar](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) -komponenten och kan nås via programmering via [logg frågor](../../azure-monitor/log-query/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). Logg fördröjningen för Synapse SQL är cirka 10-15 minuter. Mer information om de faktorer som påverkar svars tiderna finns i följande dokumentation.

## <a name="next-steps"></a>Nästa steg

I följande instruktions guide beskrivs vanliga scenarier och användnings fall när du övervakar och hanterar ditt informations lager:

- [Övervaka arbets belastningen för informations lagret med DMV: er](sql-data-warehouse-manage-monitor.md)