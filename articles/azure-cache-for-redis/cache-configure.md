---
title: Så här konfigurerar du Azure Cache for Redis
description: Förstå Redis-standardkonfigurationen för Azure Cache for Redis och lär dig hur du konfigurerar dina Azure Cache for Redis instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ffcaf7fc50a310cdd4773868c62bbbb801619e3c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833156"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Så här konfigurerar du Azure Cache for Redis
Det här avsnittet beskriver de konfigurationer som är tillgängliga för Azure Cache for Redis instanser. Det här avsnittet beskriver även Redis-standardserverkonfigurationen för Azure Cache for Redis instanser.

> [!NOTE]
> Mer information om hur du konfigurerar och använder premium-cachefunktioner finns i Så här konfigurerar du beständighet [,](cache-how-to-premium-persistence.md)Så här konfigurerar du klustring [och](cache-how-to-premium-clustering.md)Så här [konfigurerar](cache-how-to-premium-vnet.md)du Virtual Network stöd för .
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurera Azure Cache for Redis inställningar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis visas och konfigureras på bladet **Azure Cache for Redis** med hjälp av **resursmenyn**.

![Azure Cache for Redis inställningar](./media/cache-configure/redis-cache-settings.png)

Du kan visa och konfigurera följande inställningar med hjälp av **resursmenyn**.

* [Översikt](#overview)
* [Aktivitetslogg](#activity-log)
* [Åtkomstkontroll (IAM)](#access-control-iam)
* [Taggar](#tags)
* [Diagnostisera och lösa problem](#diagnose-and-solve-problems)
* [Inställningar](#settings)
    * [Åtkomstnycklar](#access-keys)
    * [Avancerade inställningar](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
    * [Skalning](#scale)
    * [Klusterstorlek](#cluster-size)
    * [Datapersistence](#redis-data-persistence)
    * [Schemauppdateringar](#schedule-updates)
    * [Geo-replikering](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Brandvägg](#firewall)
    * [Egenskaper](#properties)
    * [Lås](#locks)
    * [Automationsskript](#automation-script)
* Administration
    * [Importera data](#importexport)
    * [Exportera data](#importexport)
    * [Starta om](#reboot)
* [Övervakning](#monitoring)
    * [Redis-mått](#redis-metrics)
    * [Aviseringsregler](#alert-rules)
    * [Diagnostik](#diagnostics)
* Stöd & felsökningsinställningar
    * [Resurshälsa](#resource-health)
    * [Ny supportbegäran](#new-support-request)


## <a name="overview"></a>Översikt

**Översikt** ger dig grundläggande information om din cache, till exempel namn, portar, prisnivå och valda cachemått.

### <a name="activity-log"></a>Aktivitetslogg

Klicka **på Aktivitetslogg** för att visa åtgärder som utförts i cacheminnet. Du kan också använda filtrering för att expandera den här vyn så att den innehåller andra resurser. Mer information om hur du arbetar med granskningsloggar finns [i Granskningsåtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md). Mer information om hur du övervakar Azure Cache for Redis händelser finns i [Åtgärder och aviseringar.](cache-how-to-monitor.md#operations-and-alerts)

### <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

Avsnittet **Åtkomstkontroll (IAM)** innehåller stöd för rollbaserad åtkomstkontroll i Azure (Azure RBAC) i Azure Portal. Den här konfigurationen hjälper organisationer att uppfylla sina åtkomsthanteringskrav enkelt och exakt. Mer information finns i [Rollbaserad åtkomstkontroll i Azure i Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Taggar

Avsnittet **Taggar** hjälper dig att organisera dina resurser. Mer information finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostisera och lösa problem

Klicka **på Diagnostisera och lösa problem** för att få vanliga problem och strategier för att lösa dem.



## <a name="settings"></a>Inställningar
I **avsnittet** Inställningar kan du komma åt och konfigurera följande inställningar för din cache.

* [Åtkomstnycklar](#access-keys)
* [Avancerade inställningar](#advanced-settings)
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
* [Skalning](#scale)
* [Klusterstorlek](#cluster-size)
* [Datapersistence](#redis-data-persistence)
* [Schemauppdateringar](#schedule-updates)
* [Geo-replikering](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brandvägg](#firewall)
* [Egenskaper](#properties)
* [Lås](#locks)
* [Automationsskript](#automation-script)



### <a name="access-keys"></a>Åtkomstnycklar
Klicka **på Åtkomstnycklar** för att visa eller återskapa åtkomstnycklarna för ditt cacheminne. Dessa nycklar används av klienterna som ansluter till ditt cacheminne.

![Azure Cache for Redis åtkomstnycklar](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Avancerade inställningar
Följande inställningar konfigureras på bladet **Avancerade** inställningar.

* [Åtkomstportar](#access-ports)
* [Minnespolicyer](#memory-policies)
* [Keyspace-meddelanden (avancerade inställningar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Åtkomstportar
Som standard är icke-TLS-/SSL-åtkomst inaktiverad för nya cacheminnen. Om du vill aktivera icke-TLS-porten klickar du på Nej för Tillåt endast åtkomst **via SSL på** **bladet Avancerade** inställningar och klickar sedan på **Spara.** 

> [!NOTE]
> TLS-åtkomst till Azure Cache for Redis stöder för närvarande TLS 1.0, 1.1 och 1.2, men versionerna 1.0 och 1.1 dras snart tillbaka.  Mer information finns [på sidan Ta bort TLS 1.0 och 1.1.](cache-remove-tls-10-11.md)

![Azure Cache for Redis åtkomstportar](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Minnesprinciper
**Maxmemory-principen,** **maxmemory-reserved** och **maxfragmentationmemory-reserved**  på bladet Avancerade inställningar konfigurerar minnesprinciperna för cacheminnet.

![Azure Cache for Redis Maxmemory-princip](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory-principen** konfigurerar avlägsningsprincipen för cachen och gör att du kan välja bland följande avlägsningsprinciper:

* `volatile-lru` – Det här är standardprincipen för avlägsning.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Mer information om principer `maxmemory` finns i [Avlägsningsprinciper.](https://redis.io/topics/lru-cache#eviction-policies)

Inställningen **maxmemory-reserved** konfigurerar mängden minne i MB per instans i ett kluster som är reserverat för icke-cacheåtgärder, till exempel replikering under redundans. Om du anger det här värdet får du en mer konsekvent Redis-serverupplevelse när belastningen varierar. Det här värdet ska anges högre för arbetsbelastningar som är skrivintensiva. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

Inställningen **maxfragmentationmemory-reserved** konfigurerar mängden minne i MB per instans i ett kluster som är reserverat för minnesfragmentering. Om du anger det här värdet får du en mer konsekvent Redis-serverupplevelse när cachen är full eller nära full och fragmenteringsförhållandet är högt. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

En sak att tänka på när du väljer ett nytt värde för minnesreservation **(maxmemory-reserved** eller **maxfragmentationmemory-reserved)** är hur den här ändringen kan påverka ett cacheminne som redan körs med stora mängder data i den. Om du till exempel har en 53 GB cache med 49 GB data och sedan ändrar reservationsvärdet till 8 GB, kommer den här ändringen att minska det maximala tillgängliga minnet för systemet till 45 GB. Om dina aktuella värden eller dina värden är högre än den nya gränsen på 45 GB måste systemet avlägsna data tills båda och är `used_memory` `used_memory_rss` under `used_memory` `used_memory_rss` 45 GB. Avlägsning kan öka serverbelastningen och minnesfragmenteringen. Mer information om cachemått som och finns `used_memory` i Tillgängliga mått och `used_memory_rss` [rapporteringsintervall.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)

> [!IMPORTANT]
> Inställningarna **maxmemory-reserved** och **maxfragmentationmemory-reserved** är endast tillgängliga för Standard- och Premium-cacheminnen.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meddelanden (avancerade inställningar)
Redis-nyckelutrymmesmeddelanden konfigureras på **bladet Avancerade** inställningar. Med keyspace-meddelanden kan klienter ta emot meddelanden när vissa händelser inträffar.

![Azure Cache for Redis avancerade inställningar](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Meddelanden om nyckelutrymme och **inställningen notify-keyspace-events** är endast tillgängliga för Standard- och Premium-cacheminnen.
>
>

Mer information finns i [Redis Keyspace Notifications](https://redis.io/topics/notifications). Exempelkod finns i filen [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) i [Hello world-exemplet.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis Advisor
Bladet **Azure Cache for Redis Advisor** visar rekommendationer för ditt cacheminne. Under normal drift visas inga rekommendationer.

![Skärmbild som visar var rekommendationerna visas.](./media/cache-configure/redis-cache-no-recommendations.png)

Om det uppstår några villkor under driften av cacheminnet, till exempel hög minnesanvändning, nätverksbandbredd eller serverbelastning, visas en avisering **på Azure Cache for Redis** bladet.

![Skärmbild som visar var aviseringarna visas i Azure Cache for Redis avsnitt.](./media/cache-configure/redis-cache-recommendations-alert.png)

Mer information finns på **bladet Rekommendationer.**

![Rekommendationer](./media/cache-configure/redis-cache-recommendations.png)

Du kan övervaka dessa mått i [avsnitten Övervakningsdiagram](cache-how-to-monitor.md#monitoring-charts) [och Användningsdiagram](cache-how-to-monitor.md#usage-charts) på **Azure Cache for Redis** bladet.

Varje prisnivå har olika gränser för klientanslutningar, minne och bandbredd. Om ditt cacheminne närmar sig maximal kapacitet för dessa mått under en längre tid skapas en rekommendation. Mer information om mått och gränser som granskas av verktyget **Rekommendationer** finns i följande tabell:

| Azure Cache for Redis mått | Mer information |
| --- | --- |
| Användning av nätverksbandbredd |[Cacheprestanda – tillgänglig bandbredd](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Anslutna klienter |[Redis-standardserverkonfiguration – maxclients](#maxclients) |
| Serverbelastning |[Användningsdiagram – Redis-serverbelastning](cache-how-to-monitor.md#usage-charts) |
| Minnesanvändning |[Cacheprestanda – storlek](cache-planning-faq.md#azure-cache-for-redis-performance) |

Om du vill uppgradera din cache klickar **du på Uppgradera** nu för att ändra prisnivån och [skala](#scale) din cache. Mer information om hur du väljer en prisnivå finns [i Välja rätt nivå](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Skala
Klicka **på** Skala för att visa eller ändra prisnivån för ditt cacheminne. Mer information om skalning finns i Skala [Azure Cache for Redis](cache-how-to-scale.md).

![Azure Cache for Redis prisnivå](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-klusterstorlek
Klicka **på Klusterstorlek** för att ändra klusterstorleken för en premiumcache som körs med klustring aktiverat.

![Klusterstorlek](./media/cache-configure/redis-cache-redis-cluster-size.png)

Om du vill ändra klusterstorleken använder du skjutreglaget eller skriver ett tal mellan 1 och 10 i **textrutan Shard count (Antal shards)** och klickar på **OK** för att spara.

> [!IMPORTANT]
> Redis-klustring är endast tillgängligt för Premium-cacheminnen. Mer information finns i Så [här konfigurerar du klustring för en Premium-Azure Cache for Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Redis-datapersistens
Klicka **på Datapersistence** för att aktivera, inaktivera eller konfigurera datapersistence för premiumcachen. Azure Cache for Redis erbjuder Redis-beständighet med antingen RDB-beständighet eller AOF-beständighet.

Mer information finns i Så [här konfigurerar du beständighet för en Premium-Azure Cache for Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-datapersistence är endast tillgängligt för Premium-cacher.
>
>

### <a name="schedule-updates"></a>Schemauppdateringar
På **bladet Schemalägg** uppdateringar kan du ange en underhållsfönstret för Redis-serveruppdateringar för ditt cacheminne.

> [!IMPORTANT]
> Underhållsfönstret gäller endast redis-serveruppdateringar och inte azure-uppdateringar eller uppdateringar av operativsystemet för de virtuella datorer som är värdar för cachen.
>
>

![Schemauppdateringar](./media/cache-configure/redis-schedule-updates.png)

Om du vill ange en underhållstid kontrollerar du de önskade dagarna och anger underhållsfönstrets starttid för varje dag och klickar på **OK.** Underhållsfönstrets tid är i UTC.

Mer information och instruktioner finns i [Azure Cache for Redis administration – Schemalägg uppdateringar](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Geo-replikering

Bladet Geo-replikering innehåller en mekanism för att länka två **Premium-Azure Cache for Redis** instanser. En cache anges som den primära länkade cachen och den andra som sekundär länkad cache. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera ett cacheminne mellan Azure-regioner.

> [!IMPORTANT]
> **Geo-replikering** är endast tillgängligt för cacheminnen på Premium-nivå. Mer information och instruktioner finns i [Så här konfigurerar du geo-replikering för Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
I **Virtual Network** här avsnittet kan du konfigurera inställningarna för virtuella nätverk för ditt cacheminne. Information om hur du skapar en Premium-cache med VNET-stöd och uppdaterar dess inställningar finns i Så här konfigurerar du [Virtual Network för en Premium-Azure Cache for Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Inställningar för virtuella nätverk är endast tillgängliga för premium-cacheminnen som konfigurerades med VNET-stöd när cachen skapades.
>
>

### <a name="firewall"></a>Brandvägg

Konfiguration av brandväggsregler är tillgängligt för alla Azure Cache for Redis nivåer.

Klicka **på Brandvägg** för att visa och konfigurera brandväggsregler för cachelagring.

![Brandvägg](./media/cache-configure/redis-firewall-rules.png)

Du kan ange brandväggsregler med ett start- och slut-IP-adressintervall. När brandväggsregler har konfigurerats kan endast klientanslutningar från de angivna IP-adressintervallen ansluta till cachen. När en brandväggsregel sparas finns det en kort fördröjning innan regeln börjar gälla. Den här fördröjningen är vanligtvis mindre än en minut.

> [!IMPORTANT]
> Anslutningar från Azure Cache for Redis-övervakningssystem tillåts alltid, även om brandväggsregler har konfigurerats.
>
>

### <a name="properties"></a>Egenskaper
Klicka **på Egenskaper** för att visa information om ditt cacheminne, inklusive cacheslutpunkt och portar.

![Azure Cache for Redis egenskaper](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Lås
I **avsnittet** Lås kan du låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i din organisation oavsiktligt tar bort eller ändrar viktiga resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automationsskript

Klicka **på Automation-skript** för att skapa och exportera en mall för dina distribuerade resurser för framtida distributioner. Mer information om hur du arbetar med mallar finns [i Distribuera resurser med Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Administrationsinställningar
Med inställningarna i **avsnittet** Administration kan du utföra följande administrativa uppgifter för din cache.

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importera data](#importexport)
* [Exportera data](#importexport)
* [Starta om](#reboot)


### <a name="importexport"></a>Import/Export
Import/Export är en Azure Cache for Redis-datahanteringsåtgärd som gör att du kan importera och exportera data i cacheminnet genom att importera och exportera en ögonblicksbild av Azure Cache for Redis Database (RDB) från en Premium-cache till en sidblob i ett Azure Storage-konto. Med Import/Export kan du migrera mellan Azure Cache for Redis instanser eller fylla i cachen med data före användning.

Import kan användas för att ta Redis-kompatibla RDB-filer från valfri Redis-server som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa ett cacheminne med förifyllda data. Under importen läser Azure Cache for Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cacheminnet.

Med Exportera kan du exportera data som lagras i Azure Cache for Redis Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella dator som är värd för Azure Cache for Redis-serverinstansen och filen laddas upp till det angivna lagringskontot. När exportåtgärden har slutförts med statusen lyckades eller misslyckades tas den tillfälliga filen bort.

> [!IMPORTANT]
> Import/Export är endast tillgängligt för cacheminnen på Premium-nivå. Mer information och instruktioner finns i [Importera och exportera data i Azure Cache for Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Starta om
På **bladet** Starta om kan du starta om noderna i cacheminnet. Med den här omstartsfunktionerna kan du testa programmet för återhämtning om det uppstår ett fel i en cachenod.

![Starta om](./media/cache-configure/redis-cache-reboot.png)

Om du har en premium-cache med klustring aktiverat kan du välja vilka fragment av cacheminnet som ska startas om.

![Skärmbild som visar var du väljer vilka shards i cacheminnet som ska startas om.](./media/cache-configure/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet väljer du önskade noder och klickar på **Starta om.** Om du har en premium-cache med klustring aktiverat väljer du de fragment som ska startas om och klickar sedan på **Starta om.** Efter några minuter startar de valda nodarna om och är online igen några minuter senare.

> [!IMPORTANT]
> Omstart är nu tillgängligt för alla prisnivåer. Mer information och instruktioner finns i Azure Cache for Redis [administration – Starta om](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Övervakning

I **avsnittet** Övervakning kan du konfigurera diagnostik och övervakning för Azure Cache for Redis.
Mer information om hur Azure Cache for Redis övervakning och diagnostik finns i [Så här övervakar du Azure Cache for Redis](cache-how-to-monitor.md).

![Diagnostik](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-mått](#redis-metrics)
* [Aviseringsregler](#alert-rules)
* [Diagnostik](#diagnostics)

### <a name="redis-metrics"></a>Redis-mått
Klicka **på Redis-mått** för [att visa mått](cache-how-to-monitor.md#view-cache-metrics) för ditt cacheminne.

### <a name="alert-rules"></a>Aviseringsregler

Klicka **på Aviseringsregler** för att konfigurera aviseringar baserat Azure Cache for Redis mått. Mer information finns i [Aviseringar.](cache-how-to-monitor.md#alerts)

### <a name="diagnostics"></a>Diagnostik

Som standard lagras cachemått i Azure Monitor [i 30 dagar och](../azure-monitor/essentials/data-platform-metrics.md) tas sedan bort. Om du vill bevara dina cachemått i mer än 30 dagar klickar du på **Diagnostik för** att konfigurera det [lagringskonto som används](cache-how-to-monitor.md#export-cache-metrics) för att lagra cachediagnostik.

>[!NOTE]
>Förutom att arkivera dina cachemått till lagring kan du även strömma dem till en [händelsehubb](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)eller skicka dem till Azure Monitor loggar .
>
>

## <a name="support--troubleshooting-settings"></a>Stöd & felsökningsinställningar
Inställningarna i avsnittet **Support + felsökning** ger dig alternativ för att lösa problem med din cache.

![Support och felsökning](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resurshälsa](#resource-health)
* [Ny supportbegäran](#new-support-request)

### <a name="resource-health"></a>Resurshälsa
**Resource Health** bevakar resursen och meddelar dig om den körs som förväntat. Mer information om Azure Resource Health-tjänsten finns i [Översikt över Azure Resource Health.](../service-health/resource-health-overview.md)

> [!NOTE]
> Resource Health kan för närvarande inte rapportera om hälsotillståndet för Azure Cache for Redis instanser som finns i ett virtuellt nätverk. Mer information finns i Fungerar [alla cachefunktioner när du är värd för en cache i ett VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Ny supportbegäran
Klicka **på Ny supportbegäran** för att öppna en supportbegäran för ditt cacheminne.





## <a name="default-redis-server-configuration"></a>Redis-standardserverkonfiguration
Nya Azure Cache for Redis instanser konfigureras med följande standardvärden för Redis-konfiguration:

> [!NOTE]
> Inställningarna i det här avsnittet kan inte ändras med hjälp av `StackExchange.Redis.IServer.ConfigSet` metoden . Om den här metoden anropas med något av kommandona i det här avsnittet, kommer ett undantag som liknar följande exempel att visas:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alla värden som kan konfigureras, till exempel **max-memory-policy,** kan konfigureras via Azure Portal eller kommandoradshanteringsverktyg som Azure CLI eller PowerShell.
>
>

| Inställning | Standardvärde | Beskrivning |
| --- | --- | --- |
| `databases` |16 |Standardantalet databaser är 16, men du kan konfigurera ett annat nummer baserat på prisnivån. <sup>1</sup> Standarddatabasen är DB 0. Du kan välja en annan databas per anslutning med hjälp av där `connection.GetDatabase(dbid)` är ett tal mellan och `dbid` `0` `databases - 1` . |
| `maxclients` |Beror på prisnivå<sup>2</sup> |Det här värdet är det maximala antalet anslutna klienter som tillåts samtidigt. När gränsen har nåtts stänger Redis alla nya anslutningar och returnerar felet "maximalt antal klienter har nåtts". |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-principen är inställningen för hur Redis väljer vad som ska tas bort när (storleken på cacheerbjudandet som du valde när du skapade `maxmemory` cachen) nås. Med Azure Cache for Redis standardinställningen är , vilket tar bort nycklarna med en förfallouppsättning `volatile-lru` med hjälp av en LRU-algoritm. Den här inställningen kan konfigureras i Azure Portal. Mer information finns i [Minnespolicyer.](#memory-policies) |
| `maxmemory-samples` |3 |För att spara minne är LRU och minimala TTL-algoritmer ungefärliga algoritmer i stället för exakta algoritmer. Som standard kontrollerar Redis tre nycklar och väljer den som användes mindre nyligen. |
| `lua-time-limit` |5 000 |Maximal körningstid för ett Lua-skript i millisekunder. Om den maximala körningstiden nås loggar Redis att ett skript fortfarande körs efter den längsta tillåtna tiden och börjar svara på frågor med ett fel. |
| `lua-event-limit` |500 |Maximal storlek på skripthändelsekö. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032 mb 8 MB 60 |Buffertgränserna för klientutdata kan användas för att tvinga frånkoppling av klienter som inte läser data från servern tillräckligt snabbt av någon anledning (en vanlig orsak är att pub/sub-klienten inte kan använda meddelanden så snabbt som utgivaren kan producera dem). Mer information finns i [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> Gränsen för `databases` är olika för varje Azure Cache for Redis prisnivå och kan anges när cachen skapas. Om ingen `databases` inställning anges när cachen skapas är standardvärdet 16.

* Basic- och Standard-cacheminnen
  * C0-cache (250 MB) – upp till 16 databaser
  * C1-cache (1 GB) – upp till 16 databaser
  * C2-cache (2,5 GB) – upp till 16 databaser
  * C3-cache (6 GB) – upp till 16 databaser
  * C4-cache (13 GB) – upp till 32 databaser
  * C5-cache (26 GB) – upp till 48 databaser
  * C6-cache (53 GB) – upp till 64 databaser
* Premium-cacheminnen
  * P1 (6 GB–60 GB) – upp till 16 databaser
  * P2 (13 GB–130 GB) – upp till 32 databaser
  * P3 (26 GB–260 GB) – upp till 48 databaser
  * P4 (53 GB–530 GB) – upp till 64 databaser
  * Alla Premium-cacheminnen med Redis-kluster aktiverat – Redis-kluster stöder endast användning av databas 0, så gränsen för premiumcache med Redis-kluster aktiverat är i praktiken 1 och kommandot Välj tillåts `databases` inte. [](https://redis.io/commands/select) Mer information finns i [Behöver jag göra några ändringar i mitt klientprogram för att använda klustring?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Mer information om databaser finns i [Vad är Redis-databaser?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> Inställningen `databases` kan bara konfigureras när cachen skapas och endast använda PowerShell, CLI eller andra hanteringsklienter. Ett exempel på hur du `databases` konfigurerar när cachen skapas med Hjälp av PowerShell finns [i New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` är olika för Azure Cache for Redis olika prisnivå.

* Basic- och Standard-cacheminnen
  * C0-cache (250 MB) – upp till 256 anslutningar
  * C1-cache (1 GB) – upp till 1 000 anslutningar
  * C2-cache (2,5 GB) – upp till 2 000 anslutningar
  * C3-cache (6 GB) – upp till 5 000 anslutningar
  * C4-cache (13 GB) – upp till 10 000 anslutningar
  * C5-cache (26 GB) – upp till 15 000 anslutningar
  * C6-cache (53 GB) – upp till 20 000 anslutningar
* Premium-cacher
  * P1 (6 GB–60 GB) – upp till 7 500 anslutningar
  * P2 (13 GB–130 GB) – upp till 15 000 anslutningar
  * P3 (26 GB–260 GB) – upp till 30 000 anslutningar
  * P4 (53 GB–530 GB) – upp till 40 000 anslutningar

> [!NOTE]
> Varje cachestorlek tillåter upp *till ett visst* antal anslutningar, men varje anslutning till Redis har kostnader kopplade till sig. Ett exempel på sådana kostnader är cpu- och minnesanvändning som ett resultat av TLS/SSL-kryptering. Den maximala anslutningsgränsen för en viss cachestorlek förutsätter en lätt belastad cache. Om belastningen från *anslutningsbelastningen plus* belastningen från klientåtgärder överskrider kapaciteten för systemet kan cacheminnet uppleva kapacitetsproblem även om du inte har överskridit anslutningsgränsen för den aktuella cachestorleken.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-kommandon stöds inte i Azure Cache for Redis
> [!IMPORTANT]
> Eftersom konfiguration och hantering av Azure Cache for Redis-instanser hanteras av Microsoft inaktiveras följande kommandon. Om du försöker anropa dem visas ett felmeddelande som liknar `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * FELSÖK
> * Migrera
> * SPARA
> * Avstängning
> * SLAVEOF
> * KLUSTER – Klusterskrivningskommandon är inaktiverade, men skrivskyddade klusterkommandon tillåts.
>
>

Mer information om Redis-kommandon finns i [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Redis-konsol
Du kan på ett säkert sätt utfärda kommandon till Azure Cache for Redis instanser med hjälp av **Redis-konsolen**, som är tillgänglig i Azure Portal för alla cachenivåer.

> [!IMPORTANT]
> - Redis-konsolen fungerar inte med [VNET](cache-how-to-premium-vnet.md). När ditt cacheminne är en del av ett VNET kan endast klienter i det virtuella nätverket komma åt cachen. Eftersom Redis-konsolen körs i din lokala webbläsare, som ligger utanför det virtuella nätverket, kan den inte ansluta till ditt cacheminne.
> - Alla Redis-kommandon stöds inte i Azure Cache for Redis. En lista över Redis-kommandon som är inaktiverade för Azure Cache for Redis finns i föregående [Redis-kommandon som inte stöds Azure Cache for Redis](#redis-commands-not-supported-in-azure-cache-for-redis) avsnittet. Mer information om Redis-kommandon finns i [https://redis.io/commands](https://redis.io/commands) .
>
>

Om du vill komma åt Redis-konsolen **klickar du på** Konsol Azure Cache for Redis **bladet.**

![Skärmbild som visar knappen Konsol.](./media/cache-configure/redis-console-menu.png)

Om du vill utfärda kommandon mot din cacheinstans skriver du önskat kommando i konsolen.

![Skärmbild som visar Redis-konsolen med indatakommandot och resultat.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Använda Redis-konsolen med en premiumklustercache

När du använder Redis-konsolen med en premiumklustercache kan du utfärda kommandon till ett enskilt fragment av cachen. Om du vill utfärda ett kommando till en specifik shard ansluter du först till det önskade fragmentet genom att klicka på det i shardväljaren.

![Redis-konsol](./media/cache-configure/redis-console-premium-cluster.png)

Om du försöker komma åt en nyckel som lagras i en annan shard än den anslutna sharden får du ett felmeddelande som liknar följande meddelande:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

I föregående exempel är shard 1 den valda sharden, men finns i shard 0, vilket indikeras av delen `myKey` `(shard 0)` av felmeddelandet. I det här exemplet för att komma åt väljer du shard 0 med hjälp av `myKey` shardväljaren och kör sedan önskat kommando.


## <a name="move-your-cache-to-a-new-subscription"></a>Flytta ditt cacheminne till en ny prenumeration
Du kan flytta cacheminnet till en ny prenumeration genom att klicka på **Flytta**.

![Flytta Azure Cache for Redis](./media/cache-configure/redis-cache-move.png)

Information om hur du flyttar resurser från en resursgrupp till en annan och från en prenumeration till en annan finns i Flytta resurser till [en ny resursgrupp eller prenumeration.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med Redis-kommandon finns [i Hur kör jag Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands)
