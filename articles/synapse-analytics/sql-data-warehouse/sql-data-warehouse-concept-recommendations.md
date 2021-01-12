---
title: Azure Advisor rekommendationer för dedikerad SQL-pool
description: Lär dig mer om Synapse SQL-rekommendationer och hur de genereras
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/26/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd32b9690f8a9aef92eb1f2fbcc4ec926a65584e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121200"
---
# <a name="azure-advisor-recommendations-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Advisor rekommendationer för dedikerad SQL-pool i Azure Synapse Analytics

Den här artikeln beskriver de dedikerade rekommendationerna för SQL-poolen som finns i Azure Advisor.  

Dedikerad SQL-pool ger rekommendationer för att säkerställa att arbets belastningen för data lagret är konsekvent optimerad för prestanda. Rekommendationerna är nära integrerade med [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att ge dig bästa praxis direkt i [Azure Portal](https://aka.ms/Azureadvisor). Dedikerad SQL-pool samlar in telemetri-och underlag rekommendationer för din aktiva arbets belastning på en daglig takt. De rekommendations scenarier som stöds beskrivs nedan tillsammans med hur du använder rekommenderade åtgärder.

Du kan [kontrol lera dina rekommendationer](https://aka.ms/Azureadvisor) idag! 

## <a name="data-skew"></a>Data skevning

Dataskevning kan orsaka ytterligare data flytt eller resurs Flask halsar när du kör arbets belastningen. I följande dokumentation beskrivs hur du identifierar data skevning och förhindrar att den kan utföras genom att välja en optimal distributions nyckel.

- [Identifiera och ta bort skevning](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Ingen eller inaktuell statistik

Om du har en underoptimerad statistik kan du kraftigt påverka frågeresultatet eftersom det kan orsaka att SQL Query Optimering genererar underoptimala fråge planer. I följande dokumentation beskrivs de bästa metoderna för att skapa och uppdatera statistik:

- [Skapa och uppdatera tabell statistik](sql-data-warehouse-tables-statistics.md)

Om du vill se en lista över berörda tabeller med dessa rekommendationer kör du följande  [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor kör kontinuerligt samma T-SQL-skript för att skapa rekommendationerna.

## <a name="replicate-tables"></a>Replikera tabeller

För replikerade tabell rekommendationer identifierar Advisor tabell kandidater baserat på följande fysiska egenskaper:

- Replikerad tabell storlek
- Antal kolumner
- Tabell distributions typ
- Antal partitioner

Advisor utnyttjar kontinuerligt arbets belastnings heuristik, till exempel tabell åtkomst frekvens, rader som returneras i genomsnitt och tröskelvärden kring storlek och aktivitet för data lagret för att säkerställa att rekommendationerna skapas med hög kvalitet.

I följande avsnitt beskrivs arbets belastnings heuristik som du kan hitta i Azure Portal för varje replikerad tabell rekommendation:

- I genomsnitt används den genomsnittliga procent andelen av rader som returnerades från tabellen för varje tabell åtkomst under de senaste sju dagarna
- Frekvent läsning, ingen uppdatering – visar att tabellen inte har uppdaterats under de senaste sju dagarna vid visning av åtkomst aktivitet
- Läs-/uppdaterings kvot – förhållandet mellan hur ofta tabellen användes i relation till när den uppdaterades de senaste sju dagarna
- Aktivitet – mäter användningen baserat på åtkomst aktivitet. Den här aktiviteten jämför tabell åtkomst aktiviteten i förhållande till den genomsnittliga tabell åtkomst aktiviteten i data lagret under de senaste sju dagarna.

För närvarande kan Advisor bara visa högst fyra replikerade tabell kandidater samtidigt med grupperade columnstore-index som prioriterar den högsta aktiviteten.

> [!IMPORTANT]
> Den replikerade tabell rekommendationen är inte full och tar inte hänsyn till data flyttnings åtgärder. Vi arbetar med att lägga till detta som en tumregel, men under tiden bör du alltid validera din arbets belastning när du har tillämpat rekommendationen. Mer information om replikerade tabeller finns i följande [dokumentation](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Adaptiv (Gen2) cache-användning
När du har en stor arbets uppsättning kan du uppleva en låg cacheträffar och hög användning av cacheminnet. I det här scenariot ska du skala upp för att öka kapaciteten för cacheminnet och köra arbets belastningen igen. Mer information finns i följande [dokumentation](./sql-data-warehouse-how-to-monitor-cache.md). 

## <a name="tempdb-contention"></a>TempDB-konkurrens

Frågans prestanda kan försämras när det finns hög tempdb-konkurrens.  TempDB-konkurrens kan ske via användardefinierade temporära tabeller eller när det finns stora mängder data förflyttningar. I det här scenariot kan du skala för mer tempdb-allokering och [Konfigurera resurs klasser och arbets belastnings hantering](./sql-data-warehouse-workload-management.md) för att ge mer minne till dina frågor. 

## <a name="data-loading-misconfiguration"></a>Felaktig konfiguration av data inläsning

Du bör alltid läsa in data från ett lagrings konto i samma region som din dedikerade SQL-pool för att minimera svars tiden. Använd [kopierings instruktionen för data inmatning med högt data flöde](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) och dela upp dina mellanlagrade filer i ditt lagrings konto för att maximera data flödet. Om du inte kan använda KOPIERINGs instruktionen kan du använda SqlBulkCopy-API: et eller BCP med en hög batchstorlek för bättre data flöde. Ytterligare vägledning för data inläsning finns i följande [dokumentation](./guidance-for-loading-data.md).