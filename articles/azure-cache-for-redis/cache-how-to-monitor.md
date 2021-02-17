---
title: Övervaka Azure cache för Redis
description: Lär dig hur du övervakar hälsa och prestanda för Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0ff11c9601fb55e27d8780185d77c177e9d9201b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584631"
---
# <a name="monitor-azure-cache-for-redis"></a>Övervaka Azure cache för Redis

Azure cache för Redis använder [Azure Monitor](../azure-monitor/index.yml) för att tillhandahålla flera alternativ för att övervaka dina cache-instanser. Du kan visa mått, fästa mått diagram på Start sidan, anpassa datum-och tidsintervallet för övervaknings diagram, lägga till och ta bort mått från diagrammen och ange aviseringar när vissa villkor uppfylls. Med de här verktygen kan du övervaka Azure-cachens hälso tillstånd för Redis-instanser och hjälpa dig att hantera dina cachelagring-program.

Mått för Azure cache för Redis-instanser samlas in med hjälp av kommandot Redis [information](https://redis.io/commands/info) cirka två gånger per minut och lagras automatiskt i 30 dagar (se [Exportera cache-mått](#export-cache-metrics) för att konfigurera en annan bevarande princip) så att de kan visas i mått diagram och utvärderas av varnings regler. Mer information om de olika informations värden som används för varje cache-mått finns i [tillgängliga mått och rapporterings intervall](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Om du vill visa cache-mått [bläddrar](cache-configure.md#configure-azure-cache-for-redis-settings) du till din cache-instans i [Azure Portal](https://portal.azure.com).  Azure cache för Redis innehåller några inbyggda diagram på bladet **Översikt** och **Redis mått** bladet. Varje diagram kan anpassas genom att lägga till eller ta bort mått och ändra rapporterings intervallet.

![Sex diagram visas. En av dem är cache-träffar och Cachemissar den senaste timmen.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Visa förkonfigurerade mått diagram

**Översikts** bladet har följande förkonfigurerade övervaknings diagram.

* [Övervaknings diagram](#monitoring-charts)
* [Användnings diagram](#usage-charts)

### <a name="monitoring-charts"></a>Övervaknings diagram

Avsnittet **övervakning** i bladet **Översikt** har **träffar och har träffar**, **hämtar och ställer in**, **anslutningar** och **Totalt antal kommando** diagram.

![Övervaknings diagram](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Användnings diagram

**Användnings** avsnittet på bladet **Översikt** har **redis server belastning**, **minnes användning**, **nätverks bandbredd** och **CPU-användning** och visar även **pris nivån** för cache-instansen.

![Användnings diagram](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Pris** nivån visar cache-pris nivån och kan användas för att [skala](cache-how-to-scale.md) cachen till en annan pris nivå.

## <a name="view-metrics-with-azure-monitor"></a>Visa mått med Azure Monitor

Om du vill visa Redis-mått och skapa anpassade diagram med Azure Monitor klickar du på **mått** på **resurs-menyn** och anpassar ditt diagram med önskade mått, rapporterings intervall, diagram typ med mera.

![I det vänstra navigerings fönstret i contoso55 är måtten ett alternativ under övervakning och är markerat. På mått finns det en lista över mått. Cacheträffar och Cachemissar har valts.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Mer information om hur du arbetar med mått med Azure Monitor finns i [Översikt över mått i Microsoft Azure](../azure-monitor/data-platform.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportera cache-mått

Som standard lagras cache-mått i Azure Monitor i [30 dagar](../azure-monitor/essentials/data-platform-metrics.md) och tas sedan bort. Om du vill spara dina cache-mått i mer än 30 dagar kan du ange [ett lagrings konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) och ange en princip för **bevarande (dagar)** för dina cache-mått. 

Så här konfigurerar du ett lagrings konto för dina cache-mått:

1. På sidan **Azure cache för Redis** , under **övervaknings** rubriken väljer du **diagnostik**.
2. Välj **+ Lägg till diagnostisk inställning**.
3. Ge inställningarna ett namn.
4. Markera **Arkivera till ett lagringskonto**. Du debiteras normala data avgifter för lagring och transaktioner när du skickar diagnostik till ett lagrings konto.
4. Välj **Konfigurera** för att välja det lagrings konto där du vill lagra cache-måtten.
5. Under tabell rubrikens **mått** markerar du kryss rutan bredvid de rad objekt som du vill lagra, till exempel **AllMetrics**. Ange en princip för **bevarande (dagar)** . Det maximala antalet dagars kvarhållning du kan ange är **365 dagar**. Men om du vill behålla mått data permanent ställer du in **kvarhållning (dagar)** på **0**.
6. Klicka på **Spara**.


![Redis-diagnostik](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Förutom att arkivera dina cache-mått till lagring kan du också [strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Du kan få åtkomst till dina mått genom att visa dem i Azure Portal som tidigare beskrivits i den här artikeln och du kan också komma åt dem med hjälp av [Azure Monitor mått REST API](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Om du ändrar lagrings konton är data i det tidigare konfigurerade lagrings kontot fortfarande tillgängliga för nedladdning, men det visas inte i Azure Portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Tillgängliga mått och rapporterings intervall

Cache-mått rapporteras med hjälp av flera rapport intervall, inklusive den **senaste timmen**, **idag**, **senaste veckan** och **anpassad**. **Mått** bladet för varje mått diagram visar medelvärde, lägsta och högsta värden för varje mått i diagrammet, och vissa mått visar total summan för rapporterings intervallet. 

Varje mått innehåller två versioner. Ett mått mäter prestanda för hela cacheminnet och för cacheminnen som använder [kluster](cache-how-to-premium-clustering.md), en andra version av måttet som inkluderar `(Shard 0-9)` i namnet mäter prestanda för en enskild Shard i en cache. Om till exempel en cache har fyra Shards, `Cache Hits` är det totala antalet träffar för hela cacheminnet och `Cache Hits (Shard 3)` är bara träffarna för Shard i cacheminnet.

> [!NOTE]
> Även om cachen är inaktiv utan anslutna aktiva klient program kan du se viss cache-aktivitet, till exempel anslutna klienter, minnes användning och åtgärder som utförs. Den här aktiviteten är normal under driften av en Azure-cache för Redis-instans.
> 
> 

| Metric | Beskrivning |
| --- | --- |
| Cacheträffar |Antalet lyckade nyckels ökningar under det angivna rapporterings intervallet. Det här numret mappar till `keyspace_hits` från Redis [information](https://redis.io/commands/info) -kommandot. |
| Fördröjning för cache (för hands version) | Svars tiden för cachen beräknas utifrån mellannodens svars tid för cachen. Det här måttet mäts i mikrosekunder och har tre dimensioner: `Avg` , `Min` och `Max` , som representerar genomsnitt, minsta och högsta svars tid för cachen under det angivna rapporterings intervallet. |
| Cachemissar |Antalet misslyckade nyckels ökningar under det angivna rapporterings intervallet. Det här numret mappar till `keyspace_misses` från REDIS information-kommandot. Missar i cacheminnet innebär inte nödvändigt vis att det finns ett problem med cachen. Till exempel, när du använder programmerings mönstret för cacheminnet, ser ett program först i cachen för ett objekt. Om objektet inte är där (cache saknas) hämtas objektet från databasen och läggs till i cacheminnet för nästa gången. Cachemissar är normalt beteendet för programmerings mönstret cache-undan. Om antalet Cachemissar är högre än förväntat granskar du program logiken som fylls i och läser från cachen. Om objekt tas bort från cachen på grund av minnes belastning kan det finnas vissa Cachemissar, men ett bättre mått för att övervaka minnes belastningen är `Used Memory` eller `Evicted Keys` . |
| Cacheläsning |Mängden data som läses från cachen i megabyte per sekund (MB/s) under det angivna rapporterings intervallet. Det här värdet härleds från de nätverkskort som har stöd för den virtuella datorn som är värd för cachen och som inte är Redis. **Det här värdet motsvarar den nätverks bandbredd som används av denna cache. Om du vill konfigurera aviseringar för nätverks bandbredds gränser på Server sidan skapar du den med hjälp av den här `Cache Read` räknaren. Se [den här tabellen](cache-planning-faq.md#azure-cache-for-redis-performance) för de observerade bandbredds gränserna för olika cache-pris nivåer och storlekar.** |
| Cacheskrivning |Mängden data som skrivs till cachen i megabyte per sekund (MB/s) under det angivna rapporterings intervallet. Det här värdet härleds från de nätverkskort som har stöd för den virtuella datorn som är värd för cachen och som inte är Redis. Det här värdet motsvarar nätverks bandbredden för data som skickas till cacheminnet från klienten. |
| Anslutna klienter |Antalet klient anslutningar till cacheminnet under det angivna rapporterings intervallet. Det här numret mappar till `connected_clients` från REDIS information-kommandot. När [anslutnings gränsen](cache-configure.md#default-redis-server-configuration) har uppnåtts kommer efterföljande anslutnings försök till cacheminnet att Miss lyckas. Även om det inte finns några aktiva klient program kan det fortfarande finnas några instanser av anslutna klienter på grund av interna processer och anslutningar. |
| Processor |CPU-användningen av Azure-cachen för Redis-servern som en procent andel under det angivna rapporterings intervallet. Det här värdet mappar till operativ systemets `\Processor(_Total)\% Processor Time` prestanda räknare. |
| Fel | Specifika fel och prestanda problem som cachen kan ha under ett angivet rapporterings intervall. Det här måttet har åtta dimensioner som representerar olika fel typer, men kan ha fler tillägg i framtiden. De fel typer som visas nu är följande: <br/><ul><li>**Redundans** – när en cache växlar över (underordnade befordras till primär)</li><li>**DataLoss** – när det finns data förlust i cacheminnet</li><li>**UnresponsiveClients** – när klienterna inte läser data från servern tillräckligt snabbt</li><li>**AOF** – när det finns ett problem relaterat till AOF persistence</li><li>**RDB** – när det finns ett problem relaterat till RDB persistence</li><li>**Importera** – när det finns ett problem som rör import RDB</li><li>**Exportera** – när det finns ett problem som rör export RDB</li></ul> |
| Avlägsnade nycklar |Antalet objekt som tagits bort från cacheminnet under det angivna rapporterings intervallet på grund av `maxmemory` gränsen. Det här numret mappar till `evicted_keys` från REDIS information-kommandot. |
| Utgångna nycklar |Antalet objekt som har upphört att gälla från cachen under det angivna rapporterings intervallet. Det här värdet mappar till `expired_keys` från REDIS information-kommandot.|
| Hämtningar |Antalet get-åtgärder från cachen under det angivna rapporterings intervallet. Det här värdet är summan av följande värden från Redis-information alla kommando:,,,,,, och `cmdstat_get` `cmdstat_hget` `cmdstat_hgetall` `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit` `cmdstat_getrange` motsvarar summan av cacheträffar och missar under rapporterings intervallet. |
| Åtgärder per sekund | Det totala antalet kommandon som bearbetas per sekund av cache-servern under det angivna rapporterings intervallet.  Det här värdet mappar till "instantaneous_ops_per_sec" från kommandot Redis INFO. |
| Redis server-belastning |Procent andelen cykler där Redis-servern är upptagen och väntar inte på inaktivitet för meddelanden. Om den här räknaren når 100 innebär det att Redis-servern har uppnått ett prestanda tak och att processorn inte kan bearbeta arbetet snabbare. Om du ser hög redis server belastning visas timeout-undantag i klienten. I detta fall bör du överväga att skala upp eller partitionera data i flera cacheminnen. |
| Uppsättningar |Antalet set-åtgärder som ska cachelagras under det angivna rapporterings intervallet. Det här värdet är summan av följande värden från Redis information all-kommando:,,,,,,,,, `cmdstat_set` `cmdstat_hset` `cmdstat_hmset` `cmdstat_hsetnx` `cmdstat_lset` `cmdstat_mset` `cmdstat_msetnx` `cmdstat_setbit` `cmdstat_setex` `cmdstat_setrange` och `cmdstat_setnx` . |
| Totalt antal nycklar  | Det maximala antalet nycklar i cacheminnet under den förflutna tids perioden för rapportering. Det här numret mappar till `keyspace` från REDIS information-kommandot. På grund av en begränsning i det underliggande mått systemet, returnerar totalt antal nycklar det maximala antalet nycklar för Shard som hade det maximala antalet nycklar under rapporterings intervallet.  |
| Totalt antal åtgärder |Det totala antalet kommandon som bearbetas av cache-servern under det angivna rapporterings intervallet. Det här värdet mappar till `total_commands_processed` från REDIS information-kommandot. När Azure cache för Redis används rent för pub/sub, kommer det inte att finnas några mått för `Cache Hits` , `Cache Misses` ,, `Gets` eller `Sets` , men det kommer att finnas `Total Operations` mått som återspeglar cache-användningen för pub/sub-åtgärder. |
| Använt minne |Mängden cache-minne som används för nyckel/värde-par i cacheminnet i MB under det angivna rapporterings intervallet. Det här värdet mappar till `used_memory` från REDIS information-kommandot. Det här värdet inkluderar inte metadata eller fragmentering. |
| Använt minne i procent | % Av den totala mängden minne som används under det angivna rapporterings intervallet.  Värdet refererar till `used_memory` värdet från kommandot REDIS information för att beräkna procent andelen. |
| RSS för använt minne |Mängden cache-minne som används i MB under det angivna rapporterings intervallet, inklusive fragmentering och metadata. Det här värdet mappar till `used_memory_rss` från REDIS information-kommandot. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Aviseringar

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitetsloggar. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Om du vill konfigurera aviserings regler för din cache klickar du på **varnings regler** på **resurs menyn**.

![Övervakning](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Mer information om hur du konfigurerar och använder aviseringar finns i [Översikt över aviseringar](../azure-monitor/alerts/alerts-classic-portal.md).

## <a name="activity-logs"></a>Aktivitetsloggar
Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure-cachen för Redis-instanser. Det kallades tidigare för "gransknings loggar" eller "drift loggar". Med aktivitets loggar kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, posta och ta bort) som utförs på Azure-cachen för Redis-instanser. 

> [!NOTE]
> Aktivitets loggar innehåller inte Läs åtgärder (GET).
>

Om du vill visa aktivitets loggar för cacheminnet klickar du på **aktivitets loggar** på **resurs menyn**.

Mer information om aktivitets loggar finns i [Översikt över Azure aktivitets loggen](../azure-monitor/essentials/platform-logs-overview.md).