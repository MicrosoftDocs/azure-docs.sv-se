---
title: Så här konfigurerar du Azure cache för Redis
description: Förstå standard konfigurationen för Redis för Azure cache för Redis och lär dig hur du konfigurerar Azure cache för Redis-instanser
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: c13821544ff9725a387534370fc716c70072bb85
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831781"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Så här konfigurerar du Azure cache för Redis
I det här avsnittet beskrivs de konfigurationer som är tillgängliga för Azure-cachen för Redis-instanser. Det här avsnittet beskriver också standard konfigurationen för Redis-servern för Azure cache för Redis-instanser.

> [!NOTE]
> Mer information om hur du konfigurerar och använder Premium cache-funktioner finns i [Konfigurera persistence](cache-how-to-premium-persistence.md), [Konfigurera klustring](cache-how-to-premium-clustering.md)och [Konfigurera Virtual Network support](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurera Azure cache för Redis-inställningar
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure cache för Redis-inställningar visas och konfigureras på bladet **Azure cache för Redis** med hjälp av **resurs-menyn**.

![Azure cache för Redis-inställningar](./media/cache-configure/redis-cache-settings.png)

Du kan visa och konfigurera följande inställningar med **resurs-menyn**.

* [Översikt](#overview)
* [Aktivitetslogg](#activity-log)
* [Åtkomstkontroll (IAM)](#access-control-iam)
* [Taggar](#tags)
* [Diagnostisera och lösa problem](#diagnose-and-solve-problems)
* [Inställningar](#settings)
    * [Åtkomstnycklar](#access-keys)
    * [Avancerade inställningar](#advanced-settings)
    * [Azure cache för Redis Advisor](#azure-cache-for-redis-advisor)
    * [Skalning](#scale)
    * [Kluster storlek](#cluster-size)
    * [Data persistens](#redis-data-persistence)
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
    * [Redis mått](#redis-metrics)
    * [Aviseringsregler](#alert-rules)
    * [Diagnostik](#diagnostics)
* Stöd för & fel söknings inställningar
    * [Resurshälsa](#resource-health)
    * [Ny supportbegäran](#new-support-request)


## <a name="overview"></a>Översikt

**Översikt** innehåller grundläggande information om din cache, till exempel namn, portar, pris nivå och valda cache-mått.

### <a name="activity-log"></a>Aktivitetslogg

Klicka på **aktivitets logg** för att Visa åtgärder som utförts i cacheminnet. Du kan också använda filtrering för att expandera den här vyn för att inkludera andra resurser. Mer information om hur du arbetar med gransknings loggar finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md). Mer information om övervakning av Azure cache för Redis-händelser finns i [åtgärder och aviseringar](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

Avsnittet **åtkomst kontroll (IAM)** ger stöd för rollbaserad åtkomst kontroll i Azure (Azure RBAC) i Azure Portal. Den här konfigurationen hjälper organisationer att uppfylla sina åtkomst hanterings krav enkelt och exakt. Mer information finns i [rollbaserad åtkomst kontroll i Azure i Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Taggar

I avsnittet **taggar** kan du organisera dina resurser. Mer information finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostisera och lösa problem

Klicka på **diagnostisera och lös problem** för att tillhandahålla vanliga problem och strategier för att lösa dem.



## <a name="settings"></a>Inställningar
I avsnittet **Inställningar** kan du komma åt och konfigurera följande inställningar för cacheminnet.

* [Åtkomstnycklar](#access-keys)
* [Avancerade inställningar](#advanced-settings)
* [Azure cache för Redis Advisor](#azure-cache-for-redis-advisor)
* [Skalning](#scale)
* [Kluster storlek](#cluster-size)
* [Data persistens](#redis-data-persistence)
* [Schemauppdateringar](#schedule-updates)
* [Geo-replikering](#geo-replication)
* [Virtual Network](#virtual-network)
* [Brandvägg](#firewall)
* [Egenskaper](#properties)
* [Lås](#locks)
* [Automationsskript](#automation-script)



### <a name="access-keys"></a>Åtkomstnycklar
Klicka på **åtkomst nycklar** för att visa eller återskapa åtkomst nycklarna för cacheminnet. Dessa nycklar används av klienterna som ansluter till din cache.

![Azure cache för Redis-åtkomst nycklar](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Avancerade inställningar
Följande inställningar konfigureras på bladet **Avancerade inställningar** .

* [Åtkomst portar](#access-ports)
* [Minnes principer](#memory-policies)
* [Meddelanden om disk utrymme (avancerade inställningar)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Åtkomst portar
Som standard är icke-TLS/SSL-åtkomst inaktive rad för nya cacheminnen. Om du vill aktivera en icke-TLS-port klickar du på **Nej** för **Tillåt åtkomst endast via SSL** på bladet **Avancerade inställningar** och klicka sedan på **Spara**.

> [!NOTE]
> TLS-åtkomst till Azure cache för Redis har stöd för TLS 1,0, 1,1 och 1,2 för närvarande, men version 1,0 och 1,1 dras tillbaka snart.  Mer information finns på [sidan ta bort TLS 1,0 och 1,1](cache-remove-tls-10-11.md) .

![Azure cache för Redis åtkomst portar](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Minnes principer
**Maxmemory-principen**, **maxmemory-reserverade** och **maxfragmentationmemory-reserverade** inställningar på bladet **Avancerade inställningar** konfigurerar minnes principerna för cachen.

![Azure-cache för Redis maxmemory-princip](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory-principen** konfigurerar borttagnings principen för cachen och gör att du kan välja bland följande avlägsna principer:

* `volatile-lru` – Det här är standard principen för avavlägsning.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Mer information om `maxmemory` principer finns i [avlägsna principer](https://redis.io/topics/lru-cache#eviction-policies).

**Maxmemory-reserverad-** inställningen konfigurerar mängden minne i MB per instans i ett kluster som är reserverat för icke-cache-åtgärder, till exempel replikering under redundans. Genom att ange det här värdet får du en mer konsekvent redis-server när belastningen varierar. Det här värdet ska ställas in högre för arbets belastningar som är skrivna tung. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

**Maxfragmentationmemory-reserverad-** inställningen konfigurerar mängden minne i MB per instans i ett kluster som är reserverad för att få plats för fragmentering. Genom att ange det här värdet kan du ha en mer konsekvent redis-server när cachen är full eller nära full och fragmenteringen är hög. När minnet är reserverat för sådana åtgärder är det inte tillgängligt för lagring av cachelagrade data.

En sak att tänka på när du väljer ett nytt minnes reservations värde (**maxmemory-reserverad** eller **maxfragmentationmemory**) är hur den här ändringen kan påverka ett cacheminne som redan körs med stora mängder data. Om du till exempel har en 53 GB-cache med 49 GB data, ändrar du reservation svärdet till 8 GB, så tar den här ändringen bort det högsta tillgängliga minnet för systemet ned till 45 GB. Om antingen din aktuella `used_memory` eller dina `used_memory_rss` värden är högre än den nya gränsen på 45 GB måste systemet ta bort data förrän både `used_memory` och `used_memory_rss` är under 45 GB. Avlägsnandet kan öka Server belastningen och fragmenteringen av minnet. Mer information om cache-mått som `used_memory` och `used_memory_rss` finns i [tillgängliga mått och rapporterings intervall](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> De **maxmemory-reserverade** och **maxfragmentationmemory** inställningarna är bara tillgängliga för cacheminnen standard och Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Meddelanden om disk utrymme (avancerade inställningar)
Meddelanden om Redis-meddelanden har kon figurer ATS på bladet **Avancerade inställningar** . Meddelanden om inloggnings meddelanden tillåter klienter att ta emot meddelanden när vissa händelser inträffar.

![Avancerade inställningar för Azure cache för Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Meddelanden om disk utrymme och inställningen **meddela-Backsteg-Events** är bara tillgängliga för cacheminnen standard och Premium.
>
>

Mer information finns i [meddelanden om Redis-meddelanden](https://redis.io/topics/notifications). Exempel kod finns i [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) -filen i [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) -exemplet.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure cache för Redis Advisor
Bladet **Azure cache för Redis Advisor** visar rekommendationer för din cache. Under normal drift visas inga rekommendationer.

![Skärm bild som visar var rekommendationerna visas.](./media/cache-configure/redis-cache-no-recommendations.png)

Om ett villkor inträffar under driften av din cache, till exempel hög minnes användning, nätverks bandbredd eller server belastning, visas en avisering på bladet **Azure cache för Redis** .

![Skärm bild som visar var aviseringar visas i avsnittet Azure-cache för Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Mer information hittar du på bladet **rekommendationer** .

![Rekommendationer](./media/cache-configure/redis-cache-recommendations.png)

Du kan övervaka dessa mått i avsnitten [övervaknings diagram](cache-how-to-monitor.md#monitoring-charts) och [användnings diagram](cache-how-to-monitor.md#usage-charts) på bladet **Azure cache för Redis** .

Varje pris nivå har olika gränser för klient anslutningar, minne och bandbredd. Om din cache närmar sig högsta kapacitet för dessa mått under en varaktig tids period, skapas en rekommendation. Mer information om mått och begränsningar som granskas av **rekommendationer** -verktyget finns i följande tabell:

| Azure cache för Redis-mått | Mer information |
| --- | --- |
| Användning av nätverksbandbredd |[Cache-prestanda – tillgänglig bandbredd](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Anslutna klienter |[Standard konfiguration av Redis-maxclients](#maxclients) |
| Server belastning |[Användnings diagram – redis server load](cache-how-to-monitor.md#usage-charts) |
| Minnesanvändning |[Cache-prestanda-storlek](cache-planning-faq.md#azure-cache-for-redis-performance) |

Uppgradera din cache genom att klicka på **Uppgradera nu** för att ändra pris nivån och [skala](#scale) cacheminnet. Mer information om hur du väljer en pris nivå finns i [välja rätt nivå](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Skala
Klicka på **skala** för att visa eller ändra pris nivån för din cache. Mer information om skalning finns i [skala Azure cache för Redis](cache-how-to-scale.md).

![Azure cache för Redis-pris nivå](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis kluster storlek
Klicka på **kluster storlek** om du vill ändra kluster storleken för att köra Premium-cache med kluster aktiverat.

![Kluster storlek](./media/cache-configure/redis-cache-redis-cluster-size.png)

Om du vill ändra kluster storleken använder du skjutreglaget eller skriver ett tal mellan 1 och 10 i text rutan **Shard Count** och klickar på **OK** för att spara.

> [!IMPORTANT]
> Redis-klustring är bara tillgängligt för Premium-cacheminnen. Mer information finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Redis-datapersistens
Klicka på **data persistence** om du vill aktivera, inaktivera eller konfigurera data beständighet för din Premium-cache. Azure cache för Redis erbjuder Redis beständighet med antingen RDB persistence eller AOF persistence.

Mer information finns i [så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis data persist är bara tillgängligt för Premium-cacheminnen.
>
>

### <a name="schedule-updates"></a>Schemauppdateringar
På bladet **schema uppdateringar** kan du ange ett underhålls fönster för redis server-uppdateringar för din cache.

> [!IMPORTANT]
> Underhålls perioden gäller endast för redis server-uppdateringar och inte för Azure-uppdateringar eller uppdateringar av operativ systemet på de virtuella datorer som är värdar för cachen.
>
>

![Schemauppdateringar](./media/cache-configure/redis-schedule-updates.png)

Om du vill ange en underhålls period kontrollerar du önskade dagar och anger start timme för underhålls perioden för varje dag och klickar på **OK**. Tiden för underhålls perioden är UTC.

Mer information och instruktioner finns i [Azure cache for Redis administration-Schedule updates](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Geo-replikering

Bladet **geo-replikering** är en mekanism för att länka två Premium-nivåer i Azure-cache för Redis-instanser. Ett cacheminne har angetts som primärt länkat cache och den andra som sekundär länkad cache. Den sekundära länkade cachen blir skrivskyddad och data som skrivs till den primära cachen replikeras till den sekundära länkade cachen. Den här funktionen kan användas för att replikera en cache i Azure-regioner.

> [!IMPORTANT]
> **Geo-replikering** är endast tillgängligt för cacheminnen på Premium-nivån. Mer information och instruktioner finns i [så här konfigurerar du geo-replikering för Azure cache för Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
I avsnittet **Virtual Network** kan du konfigurera inställningarna för det virtuella nätverket för cacheminnet. Information om hur du skapar en Premium-cache med VNET-stöd och uppdaterar dess inställningar finns i [så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Virtuella nätverks inställningar är bara tillgängliga för Premium-cacheminnen som har kon figurer ATS med VNET-stöd under skapandet av cacheminnet.
>
>

### <a name="firewall"></a>Brandvägg

Konfiguration av brand Väggs regler är tillgänglig för alla Azure-cache för Redis-nivåer.

Klicka på **brand vägg** för att visa och konfigurera brand Väggs regler för cachelagring.

![Brandvägg](./media/cache-configure/redis-firewall-rules.png)

Du kan ange brand Väggs regler med start-och slut-IP-adressintervall. När brand Väggs regler har kon figurer ATS kan endast klient anslutningar från de angivna IP-adressintervall ansluta till cacheminnet. När en brand Väggs regel sparas, sker en kort fördröjning innan regeln börjar gälla. Den här fördröjningen är vanligt vis mindre än en minut.

> [!IMPORTANT]
> Anslutningar från Azure cache för Redis övervaknings system tillåts alltid, även om brand Väggs regler har kon figurer ATS.
>
>

### <a name="properties"></a>Egenskaper
Klicka på **Egenskaper** för att visa information om din cache, inklusive cache-slutpunkt och portar.

![Azure cache för Redis-egenskaper](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Lås
I avsnittet **Lås** kan du låsa en prenumeration, resurs grupp eller resurs för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automationsskript

Klicka på **Automation-skript** för att bygga och exportera en mall för dina distribuerade resurser för framtida distributioner. Mer information om hur du arbetar med mallar finns i [distribuera resurser med Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Administrations inställningar
Med inställningarna i avsnittet **Administration** kan du utföra följande administrativa uppgifter för din cache.

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importera data](#importexport)
* [Exportera data](#importexport)
* [Starta om](#reboot)


### <a name="importexport"></a>Import/Export
Import/export är en Azure-cache för Redis data hanterings åtgärd som gör att du kan importera och exportera data i cacheminnet genom att importera och exportera en Azure-cache för Redis Database (RDB) ögonblicks bild från en Premium-cache till en sid-BLOB i ett Azure Storage konto. Genom att importera/exportera kan du migrera mellan olika Azure cache för Redis-instanser eller fylla i cachen med data innan du använder.

Import kan användas för att hämta Redis-kompatibla RDB-filer från en Redis-server som körs i molnet eller i miljön, inklusive Redis som körs på Linux, Windows eller någon annan moln leverantör, till exempel Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läser Azure cache för Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cachen.

Med export kan du exportera data som lagras i Azure cache för Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure-cache för Redis-instans till en annan eller till en annan redis-server. Under exporten skapas en temporär fil på den virtuella datorn som är värd för Azure cache för Redis-Server-instansen och filen laddas upp till det angivna lagrings kontot. När export åtgärden har slutförts med statusen lyckad eller misslyckad tas den temporära filen bort.

> [!IMPORTANT]
> Import/export är endast tillgängligt för cacheminnen på Premium-nivån. Mer information och instruktioner finns i [Importera och exportera data i Azure cache för Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Starta om
På bladet **starta om** kan du starta om noderna i cacheminnet. Med den här omstarts funktionen kan du testa ditt program för återhämtning om det uppstår ett problem med en cache-nod.

![Starta om](./media/cache-configure/redis-cache-reboot.png)

Om du har en Premium-cache med aktive rad kluster kan du välja vilken Shards som ska startas om.

![Skärm bild som visar var du kan välja vilken Shards i cacheminnet som ska startas om.](./media/cache-configure/redis-cache-reboot-cluster.png)

Om du vill starta om en eller flera noder i cacheminnet väljer du önskade noder och klickar på **starta om**. Om du har en Premium-cache med klustrad aktive rad väljer du de Shard (ar) som ska startas om och klickar sedan på **starta om**. Efter några minuter startar den markerade noden om och är tillbaka online några minuter senare.

> [!IMPORTANT]
> Omstart är nu tillgängligt för alla pris nivåer. Mer information och instruktioner finns i [Azure cache for Redis administration – starta om](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Övervakning

I avsnittet **övervakning** kan du Konfigurera diagnostik och övervakning för Azure-cachen för Redis.
Mer information om Azure cache för Redis-övervakning och diagnostik finns i [så här övervakar du Azure cache för Redis](cache-how-to-monitor.md).

![Diagnostik](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis mått](#redis-metrics)
* [Aviseringsregler](#alert-rules)
* [Diagnostik](#diagnostics)

### <a name="redis-metrics"></a>Redis mått
Klicka på **Redis mått** om du vill [Visa mått](cache-how-to-monitor.md#view-cache-metrics) för din cache.

### <a name="alert-rules"></a>Aviseringsregler

Klicka på **aviserings regler** om du vill konfigurera aviseringar baserat på Azure cache för Redis-mått. Mer information finns i [aviseringar](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostik

Som standard lagras cache-mått i Azure Monitor i [30 dagar](../azure-monitor/platform/data-platform-metrics.md) och tas sedan bort. Om du vill spara dina cache-mått i mer än 30 dagar klickar du på **diagnostik** för att [Konfigurera det lagrings konto](cache-how-to-monitor.md#export-cache-metrics) som används för att lagra cache-diagnostik.

>[!NOTE]
>Förutom att arkivera dina cache-mått till lagring kan du också [strömma dem till en händelsehubben eller skicka dem till Azure Monitor loggar](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Stöd för & fel söknings inställningar
Inställningarna i avsnittet **support och fel sökning** innehåller alternativ för att lösa problem med cachen.

![Support och felsökning](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resurshälsa](#resource-health)
* [Ny supportbegäran](#new-support-request)

### <a name="resource-health"></a>Resurshälsa
**Resource Health** bevakar din resurs och meddelar dig om den körs som förväntat. Mer information om Azure Resource Health Service finns i [Översikt över Azure Resource Health](../service-health/resource-health-overview.md).

> [!NOTE]
> Resource Health kan för närvarande inte rapportera om hälsan för Azure cache för Redis-instanser som finns i ett virtuellt nätverk. Mer information finns i gör så här [fungerar alla cache-funktioner när du är värd för en cache i ett virtuellt nätverk?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Ny supportbegäran
Klicka på **ny supportbegäran** för att öppna en supportbegäran för cacheminnet.





## <a name="default-redis-server-configuration"></a>Standard konfiguration av redis-server
Nya Azure cache för Redis-instanser konfigureras med följande standard konfigurations värden för Redis:

> [!NOTE]
> Det går inte att ändra inställningarna i det här avsnittet med hjälp av `StackExchange.Redis.IServer.ConfigSet` metoden. Om den här metoden anropas med ett av kommandona i det här avsnittet genereras ett undantag som liknar följande exempel:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alla värden som kan konfigureras, till exempel **Max-Memory-princip**, kan konfigureras via Azure Portal eller kommando rads hanterings verktyg som Azure CLI eller PowerShell.
>
>

| Inställning | Standardvärde | Beskrivning |
| --- | --- | --- |
| `databases` |16 |Standard antalet databaser är 16 men du kan konfigurera ett annat nummer baserat på pris nivån. <sup>1</sup> standard databasen är dB 0, du kan välja en annan per-anslutnings-basis med `connection.GetDatabase(dbid)` där `dbid` är ett tal mellan `0` och `databases - 1` . |
| `maxclients` |Är beroende av pris nivå<sup>2</sup> |Det här värdet är det maximala antalet anslutna klienter som tillåts på samma gång. När gränsen har nåtts stänger Redis alla nya anslutningar och returnerar ett "maximalt antal klienter har nåtts"-felet. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-principen är inställningen för hur Redis väljer vad som ska tas bort när `maxmemory` (storleken på det cache-erbjudande som du valde när du skapade cachen) har nåtts. Med Azure cache för Redis är standardvärdet `volatile-lru` , vilket tar bort nycklarna med en förfallo uppsättning med en LRU-algoritm. Den här inställningen kan konfigureras i Azure Portal. Mer information finns i [minnes principer](#memory-policies). |
| `maxmemory-samples` |3 |För att spara minne är LRU och minimala TTL-algoritmer approximerade algoritmer i stället för exakta algoritmer. Som standard kontrollerar Redis tre nycklar och plockar den som användes mindre nyligen. |
| `lua-time-limit` |5 000 |Maximal körnings tid för ett Lua-skript i millisekunder. Om den maximala körnings tiden uppnås loggar Redis som ett skript fortfarande körs efter den längsta tillåtna tiden och börjar svara på frågor med ett fel. |
| `lua-event-limit` |500 |Max storlek för skript händelse kön. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8 MB 60 |Gränserna för klientens utdatabuffert kan användas för att framtvinga från koppling av klienter som inte läser data från servern tillräckligt många skäl (en vanlig orsak är att en pub/sub-klient inte kan använda meddelanden så snabbt som utgivaren kan producera dem). Mer information finns i [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> Gränsen för `databases` skiljer sig åt för varje Azure-cache för pris nivån Redis och kan ställas in när cachen skapas. Om ingen `databases` inställning anges när cachen skapas är standardvärdet 16.

* Basic-och standard-cache
  * C0 (250 MB) cache-upp till 16 databaser
  * C1-cache (1 GB) – upp till 16 databaser
  * C2 (2,5 GB) cache-upp till 16 databaser
  * C3-cache (6 GB) till 16 databaser
  * C4-cache (13 GB) – upp till 32 databaser
  * C5-cache (26 GB) – upp till 48 databaser
  * C6 (53 GB) cache – upp till 64 databaser
* Premium-cacheminnen
  * P1 (6 GB-60 GB) – upp till 16 databaser
  * P2 (13 GB-130 GB) – upp till 32 databaser
  * P3 (26 GB-260 GB) – upp till 48 databaser
  * P4 (53 GB-530 GB) – upp till 64 databaser
  * Alla Premium-cacheminnen med Redis Cluster Enabled-Redis Cluster stöder bara användning av databas 0, så `databases` gränsen för alla Premium-cache med Redis-klustret är effektiv 1 och [Select](https://redis.io/commands/select) -kommandot är inte tillåtet. Mer information finns i [behöver jag göra ändringar i klient programmet för att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Mer information om databaser finns i [Vad är Redis-databaser?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases`Inställningen kan bara konfigureras när cachelagring skapas och bara med PowerShell, CLI eller andra hanterings klienter. Ett exempel på hur `databases` du konfigurerar under generering av cache med PowerShell finns i [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` är olika för varje Azure-cache för Redis-pris nivå.

* Basic-och standard-cache
  * C0 (250 MB) cache-upp till 256 anslutningar
  * C1-cache (1 GB) – upp till 1 000 anslutningar
  * C2 (2,5 GB) cache-upp till 2 000 anslutningar
  * C3-cache (6 GB) till 5 000 anslutningar
  * C4-cache (13 GB) – upp till 10 000 anslutningar
  * C5-cache (26 GB) – upp till 15 000 anslutningar
  * C6-cache (53 GB) – upp till 20 000 anslutningar
* Premium-cacheminnen
  * P1 (6 GB-60 GB) – upp till 7 500 anslutningar
  * P2 (13 GB-130 GB) – upp till 15 000 anslutningar
  * P3 (26 GB-260 GB) – upp till 30 000 anslutningar
  * P4 (53 GB-530 GB) – upp till 40 000 anslutningar

> [!NOTE]
> Varje cache-storlek tillåter *upp till* ett visst antal anslutningar, och varje anslutning till Redis har kopplad till sig. Ett exempel på sådana kostnader är CPU och minnes användning som ett resultat av TLS/SSL-kryptering. Den maximala anslutnings gränsen för en specifik cachestorlek förutsätter en lätt inläst cache. Om belastningen från anslutnings belastning *plus* belastning från klient åtgärder överskrider kapaciteten för systemet, kan cachen uppleva kapacitets problem även om du inte har överskridit anslutnings gränsen för den aktuella cachestorleken.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis-kommandon stöds inte i Azure cache för Redis
> [!IMPORTANT]
> Eftersom konfiguration och hantering av Azure cache för Redis-instanser hanteras av Microsoft, är följande kommandon inaktiverade. Om du försöker anropa dem visas ett fel meddelande liknande `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * FELSÖK
> * FLYTTAR
> * SPARA
> * ANVÄNDARINITIERADE
> * SLAVEOF
> * KLUSTER-kluster skrivnings kommandon är inaktiverade, men skrivskyddade kluster kommandon är tillåtna.
>
>

Mer information om Redis-kommandon finns i [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Redis-konsol
Du kan på ett säkert sätt utfärda kommandon till Azure cache för Redis-instanser med hjälp av **Redis-konsolen**, som finns i Azure Portal för alla cache-nivåer.

> [!IMPORTANT]
> - Redis-konsolen fungerar inte med [VNet](cache-how-to-premium-vnet.md). När din cache ingår i ett VNET kan endast klienter i VNET komma åt cacheminnet. Eftersom Redis-konsolen körs i din lokala webbläsare, som är utanför det virtuella nätverket, kan den inte ansluta till cacheminnet.
> - Det finns inte stöd för alla Redis-kommandon i Azure cache för Redis. En lista över Redis-kommandon som är inaktiverade för Azure cache för Redis finns i föregående [Redis-kommandon som inte stöds i Azure cache för Redis](#redis-commands-not-supported-in-azure-cache-for-redis) -avsnittet. Mer information om Redis-kommandon finns i [https://redis.io/commands](https://redis.io/commands) .
>
>

Öppna Redis-konsolen genom att klicka på **konsol** från **Azure-cache för Redis** -bladet.

![Skärm bild som visar knappen konsol.](./media/cache-configure/redis-console-menu.png)

Om du vill utfärda kommandon mot din cache-instans skriver du önskat kommando i konsolen.

![Skärm bild thas visar Redis-konsolen med inmatat kommando och resultat.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Använda Redis-konsolen med en Premium klustrad cache

När du använder Redis-konsolen med en förstklassig klustrad cache kan du skicka kommandon till en enda Shard i cacheminnet. Om du vill skicka ett kommando till en speciell Shard ansluter du först till önskad Shard genom att klicka på den på Shard-väljaren.

![Redis-konsol](./media/cache-configure/redis-console-premium-cluster.png)

Om du försöker komma åt en nyckel som lagras i en annan Shard än den anslutna Shard visas ett fel meddelande som liknar följande meddelande:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

I det föregående exemplet är Shard 1 den valda Shard, men finns `myKey` i Shard 0, som anges i den `(shard 0)` del av fel meddelandet. I det här exemplet väljer du `myKey` Shard 0 med Shard-väljaren och utfärdar sedan det önskade kommandot.


## <a name="move-your-cache-to-a-new-subscription"></a>Flytta cacheminnet till en ny prenumeration
Du kan flytta din cache till en ny prenumeration genom att klicka på **Flytta**.

![Flytta Azure cache för Redis](./media/cache-configure/redis-cache-move.png)

Information om hur du flyttar resurser från en resurs grupp till en annan, och från en prenumeration till en annan, finns i [Flytta resurser till ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du arbetar med Redis-kommandon finns i [Hur kan jag köra Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands)
