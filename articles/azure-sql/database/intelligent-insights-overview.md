---
title: Övervaka databas prestanda med Intelligent Insights
description: Intelligent Insights i Azure SQL Database och Azure SQL-hanterad instans använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592078"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights att använda AI för att övervaka och felsöka databas prestanda (för hands version)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights i Azure SQL Database och Azure SQL-hanterad instans kan du se vad som händer med databasens prestanda.

Intelligent Insights använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. När den har identifierats utförs en detaljerad analys som genererar en Intelligent Insights resurs logg (kallas SQLInsights) med en intelligent utvärdering av problemet. Den här utvärderingen består av en rotor Saks analys av databasens prestanda problem och, om möjligt, rekommendationer för prestanda förbättringar.

## <a name="what-can-intelligent-insights-do-for-you"></a>Vad kan Intelligent Insights göra

Intelligent Insights är en unik funktion i Azure inbyggda intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestanda insikter
- Tidig identifiering av databas prestanda försämring
- Rotor Saks analys av problem som upptäckts
- Rekommendationer för prestanda förbättring
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="how-does-intelligent-insights-work"></a>Hur fungerar Intelligent Insights

Intelligent Insights analyserar databas prestanda genom att jämföra databas arbets belastningen från den senaste timmen med den senaste sju dagars bas belastningen. Databasens arbets belastning består av frågor som bedöms vara mest betydelsefulla för databasens prestanda, till exempel de mest upprepande och största frågorna. Eftersom varje databas är unik baserat på dess struktur, data, användning och program, är varje bas linje för arbets belastningar som genereras specifik och unik för den arbets belastningen. Intelligent Insights, oberoende av arbets belastnings bas linjen, övervakar även absoluta drift tröskelvärden och identifierar problem med onödigt vänte tider, kritiska undantag och problem med parameterizations som kan påverka prestandan.

När ett problem med prestanda försämring har upptäckts från flera observerade mått med hjälp av artificiell intelligens utförs analysen. En diagnostisk logg skapas med en intelligent insikt om vad som händer med din databas. Intelligent Insights är det enkelt att spåra problem med databas prestanda från det första utseendet tills lösningen upprättas. Varje identifierat problem spåras genom livs cykeln från inledande problem identifiering och verifiering av prestanda förbättringar för slut för ande.

![Arbets flöde för databas prestanda analys](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Måtten som används för att mäta och identifiera problem med databas prestanda baseras på frågans varaktighet, timeout-begär Anden, överdriven vänte tid och felaktiga begär Anden. Mer information om mått finns i [identifierings mått](#detection-metrics).

Identifierade databas prestanda försämringar registreras i SQLInsights-loggen med intelligenta poster som består av följande egenskaper:

| Egenskap | Information |
| :------------------- | ------------------- |
| Databas information | Metadata om en databas där insikter upptäcktes, till exempel en resurs-URI. |
| Observerat tidsintervall | Start-och slut tid för den identifierade insikten. |
| Påverkade mått | Mått som gjorde att en insikt genererades: <ul><li>Ökning av frågans varaktighet [sekunder].</li><li>Överdriven väntan [sekunder].</li><li>Timeout-begär Anden [procent].</li><li>Misslyckade begär Anden [procent].</li></ul>|
| Effekt värde | Värde för uppmätt mått. |
| Påverkade frågor och felkoder | Fråga hash-kod eller felkod. Dessa kan användas för att enkelt korrelera till berörda frågor. Mått som består av en ökning av frågans varaktighet, vänte tid, timeout-antal eller felkoder tillhandahålls. |
| Identifieringar | Identifiering som identifieras i databasen under tiden för en händelse. Det finns 15 identifierings mönster. Mer information finns i [Felsöka problem med databas prestanda med intelligent Insights](intelligent-insights-troubleshoot-performance.md). |
| Rotorsaksanalys | Rotor Saks analys av problemet som identifieras i ett läsbart format. Vissa insikter kan innehålla en rekommendation om prestanda förbättringar där det är möjligt. |
|||

En praktisk översikt över hur du använder Intelligent Insights med Azure SQL-analys och för vanliga användnings scenarier finns i den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights Skins vid identifiering och fel sökning av databas prestanda problem. För att kunna använda Intelligent Insights för att felsöka problem med databas prestanda, se [Felsöka prestanda problem med intelligent Insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Intelligent Insights alternativ

Intelligent Insights alternativ som är tillgängliga är:

| Intelligent Insights alternativ | Azure SQL Database support | Support för Azure SQL-hanterad instans |
| :----------------------------- | ----- | ----- |
| **Konfigurera intelligent Insights** -konfigurera intelligent Insights analys för dina databaser. | Ja | Ja |
| **Strömma insikter till Azure SQL-analys** – strömma insikter till Azure SQL-analys. | Ja | Ja |
| **Strömma insikter till Azure Event Hubs** -Stream Insights för att Event Hubs för ytterligare anpassade integreringar. | Ja | Ja |
| **Strömma insikter till Azure Storage** Stream Insights för att Azure Storage för ytterligare analys och långsiktig arkivering. | Ja | Ja |

> [!NOTE]
> Intelligenta insikter är en förhands gransknings funktion som inte är tillgänglig i följande regioner: Västeuropa, norra Europa, västra USA 1 och östra USA 1.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurera exporten av Intelligent Insights loggen

Utdata från Intelligent Insights kan strömmas till en av flera mål för analys:

- Utdata som strömmas till en Log Analytics arbets yta kan användas med [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md) för att Visa insikter via användar gränssnittet i Azure Portal. Det här är den integrerade Azure-lösningen och det vanligaste sättet att Visa insikter.
- Utdata som strömmas till Azure Event Hubs kan användas för utveckling av anpassade övervaknings-och aviserings scenarier
- Utdata som strömmas till Azure Storage kan användas för utveckling av anpassade program, t. ex. anpassade rapporter, långsiktig dataarkivering och så vidare.

Integrering av Azure SQL-analys, Azure Event Hubs, Azure Storage eller produkter från tredje part för konsumtion utförs genom att först aktivera Intelligent Insights loggning ("SQLInsights"-loggen) på bladet diagnostikinställningar i en databas och sedan konfigurera Intelligent Insights loggdata så att de strömmas till någon av dessa destinationer.

Mer information om hur du aktiverar Intelligent Insights loggning och konfigurerar mått-och resurs logg data som ska strömmas till en konsumerande produkt finns i [statistik och diagnostisk loggning](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Konfigurera med Azure SQL-analys

Azure SQL-analys-lösningen tillhandahåller grafiskt användar gränssnitt, rapporterings-och aviserings funktioner för databas prestanda med hjälp av Intelligent Insights resurs logg data.

Lägg till Azure SQL-analys i Azure Portal-instrumentpanelen från Marketplace och skapa en arbets yta finns i [konfigurera Azure SQL-analys](../../azure-monitor/insights/azure-sql.md#configuration)

Om du vill använda Intelligent Insights med Azure SQL-analys konfigurerar du Intelligent Insights loggdata som ska strömmas till Azure SQL-analys arbets ytan som du skapade i föregående steg, se [statistik och diagnostikloggning](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

I följande exempel visas en Intelligent Insights som visas via Azure SQL-analys:

![Intelligent Insights rapport](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Konfigurera med Event Hubs

Om du vill använda Intelligent Insights med Event Hubs konfigurerar du Intelligent Insights loggdata som ska strömmas till Event Hubs, se [mått och diagnostik](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) loggar och [strömma Azure-diagnostikloggar till Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

Om du vill använda Event Hubs för att konfigurera anpassad övervakning och aviseringar, se [vad du kan göra med mått och diagnostikloggar i Event Hubs](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs).

### <a name="set-up-with-azure-storage"></a>Konfigurera med Azure Storage

Om du vill använda Intelligent Insights med lagring konfigurerar du Intelligent Insights loggdata som ska strömmas till lagring, se [mått och diagnostikloggning](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) och [strömma till Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Anpassad integrering av Intelligent Insights logg

Om du vill använda Intelligent Insights med verktyg från tredje part, eller om du vill ha en anpassad utveckling och övervakning, kan du läsa mer i [använda loggen för att använda intelligent Insights Database](intelligent-insights-use-diagnostics-log.md)

## <a name="detection-metrics"></a>Identifierings mått

Mått som används för identifierings modeller som genererar Intelligent Insights baseras på övervakning:

- Frågans varaktighet
- Timeout-begäranden
- Lång vänte tid
- Misslyckade förfrågningar

Frågans varaktighet och tids gräns för förfrågningar används som primära modeller vid identifiering av problem med databasens arbets belastnings prestanda. De används eftersom de direkt mäter vad som händer med arbets belastningen. För att identifiera alla möjliga fall av prestanda försämring av arbets belastningen, används en alltför lång vänte tid och fel förfrågningar som ytterligare modeller för att ange problem som påverkar arbets belastnings prestandan.

Systemet tar automatiskt hänsyn till ändringar i arbets belastningen och ändrar antalet förfrågningar som görs till databasen dynamiskt för att dynamiskt fastställa normala och färdiga tröskelvärden för databas prestanda.

Alla mät värden beaktas tillsammans i olika relationer via en vetenskapligt härledd data modell som kategoriserar varje identifierat prestanda problem. Information som tillhandahålls via intelligenta insikter innehåller:

- Information om prestanda problem har identifierats.
- En rotor Saks analys av problemet upptäcktes.
- Rekommendationer för hur du kan förbättra prestandan hos den övervakade databasen, där det är möjligt.

## <a name="query-duration"></a>Frågans varaktighet

Modellen för frågans varaktighets försämring analyserar enskilda frågor och upptäcker ökningen under den tid det tar att kompilera och köra en fråga jämfört med prestanda bas linjen.

Om den inbyggda intelligensen upptäcker en betydande ökning av frågans kompilering eller tid för att köra frågor som påverkar arbets belastnings prestanda flaggas dessa frågor som problem med prestanda försämring av frågor.

I Intelligent Insights Diagnostics-loggen visas frågans hash-nummer för frågan försämrad i prestanda. Frågans hash anger om prestanda försämringen var relaterad till en fråga kompilera eller körnings tids ökning, vilket ökar frågans varaktighets tid.

## <a name="timeout-requests"></a>Timeout-begäranden

Modellen för tids gräns för tids gräns analys analyserar enskilda frågor och identifierar eventuella ökningar i tids gränsen på frågans körnings nivå och den övergripande tids gränsen för begäran på databas nivån jämfört med prestanda bas linje perioden.

Vissa av frågorna kan ta lång tid även innan de når körnings fasen. Med hjälp av förkastade arbetare och begär Anden som gjorts, inbyggda Intelligence-åtgärder och analyserar alla frågor som har nått databasen, oavsett om de kom till körnings fasen eller inte.

När antalet tids gränser för exekverade frågor eller antalet avbrutna förfrågningar om arbetare korsar det systemhanterade tröskelvärdet fylls en diagnostisk logg med intelligenta insikter.

De insikter som genereras innehåller antalet förfrågningar om timeout och antalet tids gräns frågor. Indikationen på prestanda försämringen är relaterad till att tids gränsen ökar vid körningen, eller så tillhandahålls den övergripande databas nivån. När tids gränsen bedöms vara viktig för databas prestanda flaggas dessa frågor som tids gräns för försämring av prestanda problem.

## <a name="excessive-wait-times"></a>Lång vänte tid

Den förstora vänte tids modellen övervakar enskilda databas frågor. Den identifierar ovanligt hög vänte tid för frågor som korsade de absoluta tröskelvärdena som hanteras av systemet. Följande fråga för överdriven väntan/tid-mått observeras med hjälp av, [vänte läge för Query Store (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql):

- Når resurs gränser
- Når resurs gränser för elastisk pool
- Högt antal arbets-eller sessions trådar
- Överdriven databas låsning
- Minnesbelastning
- Annan wait-statistik

Att nå resurs gränser eller resurs gränser för elastisk pool anger att förbrukningen av tillgängliga resurser i en prenumeration eller i den elastiska poolen överskrider absoluta tröskelvärden. Den här statistiken indikerar försämring av arbets belastnings prestanda. Ett högt antal arbets trådar eller sessionsnycklar anger ett villkor där antalet arbets trådar eller sessioner initieras över absoluta tröskelvärden. Den här statistiken indikerar försämring av arbets belastnings prestanda.

Överdriven databas låsning anger ett villkor där antalet lås på en databas har korsade absoluta tröskelvärden. Denna stat anger prestanda försämring av arbets belastningar. Minnes belastning är ett villkor där antalet trådar som begär minnes anslag korsar ett absolut tröskelvärde. Denna stat anger prestanda försämring av arbets belastningar.

Annan identifiering av väntande statistik indikerar ett villkor där Diverse mått som mäts genom Query Store wait-statistik korsar ett absolut tröskelvärde. Den här statistiken indikerar försämring av arbets belastnings prestanda.

När alltför långa vänte tider har identifierats, beroende på vilka data som är tillgängliga, visar Intelligent Insights Diagnostics-loggen hashar av de påverkade och påverkade frågorna som försämras i prestanda, information om de mått som orsakar att frågor väntar på körning och uppmätt vänte tid.

## <a name="errored-requests"></a>Felaktiga begär Anden

Den felaktiga begär ande försämrings modellen övervakar enskilda frågor och identifierar en ökning av antalet frågor som har fel jämfört med bas linje perioden. Den här modellen övervakar också kritiska undantag som överskrider absoluta tröskelvärden som hanteras av inbyggd intelligens. Systemet betraktar automatiskt antalet förfrågningar som gjorts till databasen och kontona för eventuella arbets belastnings ändringar i den övervakade perioden.

När den uppmätta ökningen av felaktiga begär anden i förhållande till det totala antalet begär Anden som har gjorts bedöms vara viktig för arbets belastnings prestanda, flaggas berörda frågor som fel vid problem med prestanda försämring.

I Intelligent Insights loggen visas antalet felaktiga begär Anden. Det anger om prestanda försämringen var relaterad till en ökning av felorsakade begär Anden eller för att passera en övervakad kritisk undantags tröskel och uppmätt tid för prestanda försämring.

Om något av de övervakade kritiska undantagen passerar de absoluta tröskelvärdena som hanteras av systemet, genereras en intelligent insikt med information om kritiskt undantag.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [övervakar databaser med hjälp av SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Lär dig hur du [felsöker prestanda problem med intelligent Insights](intelligent-insights-troubleshoot-performance.md).