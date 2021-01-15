---
title: Felsöka prestandaproblem med Intelligent Insights
description: Intelligent Insights hjälper dig att felsöka prestanda problem med Azure SQL Database och Azure SQL-hanterade instanser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: troubleshooting
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 3b57172daeffd1766da456e56cb5e445427a4858
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220396"
---
# <a name="troubleshoot-azure-sql-database-and-azure-sql-managed-instance-performance-issues-with-intelligent-insights"></a>Felsök prestanda problem med Azure SQL Database och Azure SQL-hanterade instanser med Intelligent Insights
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här sidan innehåller information om prestanda problem för Azure SQL Database och Azure SQL-hanterade instanser i [intelligent Insights](intelligent-insights-overview.md) resurs loggen. Mått och resurs loggar kan strömmas till [Azure Monitor loggar](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)eller en lösning från tredje part för anpassade DevOps-aviseringar och rapporterings funktioner.

> [!NOTE]
> En snabb prestanda fel söknings guide med hjälp av Intelligent Insights finns i det [rekommenderade fel söknings flödet](intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) i det här dokumentet.
>
> Intelligenta insikter är en förhands gransknings funktion som inte är tillgänglig i följande regioner: Västeuropa, norra Europa, västra USA 1 och östra USA 1.

## <a name="detectable-database-performance-patterns"></a>Identifiera mönster för databas prestanda

Intelligent Insights identifierar automatiskt prestanda problem baserat på vänte tider, fel eller tids gränser för frågekörning. Intelligent Insights utdata identifierade prestanda mönster till resurs loggen. Identifierade prestanda mönster sammanfattas i tabellen nedan.

| Identifierbara prestandamönster | Azure SQL Database | Hanterad Azure SQL-instans |
| :------------------- | ------------------- | ------------------- |
| [Når resurs gränser](intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Förbrukningen av tillgängliga resurser (DTU: er), databas arbets trådar eller databas inloggnings sessioner som är tillgängliga i den övervakade prenumerationen har nått sin resurs gräns. Detta påverkar prestanda. | Förbrukningen av CPU-resurser når resurs gränserna. Detta påverkar databasens prestanda. |
| [Ökad arbets belastning](intelligent-insights-troubleshoot-performance.md#workload-increase) | Ökad arbets belastning eller kontinuerlig ackumulering av arbets belastningen på databasen upptäcktes. Detta påverkar prestanda. | Arbets belastnings ökning har upptäckts. Detta påverkar databasens prestanda. |
| [Minnesbelastning](intelligent-insights-troubleshoot-performance.md#memory-pressure) | Arbetare som begärde minnes anslag måste vänta på minnes tilldelningar för statistiskt betydande tid eller en ökad ackumulering av arbets tagare som begärt minnes bidrag. Detta påverkar prestanda. | Arbetare som har begärt minnes bidrag väntar på minnes tilldelningar för en statistiskt betydande tids period. Detta påverkar databasens prestanda. |
| [Låsning](intelligent-insights-troubleshoot-performance.md#locking) | Onödig databas låsning påträffades för prestanda. | Databasens prestanda har upptäckts för överdriven databas. |
| [Ökad MAXDOP](intelligent-insights-troubleshoot-performance.md#increased-maxdop) | Den högsta graden av Parallel-alternativ (MAXDOP) har ändrats påverkar effektiviteten i körningen av frågan. Detta påverkar prestanda. | Den högsta graden av Parallel-alternativ (MAXDOP) har ändrats påverkar effektiviteten i körningen av frågan. Detta påverkar prestanda. |
| [PAGELATCH-konkurrens](intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Flera trådar försöker samtidigt få åtkomst till samma InMemory DataBuffer-sidor som resulterar i ökade vänte tider och orsakar PAGELATCH konkurrens. Detta påverkar prestanda. | Flera trådar försöker samtidigt få åtkomst till samma InMemory DataBuffer-sidor som resulterar i ökade vänte tider och orsakar PAGELATCH konkurrens. Detta påverkar prestandan för databasen. |
| [Index saknas](intelligent-insights-troubleshoot-performance.md#missing-index) | Indexet som saknas påverkade prestanda. | Indexet som saknas har identifierats som påverkar databasens prestanda. |
| [Ny fråga](intelligent-insights-troubleshoot-performance.md#new-query) | En ny fråga som påverkar den övergripande prestandan påträffades. | En ny fråga som påverkar den övergripande databas prestandan påträffades. |
| [Ökad vänte statistik](intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) | Ökad vänte tid för databasen upptäcktes för prestanda. | Ökade vänte tider för databasen upptäcktes under databasens prestanda. |
| [TempDB-konkurrens](intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en Flask hals. Detta påverkar prestanda. | Flera trådar försöker komma åt samma TempDB-resurs som orsakar en Flask hals. Detta påverkar databasens prestanda. |
| [DTU-underskott för elastisk pool](intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | Brist på tillgängliga eDTU: er i den elastiska poolen påverkar prestanda. | Inte tillgängligt för Azure SQL-hanterad instans eftersom den använder vCore-modellen. |
| [Plan regression](intelligent-insights-troubleshoot-performance.md#plan-regression) | Ny plan eller en ändring i arbets belastningen för en befintlig plan upptäcktes. Detta påverkar prestanda. | Ny plan eller en ändring i arbets belastningen för en befintlig plan upptäcktes. Detta påverkar databasens prestanda. |
| [Ändring av konfigurations värde för databas omfattning](intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Konfigurations ändringen av databasen upptäcktes och gjorde att databasens prestanda påverkas. | Konfigurations ändringen av databasen upptäcktes och gjorde att databasens prestanda påverkas. |
| [Långsam klient](intelligent-insights-troubleshoot-performance.md#slow-client) | Långsam program klient kan inte använda utdata från databasen tillräckligt snabbt. Detta påverkar prestanda. | Långsam program klient kan inte använda utdata från databasen tillräckligt snabbt. Detta påverkar databasens prestanda. |
| [Nedgradering av pris nivå](intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | Åtgärden för nedgradering av pris nivå minskar tillgängliga resurser. Detta påverkar prestanda. | Åtgärden för nedgradering av pris nivå minskar tillgängliga resurser. Detta påverkar databasens prestanda. |

> [!TIP]
> Aktivera [Automatisk justering](automatic-tuning-overview.md)för kontinuerlig prestanda optimering av databaser. Den här inbyggda intelligensen övervakar kontinuerligt din databas, justerar automatiskt index och tillämpar korrigeringar för frågekörningen.
>

I följande avsnitt beskrivs identifierade prestanda mönster mer detaljerat.

## <a name="reaching-resource-limits"></a>Når resurs gränser

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret kombinerar prestanda problem som rör tillgängliga resurs gränser, arbets gränser och sessionsgränser. När detta prestanda problem har identifierats anger ett beskrivnings fält i Diagnostic-loggen om prestanda problemet är relaterat till resurs-, arbets-eller sessionsgräns.

Resurser på Azure SQL Database kallas vanligt vis för [DTU](service-tiers-dtu.md) -eller [vCore](service-tiers-vcore.md) -resurser och resurser på Azure SQL-hanterad instans kallas för vcore-resurser. Mönstret för att nå resurs gränserna identifieras när prestanda försämringen för frågor har identifierats genom att nå någon av de uppmätta resurs gränserna.

Resursen för sessionsgränser anger antalet tillgängliga samtidiga inloggningar för databasen. Detta prestanda mönster identifieras när program som är anslutna till databaserna har uppnått antalet tillgängliga samtidiga inloggningar till databasen. Om program försöker använda fler sessioner än vad som är tillgängligt i en databas påverkas frågans prestanda.

Att nå arbets gränser är ett särskilt fall där resurs begränsningar nås, eftersom tillgängliga arbetare inte räknas i DTU-eller vCore-användningen. Att nå arbetarnas gränser för en databas kan orsaka en ökning av resursens angivna vänte tider, vilket leder till försämrade prestanda.

### <a name="troubleshooting"></a>Felsökning

I diagnostikloggar visas frågor om hash-värden för frågor som påverkar procent andelen prestanda och resursförbrukning. Du kan använda den här informationen som utgångs punkt för att optimera databasens arbets belastning. I synnerhet kan du optimera de frågor som påverkar prestanda försämringen genom att lägga till index. Eller så kan du optimera program med en mer jämn arbets belastnings distribution. Om du inte kan minska arbets belastningarna eller göra optimeringar bör du överväga att öka pris nivån för databas prenumerationen för att öka mängden tillgängliga resurser.

Om du har nått gränserna för tillgängliga sessioner kan du optimera dina program genom att minska antalet inloggningar som görs i databasen. Om du inte kan minska antalet inloggningar från dina program till databasen kan du öka pris nivån för din databas prenumeration. Du kan också dela upp och flytta databasen till flera databaser för en mer bal anse rad arbets belastnings distribution.

Mer information om hur du löser sessionsgränser finns i [Hantera gränserna för maximala inloggningar](/archive/blogs/latam/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins). Se [Översikt över resurs begränsningar på en server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.

## <a name="workload-increase"></a>Ökad arbets belastning

### <a name="what-is-happening"></a>Vad händer

Det här prestanda mönstret identifierar problem som orsakas av en arbets belastnings ökning eller, i dess mer allvarliga form, en arbets belastnings grupp.

Den här identifieringen görs genom en kombination av flera mått. Det grundläggande måttet som mäts är en ökning av arbets belastningen jämfört med den tidigare bas linjen för arbets belastningen. Den andra typen av identifiering baseras på att mäta en stor ökning av aktiva arbets trådar som är tillräckligt stora för att påverka frågans prestanda.

I ett mer allvarligt formulär kan arbets belastningen sammanställa kontinuerligt på grund av att en databas inte kan hantera arbets belastningen. Resultatet är en ständigt växande arbets belastnings storlek, vilket är ett villkor för arbets belastnings sammanställningen. På grund av det här tillståndet är tiden som arbets belastningen väntar på att körningen växer. Det här tillståndet representerar ett av de mest allvarliga prestanda problemen i databasen. Det här problemet upptäcks genom övervakning av ökningen av antalet avbrutna arbets trådar.

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas antalet frågor vars körning har ökat och frågans hash-fråga med det största bidraget till arbets belastnings ökningen. Du kan använda den här informationen som utgångs punkt för att optimera arbets belastningen. Frågan som identifieras som största bidrags givare till arbets belastnings ökningen är särskilt användbar som start punkt.

Du kan överväga att distribuera arbets belastningarna mer jämnt till databasen. Överväg att optimera frågan som påverkar prestandan genom att lägga till index. Du kan också distribuera arbets belastningen mellan flera databaser. Om dessa lösningar inte är möjliga bör du överväga att öka pris nivån för databas prenumerationen för att öka mängden tillgängliga resurser.

## <a name="memory-pressure"></a>Minnesbelastning

### <a name="what-is-happening"></a>Vad händer

Det här prestanda mönstret indikerar försämring i den aktuella databas prestandan som orsakas av minnes belastningen, eller i dess mer allvarliga form ett minnes sammanställnings villkor jämfört med den senaste sju dagars prestanda bas linjen.

Minnes belastningen anger ett prestanda villkor där det finns ett stort antal arbets trådar som begär minnes anslag. Den stora volymen orsakar ett högt minnes användnings villkor där databasen inte kan allokera minne på ett effektivt sätt till alla arbets tagare som begär det. En av de vanligaste orsakerna till det här problemet är relaterad till mängden minne som är tillgängligt för databasen på en hand. Å andra sidan orsakar en ökning i arbets belastningen ökningen av arbets trådar och minnes belastning.

En mer svår form av minnes belastning är minnes sammanställnings villkoret. Det här villkoret indikerar att ett högre antal arbets trådar begär minnes anslag än det finns frågor som frigör minnet. Det här antalet arbets trådar som begär minnes anslag kan också öka kontinuerligt (Piling upp) eftersom databas motorn inte kan allokera tillräckligt mycket minne för att uppfylla behovet. Minnes sammanställnings villkoret representerar ett av de mest allvarliga databas prestanda problemen.

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas minnes objektets lagrings information med den ansvariga (dvs. arbets tråd) som är den högsta orsaken för hög minnes användning och relevanta tidsstämplar. Du kan använda den här informationen som grund för fel sökning.

Du kan optimera eller ta bort frågor som är relaterade till clerkerna med högsta minnes användning. Du kan också se till att du inte frågar efter data som du inte planerar att använda. Bra tillvägagångs är att alltid använda en WHERE-sats i dina frågor. Dessutom rekommenderar vi att du skapar grupperade index för att söka efter data i stället för att genomsöka dem.

Du kan också minska arbets belastningen genom att optimera eller distribuera den över flera databaser. Du kan också distribuera arbets belastningen mellan flera databaser. Om dessa lösningar inte är möjliga bör du överväga att öka pris nivån för databasen för att öka mängden minnes resurser som är tillgängliga för databasen.

Ytterligare fel söknings förslag finns i [minnes beviljande meditation: mystiska SQL Server minnes konsument med många namn](https://techcommunity.microsoft.com/t5/sql-server-support/memory-grants-meditation-the-mysterious-sql-server-memory/ba-p/333994).

## <a name="locking"></a>Låsning

### <a name="what-is-happening"></a>Vad händer

Det här prestanda mönstret indikerar försämring i den aktuella databas prestandan där överdriven databas låsning upptäcks jämfört med den senaste sju dagars prestanda bas linjen.

I modern RDBMS är låsning nödvändig för att implementera multitrådade system där prestanda maximeras genom att köra flera samtidiga arbetare och parallella databas transaktioner där det är möjligt. Låsning i det här sammanhanget syftar på den inbyggda Access-mekanismen där endast en enskild transaktion kan komma åt de rader, sidor, tabeller och filer som krävs och inte konkurrerar med en annan transaktion för resurser. När den transaktion som låste resurserna för användning görs med, släpps låset på dessa resurser, vilket gör att andra transaktioner kan komma åt nödvändiga resurser. Mer information om att låsa finns i [Lås i databas motorn](/previous-versions/sql/sql-server-2008-r2/ms190615(v=sql.105)).

Om transaktioner som körs av SQL-motorn väntar på längre tids perioder för att få åtkomst till resurser som är låsta för användning, kan den här vänte tiden leda till att prestandan för arbets belastningen går långsammare.

### <a name="troubleshooting"></a>Felsökning

I diagnostik-loggen visas låsnings information som du kan använda som grund för fel sökning. Du kan analysera de rapporterade blockerande frågorna, det vill säga de frågor som introducerar prestanda försämringen och ta bort dem. I vissa fall kan det hända att du har optimerat spärrnings frågorna.

Det enklaste och säkraste sättet att åtgärda problemet är att hålla transaktionerna korta och minska de dyraste frågornas lock-avtryck. Du kan dela upp en stor sats med åtgärder i mindre operationer. Bra tillvägagångs sätt är att minska storleken på frågearkivet genom att göra frågan så effektiv som möjligt. Minska stora inläsningar eftersom de ökar risken för död lägen och påverkar den övergripande databas prestanda negativt. För identifierade frågor som orsakar låsning kan du skapa nya index eller lägga till kolumner i det befintliga indexet för att undvika att tabellen genomsöks.

Fler förslag finns i:
- [Förstå och lösa problem med Azure SQL-blockering](understand-resolve-blocking.md)
- [Lösa blockerings problem som orsakas av lås eskalering i SQL Server](https://support.microsoft.com/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)

## <a name="increased-maxdop"></a>Ökad MAXDOP

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret indikerar ett villkor där en vald frågeplan för frågekörningen är parallellt mer än vad som borde ha varit. Fråga optimering kan förbättra arbets belastnings prestandan genom att köra frågor parallellt för att snabba upp saker där det är möjligt. I vissa fall kan parallella arbetare som bearbetar en fråga ägna mer tid åt att synkronisera och slå samman resultat jämfört med att köra samma fråga med färre parallella arbetare, eller till och med i vissa fall jämfört med en enda arbets tråd.

Expert systemet analyserar den aktuella databas prestandan jämfört med bas linje perioden. Den avgör om en tidigare körnings fråga körs långsammare än tidigare eftersom frågekörningen är mer parallell än den ska vara.

Konfigurations alternativet för MAXDOP används för att styra hur många processor kärnor som kan användas för att köra samma fråga parallellt.

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas fråga-hashar som är relaterade till frågor för vilka körnings tiden ökar, eftersom de har blivit parallella än vad de hade. Loggen utvärderar även CXP vänte tider. Den här tiden representerar tiden som en enskild organisatör/koordinator tråd (tråd 0) väntar på att alla andra trådar ska slutföras innan resultaten slås samman och flyttas framåt. Dessutom utvärderar diagnostikloggar de vänte tider som de dåligt presterande frågorna väntade i körnings övergripande. Du kan använda den här informationen som grund för fel sökning.

Börja med att optimera eller förenkla komplexa frågor. Bra tillvägagångs sätt är att dela upp långa batch-jobb i mindre. Se dessutom till att du har skapat index som stöd för dina frågor. Du kan också manuellt framtvinga den högsta graden av parallellitet (MAXDOP) för en fråga som har flaggats som en dålig utförande. Information om hur du konfigurerar den här åtgärden med hjälp av T-SQL finns i [Konfigurera konfigurations alternativet för MAXDOP-servern](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Om du anger MAXDOP till noll (0) som standardvärde anger du att databasen kan använda alla tillgängliga processor kärnor för att parallellisera trådar för att köra en enskild fråga. Ange MAXDOP till en (1) anger att endast en kärna kan användas för en enda fråga. I praktiska termer innebär detta att parallellitet är inaktiverat. Beroende på varje fall, tillgängliga kärnor i databasen och information om diagnostikinformation, kan du finjustera alternativet MAXDOP till antalet kärnor som används för parallell frågekörning och som kan lösa problemet i ditt fall.

## <a name="pagelatch-contention"></a>PAGELATCH-konkurrens

### <a name="what-is-happening"></a>Vad händer

Detta prestanda mönster indikerar den aktuella prestanda försämringen av databasens arbets belastning på grund av PAGELATCH-konkurrens jämfört med den senaste sju dagars arbets belastnings bas linjen.

Lås är lätta mekanismer för synkronisering som används för att aktivera multitrådning. De garanterar konsekvens för minnes intern strukturer som innehåller index, data sidor och andra interna strukturer.

Det finns många olika typer av lås. För enkelhetens skull används buffertutrymme för att skydda minnes sidor i bufferten. I/o-lås används för att skydda sidor som ännu inte har lästs in i bufferten. När data skrivs till eller läses från en sida i bufferten måste en arbets tråd först skaffa ett buffertutrymme för sidan. När en arbets tråd försöker komma åt en sida som inte redan är tillgänglig i poolen i minnet, görs en IO-begäran för att läsa in nödvändig information från lagrings platsen. Den här sekvensen av händelser visar en mer allvarlig form av prestanda försämring.

Konkurrens på sid låsen inträffar när flera trådar samtidigt försöker erhålla lås i samma minnes struktur, vilket ger en ökad vänte tid för att köra frågor. När det gäller PAGELATCH i/o-konkurrens om data behöver nås från lagringen är denna vänte tid ännu högre. Det kan påverka arbets belastnings prestanda avsevärt. PAGELATCH-konkurrens är det vanligaste scenariot för trådar som väntar på varandra och konkurrerar om resurser på flera processor system.

### <a name="troubleshooting"></a>Felsökning

Diagnostik-loggen visar information om PAGELATCH-innehåll. Du kan använda den här informationen som grund för fel sökning.

Eftersom en PAGELATCH är en intern kontroll mekanism bestämmer den automatiskt när de ska användas. Program beslut, inklusive schema design, kan påverka PAGELATCH-beteendet på grund av det deterministiska beteendet för lås.

En metod för att hantera spärr konkurrens är att ersätta en sekventiell index nyckel med en insekventiell nyckel för att jämnt distribuera infogningar i ett index intervall. Normalt distribuerar en inledande kolumn i index arbets belastningen proportionellt. En annan metod att överväga är tabell partitionering. Att skapa ett hash-partitionerings schema med en beräknad kolumn på en partitionerad tabell är ett gemensamt tillvägagångs sätt för att minimera överdriven spärr-konkurrens. Om du använder PAGELATCH i/o-konkurrens hjälper indexen att hjälpa till att minimera det här prestanda problemet.

Mer information finns i [diagnostisera och lösa låsnings konkurrens på SQL Server](http://databaser.net/moniwiki/pds/PerformanceTuning/SQLServerLatchContention.pdf) (PDF-nedladdning).

## <a name="missing-index"></a>Index saknas

### <a name="what-is-happening"></a>Vad händer

Detta prestanda mönster anger den aktuella prestanda försämringen för databasens arbets belastning jämfört med den senaste sju dagars bas linjen på grund av ett saknat index.

Ett index används för att påskynda prestandan för frågor. Den ger snabb åtkomst till tabell data genom att minska antalet data uppsättnings sidor som behöver besökas eller genomsökas.

Vissa frågor som orsakade försämrade prestanda identifieras genom den här identifieringen för vilken skapandet av index skulle bli fördelaktigt i prestandan.

### <a name="troubleshooting"></a>Felsökning

I diagnostikloggar visas frågans hashar för de frågor som identifierats för att påverka arbets Belastningens prestanda. Du kan bygga index för dessa frågor. Du kan också optimera eller ta bort dessa frågor om de inte behövs. En bra prestanda rutin är att undvika att fråga data som du inte använder.

> [!TIP]
> Visste du att inbyggd intelligens automatiskt kan hantera de bästa presterande indexen för dina databaser?
>
> För kontinuerlig prestanda optimering rekommenderar vi att du aktiverar [Automatisk justering](automatic-tuning-overview.md). Den här unika inbyggda intelligens-funktionen övervakar kontinuerligt din databas och justerar och skapar index för dina databaser automatiskt.
>

## <a name="new-query"></a>Ny fråga

### <a name="what-is-happening"></a>Vad händer

Detta prestanda mönster indikerar att en ny fråga identifieras som fungerar dåligt och som påverkar arbets belastnings prestandan jämfört med prestanda bas linjen för sju dagar.

Det kan ibland vara en utmaning att skriva en egen körnings fråga. Mer information om hur du skriver frågor finns i [skriva SQL-frågor](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)). Information om hur du optimerar befintliga frågor finns i [justering av frågor](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas information upp till två nya processor krävande frågor, inklusive deras fråge-hashar. Eftersom den identifierade frågan påverkar arbets belastnings prestandan kan du optimera din fråga. Bra tillvägagångs är att bara hämta de data som du behöver använda. Vi rekommenderar också att du använder frågor med en WHERE-sats. Vi rekommenderar också att du fören klar komplexa frågor och delar upp dem i mindre frågor. En annan bra idé är att dela upp stora batch-frågor i mindre batch-frågor. Introduktion av index för nya frågor är vanligt vis en bra idé att minimera det här prestanda problemet.

Överväg att använda [query Performance Insight](query-performance-insight-use.md)i Azure SQL Database.

## <a name="increased-wait-statistic"></a>Ökad vänte statistik

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret indikerar en försämring av arbets belastnings prestanda där dåliga frågor identifieras jämfört med den senaste sju dagars arbets belastnings bas linjen.

I det här fallet kan systemet inte klassificera de dåligt utförda frågorna under någon annan standard prestanda kategori, men det identifierade den väntande statistik som är ansvarig för regressionen. Därför anses det som frågor med *ökad wait-statistik*, där den väntande statistik som är ansvarig för regressionen också exponeras.

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas information om ökad information om vänte tid och fråga om hash-värden för de berörda frågorna.

Eftersom systemet inte kunde identifiera rotor saken för de dåliga frågorna är diagnostikinformation en bra utgångs punkt för manuell fel sökning. Du kan optimera prestanda för dessa frågor. En bra idé är att bara hämta de data du behöver använda och för att förenkla och dela upp komplexa frågor i mindre.

Mer information om hur du optimerar prestanda för frågor finns i [fråga justering](/previous-versions/sql/sql-server-2008-r2/ms176005(v=sql.105)).

## <a name="tempdb-contention"></a>TempDB-konkurrens

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret indikerar ett databas prestanda tillstånd där det finns en Flask hals för trådar som försöker komma åt tempDB-resurser. (Det här villkoret är inte i/o relaterat.) Det vanligaste scenariot för det här prestanda problemet är hundratals samtidiga frågor som alla skapar, använder och sedan tar bort små tempDB-tabeller. Systemet upptäckte att antalet samtidiga frågor som använder samma tempDB-tabeller ökade med tillräcklig statistisk betydelse för att påverka databasens prestanda jämfört med den senaste sju dagars prestanda bas linjen.

### <a name="troubleshooting"></a>Felsökning

I diagnostik-loggen visas information om tempDB-konkurrens. Du kan använda informationen som utgångs punkt för fel sökning. Det finns två saker du kan göra för att minska den här typen av konkurrens och öka data flödet för den övergripande arbets belastningen: du kan sluta använda de tillfälliga tabellerna. Du kan också använda minnesoptimerade tabeller.

Mer information finns i [Introduktion till minnesoptimerade tabeller](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables).

## <a name="elastic-pool-dtu-shortage"></a>DTU-underskott för elastisk pool

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret visar en försämring av den aktuella databasens arbets belastnings prestanda jämfört med den senaste sju dagars bas linjen. Det beror på brist på tillgänglig DTU: er i den elastiska poolen av din prenumeration.

[Azure Elastic pool-resurser](elastic-pool-overview.md) används som en pool av tillgängliga resurser som delas mellan flera databaser för skalnings användning. När tillgängliga eDTU-resurser i den elastiska poolen inte är tillräckligt stora för att stödja alla databaser i poolen, upptäcks prestanda problem i elastisk pool i systemet.

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas information om den elastiska poolen, en lista över de främsta DTU-databaserna och en procent andel av poolens DTU som används av databasen som förbrukas.

Eftersom det här prestanda tillståndet är relaterat till flera databaser med samma pool av eDTU: er i den elastiska poolen, fokuserar vi på fel söknings stegen på de översta DTU-förbrukade databaserna. Du kan minska arbets belastningen för de mest krävande databaserna, som innehåller optimering av de mest krävande frågorna på dessa databaser. Du kan också se till att du inte frågar efter data som du inte använder. En annan metod är att optimera program genom att använda de översta DTU-förbrukade databaserna och distribuera om arbets belastningen mellan flera databaser.

Om du inte kan göra en minskning och optimering av den aktuella arbets belastningen på dina främsta DTU-krävande databaser kan du överväga att öka pris nivån för den elastiska poolen. En sådan ökning resulterar i ökningen av tillgängliga DTU: er i den elastiska poolen.

## <a name="plan-regression"></a>Plan regression

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret anger ett villkor där databasen använder en under optimal fråga för att köra en fråga. Den optimala planen orsakar vanligt vis ökad frågekörningen, vilket leder till längre vänte tider för aktuella och andra frågor.

Databas motorn bestämmer körnings planen för frågor med minst kostnad för att köra frågor. När typen av frågor och arbets belastningar ändras, är ibland befintliga planer inte längre effektiva, eller så kanske databas motorn inte gjorde en bra utvärdering. Som en korrigerings fråga kan du utföra frågor om frågekörning manuellt.

Det här identifierade prestanda mönstret kombinerar tre olika fall av plan regression: ny plan regression, Gammal plan regression och befintliga planer har ändrats arbets belastning. Den specifika typen av plan regression som inträffat finns i egenskapen *information* i Diagnostic-loggen.

Det nya plan Regressions villkoret refererar till ett tillstånd där databas motorn börjar köra en ny frågeplan för frågekörningen som inte är lika effektiv som den gamla planen. Det gamla plan Regressions villkoret avser tillståndet när databas motorn växlar från att använda en ny, mer effektiv plan till den gamla planen, vilket inte är lika effektivt som den nya planen. De befintliga planerna ändrade arbets belastnings regressionen syftar på det tillstånd i vilket de gamla och nya planerna ständigt alternerar, med saldot som går mer mot den låga presterande planen.

Mer information om plan regressioner finns i [Vad är plan regression i SQL Server?](/archive/blogs/sqlserverstorageengine/what-is-plan-regression-in-sql-server).

### <a name="troubleshooting"></a>Felsökning

I Diagnostic-loggen visas frågans hash-värden, bra plan-ID, dåligt plan-ID och fråge-ID. Du kan använda den här informationen som grund för fel sökning.

Du kan analysera vilken plan som fungerar bättre för dina speciella frågor som du kan identifiera med de angivna hashvärdet. När du har fastställt vilka planer som fungerar bättre för dina frågor kan du framtvinga det manuellt.

Mer information finns i [Lär dig hur SQL Server förhindrar plan regressioner](/archive/blogs/sqlserverstorageengine/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions).

> [!TIP]
> Visste du att den inbyggda intelligens-funktionen kan hantera de bästa körnings planerna för dina databaser automatiskt?
>
> För kontinuerlig prestanda optimering rekommenderar vi att du aktiverar [Automatisk justering](automatic-tuning-overview.md). Den här inbyggda intelligensen övervakar kontinuerligt din databas och anpassar automatiskt och skapar bästa körnings planer för dina databaser.

## <a name="database-scoped-configuration-value-change"></a>Ändring av konfigurations värde för databas omfattning

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret indikerar ett villkor där en ändring i den databasbaserade konfigurationen orsakar prestanda regression som identifieras jämfört med den senaste sju dagars databasens arbets belastnings beteende. Det här mönstret anger att en nyligen utförd ändring i den databasbaserade konfigurationen inte verkar vara fördelaktig i databasens prestanda.

Konfigurations ändringar i databasen kan ställas in för varje enskild databas. Den här konfigurationen används i fall-för-fall-basis för att optimera den enskilda databasens prestanda. Följande alternativ kan konfigureras för varje enskild databas: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES och rensa PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Felsökning

I diagnostik-loggen visas de konfigurations ändringar som gjorts nyligen och som gjorde att prestanda försämringen jämförs med föregående sju dagars arbets belastnings beteende. Du kan återställa konfigurations ändringarna till föregående värden. Du kan också justera värde efter värde tills den önskade prestanda nivån har uppnåtts. Du kan kopiera konfigurations värden för databas omfattning från en liknande databas med tillfredsställande prestanda. Om du inte kan felsöka prestandan återställer du standardvärdena och försöker finjustera från den här bas linjen.

Mer information om hur du optimerar en databas-och T-SQL-syntax vid ändring av konfigurationen finns i [Alter Database-scoped Configuration (Transact-SQL)](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="slow-client"></a>Långsam klient

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret visar ett villkor där klienten som använder databasen inte kan använda utdata från databasen så snabbt som databasen skickar resultatet. Eftersom databasen inte lagrar resultat från de körda frågorna i en buffert saktas den ned och väntar på att klienten ska använda de utdata som skickats innan de fortsätter. Det här villkoret kan också vara relaterat till ett nätverk som inte är tillräckligt snabbt för att skicka utdata från databasen till klienten som konsumeras.

Det här villkoret skapas endast om en prestanda regression identifieras jämfört med den senaste sju dagars databasens arbets belastnings beteende. Det här prestanda problemet identifieras bara om en statistiskt märkbar prestanda försämring sker jämfört med tidigare prestanda beteende.

### <a name="troubleshooting"></a>Felsökning

Detta identifierade prestanda mönster indikerar ett villkor på klient sidan. Fel sökning krävs på klient sidans program eller på klient sidans nätverk. Diagnostik-loggen utvärderar frågans hashar och vänte tider som förefaller vara mest för klienten att använda dem inom de senaste två timmarna. Du kan använda den här informationen som grund för fel sökning.

Du kan optimera prestanda för ditt program för användning av dessa frågor. Du kan också överväga eventuella problem med nätverks fördröjning. Eftersom problemet med prestanda försämringen baseras på en ändring under den senaste sju dagars prestanda bas linjen kan du undersöka om de senaste ändringarna i program-eller nätverks tillståndet orsakade denna prestanda Regressions händelse.

## <a name="pricing-tier-downgrade"></a>Nedgradering av pris nivå

### <a name="what-is-happening"></a>Vad händer

Det här identifierade prestanda mönstret anger ett villkor där pris nivån för din databas prenumeration har degraderats. På grund av en minskning av resurser (DTU: er) som är tillgängliga för databasen, upptäckte systemet en minskning av den aktuella databas prestandan jämfört med den senaste sju dagars bas linjen.

Dessutom kan det finnas ett villkor där pris nivån för din databas prenumeration har degraderats och sedan uppgraderats till en högre nivå under en kort tids period. Identifiering av den här tillfälliga prestanda försämringen visas i avsnittet information i Diagnostic-loggen som en degradering och uppgradering av pris nivån.

### <a name="troubleshooting"></a>Felsökning

Om du har minskat pris nivån och därför DTU: er är tillgänglig och du är nöjd med prestandan behöver du inte göra något. Om du har minskat pris nivån och du inte är nöjd med databasens prestanda minskar du databasens arbets belastningar eller så kan du öka pris nivån till en högre nivå.

## <a name="recommended-troubleshooting-flow"></a>Rekommenderat fel söknings flöde

 Följ flödesschemat för en rekommenderad metod för att felsöka prestanda problem med hjälp av Intelligent Insights.

Kom åt Intelligent Insights via Azure Portal genom att gå till Azure SQL-analys. Försök att hitta den inkommande prestanda aviseringen och välj den. Identifiera vad som händer på sidan identifieringar. Observera den tillhandahållna rotor Saks analysen av problemet, frågetext, fråge tids trender och incident utveckling. Försök att lösa problemet med hjälp av Intelligent Insights rekommendation för att minimera prestanda problemet.

[![Fel sökning av flödes diagram](./media/intelligent-insights-troubleshoot-performance/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Välj flödesschemat för att ladda ned en PDF-version.

Intelligent Insights behöver vanligt vis en timmes tid för att utföra rotor Saks analysen av prestanda problemet. Om du inte kan hitta ditt problem i Intelligent Insights och det är viktigt för dig, kan du använda Frågearkivet för att manuellt identifiera rotor saken till prestanda problemet. (Vanligt vis är de här problemen mindre än en timme gammal.) Mer information finns i [övervaka prestanda med hjälp av Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store).

## <a name="next-steps"></a>Nästa steg

- Lär dig [intelligent Insights](intelligent-insights-overview.md) begrepp.
- Använd [loggen intelligent Insights prestanda diagnos](intelligent-insights-use-diagnostics-log.md).
- Övervaka med hjälp av [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
- Lär dig att [samla in och använda loggdata från dina Azure-resurser](../../azure-monitor/platform/platform-logs-overview.md).