---
title: Rekommendationer för dedikerad SQL Azure Advisor pool
description: Lär dig Synapse SQL rekommendationer och hur de genereras
services: synapse-analytics
author: julieMSFT
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: b418b46199c524ca92d60dece6031073938e159b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568428"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor rekommendationer för dedikerad SQL-pool i Azure Synapse Analytics

I den här artikeln beskrivs de dedikerade rekommendationer för SQL-pooler som är Azure Advisor.  

Dedikerad SQL-pool ger rekommendationer för att säkerställa att arbetsbelastningen för informationslagret är konsekvent optimerad för prestanda. Rekommendationerna är nära integrerade [med Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att ge dig bästa praxis direkt inom [Azure Portal](https://aka.ms/Azureadvisor). Dedikerad SQL-pool samlar in telemetri och ger rekommendationer för din aktiva arbetsbelastning i en daglig takt. De rekommendationsscenarier som stöds beskrivs nedan tillsammans med hur du tillämpar rekommenderade åtgärder.

Du kan [kontrollera dina rekommendationer](https://aka.ms/Azureadvisor) idag! 

## <a name="data-skew"></a>Dataskev

Dataskevnad kan orsaka ytterligare dataförflyttning eller resursflaskhalsar när du kör din arbetsbelastning. I följande dokumentation beskrivs hur du identifierar dataskevningar och förhindrar att de inträffar genom att välja en optimal distributionsnyckel.

- [Identifiera och ta bort snedskev](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Ingen eller inaktuell statistik

Icke-optimal statistik kan allvarligt påverka frågeprestanda eftersom det kan göra att SQL-frågeoptimeraren genererar icke-optimala frågeplaner. I följande dokumentation beskrivs metodtipsen för att skapa och uppdatera statistik:

- [Skapa och uppdatera tabellstatistik](sql-data-warehouse-tables-statistics.md)

Kör följande  [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)för att se listan över påverkade tabeller med dessa rekommendationer. Advisor kör kontinuerligt samma T-SQL-skript för att generera dessa rekommendationer.

## <a name="replicate-tables"></a>Replikera tabeller

För rekommendationer för replikerade tabeller identifierar Advisor tabellkandidater baserat på följande fysiska egenskaper:

- Storlek på replikerad tabell
- Antal kolumner
- Tabelldistributionstyp
- Antal partitioner

Advisor använder kontinuerligt arbetsbelastningsbaserad heuristik som åtkomstfrekvens för tabeller, rader som returneras i genomsnitt och tröskelvärden för informationslagerstorlek och aktivitet för att säkerställa att rekommendationer av hög kvalitet genereras.

I följande avsnitt beskrivs arbetsbelastningsbaserad heuristik som du kan hitta i Azure Portal för varje replikerad tabellrekommendation:

- Genomsökning av medelvärde – den genomsnittliga procentandelen rader som returnerades från tabellen för varje tabellåtkomst under de senaste sju dagarna
- Frekvent läsning, ingen uppdatering – anger att tabellen inte har uppdaterats under de senaste sju dagarna medan åtkomstaktiviteten visas
- Läs-/uppdateringsförhållande – förhållandet mellan hur ofta tabellen har varit åtkomliga i förhållande till när den uppdateras under de senaste sju dagarna
- Aktivitet – mäter användningen baserat på åtkomstaktivitet. Den här aktiviteten jämför tabellåtkomstaktiviteten i förhållande till den genomsnittliga tabellåtkomstaktiviteten i informationslagret under de senaste sju dagarna.

För närvarande visar Advisor endast högst fyra replikerade tabellkandidater samtidigt med grupperade columnstore-index som prioriterar den högsta aktiviteten.

> [!IMPORTANT]
> Rekommendationen för replikerad tabell är inte fullständigt bevis och tar inte hänsyn till dataförflyttningsåtgärder. Vi arbetar med att lägga till detta som en heuristik, men under tiden bör du alltid verifiera din arbetsbelastning när du har tillämpat rekommendationen. Mer information om replikerade tabeller finns i följande [dokumentation.](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)


## <a name="adaptive-gen2-cache-utilization"></a>Anpassningsbar (Gen2) cacheanvändning
När du har en stor arbetsuppsättning kan du uppleva en låg procentandel cacheträff och hög cacheanvändning. I det här scenariot ska du skala upp för att öka cachekapaciteten och köra arbetsbelastningen igen. Mer information finns i följande [dokumentation.](./sql-data-warehouse-how-to-monitor-cache.md) 

## <a name="tempdb-contention"></a>Tempdb-contention

Frågeprestanda kan försämras när tempdb-innehållet är högt.  Tempdb-contention kan ske via användardefinierade temporära tabeller eller när det sker en stor mängd dataförflyttning. I det här scenariot kan du skala för mer tempdb-allokering och konfigurera resursklasser och [arbetsbelastningshantering](./sql-data-warehouse-workload-management.md) för att ge mer minne till dina frågor. 

## <a name="data-loading-misconfiguration"></a>Felaktig inläsning av datakonfiguration

Du bör alltid läsa in data från ett lagringskonto i samma region som din dedikerade SQL-pool för att minimera svarstiden. Använd [COPY-instruktionen för datainmatning med](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) högt dataflöde och dela dina mellanlagringsfiler i lagringskontot för att maximera dataflödet. Om du inte kan använda COPY-instruktionen kan du använda SQLBulkCopy-API:et eller bcp med en hög batchstorlek för bättre dataflöde. Mer information om inläsning av data finns i följande [dokumentation.](./guidance-for-loading-data.md)