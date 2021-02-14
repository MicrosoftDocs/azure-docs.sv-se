---
title: Vad är Azure Cache for Redis?
description: Lär dig om Azure cache för Redis för att aktivera cachelagring av cacheminnen, cachelagring av innehåll, cachelagring av användarsessioner, jobb och meddelande köer och distribuerade transaktioner.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 9b1176f579754d714490297e9ff960d7808bd834
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383168"
---
# <a name="about-azure-cache-for-redis"></a>Om Azure Cache for Redis
Azure cache för Redis tillhandahåller ett minnes intern data lager baserat på [Redis](https://redis.io/) -programvaran. Redis förbättrar prestanda och skalbarhet för ett program som använder på backend-data lagrar mycket. Det går att bearbeta stora mängder programbegäran genom att lagra data som används ofta i Server minnet som kan skrivas till och läsas från snabbt. Redis ger en kritisk data lagrings lösning med låg latens och hög data flöde till moderna program.

Azure cache för Redis erbjuder både Redis med öppen källkod (OSS Redis) och en kommersiell produkt från Redis Labs (Redis Enterprise) som en hanterad tjänst. Det ger säkra och dedikerade Redis-Server instanser och fullständig Redis API-kompatibilitet. Tjänsten drivs av Microsoft, som finns i Azure och är tillgänglig för alla program inom eller utanför Azure.

Azure cache för Redis kan användas som distribuerade data eller innehålls-cache, ett migreringsarkiv, en meddelande koordinator med mera. Den kan distribueras som fristående eller tillsammans med andra Azure Database-tjänster, till exempel Azure SQL eller Cosmos DB.

## <a name="key-scenarios"></a>Viktiga scenarier
Azure cache för Redis förbättrar program prestandan genom att stödja vanliga mönster för program arkitektur. Här följer exempel på några av de vanligaste:

| Mönster      | Beskrivning                                        |
| ------------ | -------------------------------------------------- |
| [Datacache](cache-web-app-cache-aside-leaderboard.md) | Databaserna är ofta för stora för att läsas in direkt i en cache. Det är vanligt att använda [cache-undan-](/azure/architecture/patterns/cache-aside) mönstret för att endast läsa in data i cachen vid behov. När systemet gör ändringar i data kan systemet också uppdatera cachen, som sedan distribueras till andra klienter. Dessutom kan systemet ange förfallo datum för data eller använda en princip för att utlösa data uppdateringar i cacheminnet.|
| [Innehålls-cache](cache-aspnet-output-cache-provider.md) | Många webb sidor genereras från mallar som använder statiskt innehåll, till exempel sidhuvuden, sid fötter, banderoller. Dessa statiska objekt bör inte ändras ofta. Om du använder en minnes intern cache får du snabb åtkomst till statiskt innehåll jämfört med backend-datalager. Det här mönstret minskar bearbetnings tiden och Server belastningen, vilket gör att webb servrar svarar mer. Det kan göra att du kan minska antalet servrar som behövs för att hantera belastningar. Azure cache för Redis tillhandahåller providern för Redis för utdata för att stödja det här mönstret med ASP.NET.|
| [Arkiv lag ring](cache-aspnet-session-state-provider.md) | Det här mönstret används ofta med shopping vagnar och andra användar historik data som ett webb program kanske vill associera med användarens cookies. Att lagra för stora datamängder i en cookie kan påverka prestanda negativt då storleken på cookie-filerna ökar och de skickas och verifieras med varje begäran. En typisk lösning använder cookien som en nyckel för att fråga data i en databas. Ett mycket snabbare sätt än att interagera med en hel relationsdatabas är att använda en minnesintern cache, som Azure Cache for Redis, till att associera information med en användare. |
| Jobb- och meddelandeköer | Program lägger ofta till uppgifter i en kö när de åtgärder som är kopplade till begäran tar tid att köra. Längre kör åtgärder placeras i kö för att bearbetas i följd, ofta av en annan server.  Den här metoden att skjuta upp arbete kallas för att placera uppgifter i kö. Azure cache för Redis tillhandahåller en distribuerad kö för att aktivera det här mönstret i ditt program.|
| Distribuerade transaktioner | Program kräver ibland en serie kommandon mot en backend-datalagringsplats för att köras som en enskild atomisk åtgärd. Alla kommandon måste slutföras, eller så måste alla återställas till det ursprungliga tillståndet. Azure cache för Redis stöder körning av en batch med-kommandon som en enskild [transaktion](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Redis-versioner

Azure cache för Redis stöder OSS Redis version 4. x och, som en för hands version, 6,0. Vi har gjort beslutet att hoppa över Redis 5,0 för att ta dig till den senaste versionen. Tidigare bevarade Azure cache för Redis endast en enda Redis-version. Det ger en nyare uppgradering av större versioner och minst en äldre stabil version går framåt. Du kan [välja vilken version som](cache-how-to-version.md) passar bäst för ditt program.


## <a name="service-tiers"></a>Tjänstnivåer
Azure Cache for Redis är tillgänglig på följande nivåer:

| Nivå | Beskrivning |
|---|---|
| Grundläggande | En OSS Redis-cache som körs på en enskild virtuell dator. Den här nivån har inget service nivå avtal (SLA) och är idealisk för utveckling/testning och icke-kritiska arbets belastningar. |
| Standard | En OSS Redis-cache som körs på två virtuella datorer i en replikerad konfiguration. |
| Premium | Högpresterande OSS Redis-cache. Den här nivån ger högre data flöde, lägre latens, bättre tillgänglighet och fler funktioner. Premium-cacheminnen distribueras på mer kraftfulla virtuella datorer jämfört med dem för Basic-eller standard-cacheminnen. |
| Enterprise (för hands version) | Högpresterande cacheminnen som drivs av Redis Labs Redis Enterprise-programvara. Den här nivån stöder Redis-moduler, inklusive RediSearch, RedisBloom och RedisTimeSeries. Dessutom erbjuder den ännu högre tillgänglighet än Premium nivån. |
| Enterprise Flash (för hands version) | Kostnads effektiva stora cacheminnen som drivs av Redis Labs Redis Enterprise-programvara. Den här nivån utökar Redis data lagring till beständigt minne, vilket är billigare än DRAM, på en virtuell dator. Det minskar den totala minnes kostnaden per GB. |

### <a name="feature-comparison"></a>Jämför funktioner
[Azure cache för Redis-prissättning](https://azure.microsoft.com/pricing/details/cache/) ger en detaljerad jämförelse mellan varje nivå. Följande tabell beskriver några av de funktioner som stöds på respektive nivå:

| Funktionsbeskrivning | Basic | Standard | Premium | Stora företag | Enterprise Flash |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Datakryptering |✔|✔|✔|✔|✔|
| [Nätverksisolering](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Skalning](cache-how-to-scale.md) |✔|✔|✔|-|-|
| [OSS-kluster](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Data persistens](cache-how-to-premium-persistence.md) |-|-|✔|-|-|
| [Zonredundans](cache-how-to-zone-redundancy.md) |-|-|Förhandsgranskning|Förhandsgranskning|Förhandsgranskning|
| [Geo-replikering](cache-how-to-geo-replication.md) |-|-|✔|-|-|
| [Moduler](https://redis.io/modules) |-|-|-|✔|✔|
| [Import/Export](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Schemalagda uppdateringar](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Välja rätt nivå
Tänk på följande när du väljer en Azure-cache för Redis-nivån:

* **Minne**: Basic-och standard-nivåerna erbjuder 250 MB – 53 GB; Premium-nivån 6 GB – 1,2 TB; Enterprise-nivåerna 12 GB-14 TB.  Om du vill skapa ett cacheminne på Premium-nivå som är större än 120 GB kan du använda Redis OSS-kluster. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). Mer information finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).
* **Prestanda**: cacheminnen i Premium-och Enterprise-nivåerna distribueras på maskin vara som har snabbare processorer och ger bättre prestanda jämfört med Basic-eller standard-nivån. Cacheminnen på Premium-nivån har högre genomflöde och lägre fördröjning. Mer information finns i [Azure cache för Redis-prestanda](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Dedikerad kärna för redis-server**: alla cacheminnen utom C0 kör DEDIKERADe VM-kärnor. Redis, som design, använder bara en tråd för kommando bearbetning. Azure cache för Redis använder ytterligare kärnor för I/O-bearbetning. Att ha fler kärnor ger bättre data flödes prestanda även om den inte kan producera linjär skalning. Dessutom levereras större VM-storlekar vanligt vis med högre bandbredds begränsningar än mindre. Det hjälper dig att undvika överbelastning i nätverket, vilket leder till timeout i ditt program.
* **Nätverks prestanda**: om du har en arbets belastning som kräver stora data flöden erbjuder Premium-eller Enterprise-nivån mer bandbredd jämfört med Basic eller standard. I varje nivå har större cache-minne också större bandbredd på grund av den underliggande virtuella datorn som är värd för cachen. Mer information finns i [Azure cache för Redis-prestanda](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Maximalt antal klient anslutningar**: Premium-nivån erbjuder det maximala antalet klienter som kan ansluta till Redis, med ett högre antal anslutningar för större cacheminnen för storlek. Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).
* **Hög tillgänglighet**: Azure cache för Redis tillhandahåller flera alternativ för [hög tillgänglighet](cache-high-availability.md) . Det garanterar att en standard-, Premium-eller företags-cache är tillgänglig enligt vårt [service avtal](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Service avtalet täcker endast anslutningen till cachens slut punkter. Service avtalet omfattar inte skydd mot data förlust. Vi rekommenderar att du använder funktionen Redis data persistion på Premium-och Enterprise-nivåerna för att öka återhämtningen mot data förlust.
* **Data beständighet**: Premium-och Enterprise-nivåerna gör att du kan spara cache-data till ett Azure Storage-konto och en hanterad disk. Bakomliggande infrastruktur problem kan leda till potentiell data förlust. Vi rekommenderar att du använder funktionen Redis data persistion på dessa nivåer för att öka återhämtningen mot data förlust. Azure cache för Redis erbjuder alternativ för både RDB och AOF (för hands version). För företags nivåer har data beständighet aktive rad som standard. För Premium-nivån, se [så här konfigurerar du persistence för en Premium Azure-cache för Redis](cache-how-to-premium-persistence.md).
* **Nätverks isolering**: Azure Private Link and Virtual Network (VNet) distributioner ger förbättrad säkerhet och trafik isolering för Azure cache för Redis. Med VNET kan du ytterligare begränsa åtkomst via principer för nätverks åtkomst kontroll. Mer information finns i [Azure cache för Redis med Azures privata länk](cache-private-link.md) och [hur du konfigurerar Virtual Network stöd för en Premium Azure-cache för Redis](cache-how-to-premium-vnet.md).
* **Utöknings barhet**: Enterprise-nivåer stöder [RediSearch](https://docs.redislabs.com/latest/modules/redisearch/), [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) och [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Dessa moduler lägger till nya data typer och funktioner i Redis.

Du kan skala din cache från Basic-nivån upp till Premium när den har skapats. Att skala ned till en lägre nivå stöds inte för närvarande. Stegvisa skalningsanvisningar finns i [Skala Azure Cache for Redis](cache-how-to-scale.md) och [Automatisera en skalningsåtgärd](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="enterprise-and-enterprise-flash-tier-requirements"></a>Krav för Enterprise-och Enterprise-Flash-nivå

Företags nivåerna är beroende av Redis Enterprise, en kommersiell variant av Redis från Redis Labs. Kunderna får och betalar för en licens till den här program varan via ett erbjudande för Azure Marketplace. Azure cache för Redis underlättar licens hämtningen så att du inte behöver göra det separat. För att köpa i Azure Marketplace måste du ha följande krav:
* Din Azure-prenumeration har en giltig betalnings metod. Azure-krediter eller kostnads fria MSDN-prenumerationer stöds inte.
* Du är ägare eller deltagare i prenumerationen.
* Din organisation tillåter [köp av Azure Marketplace](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases).
* Om du använder en privat Marketplace måste den innehålla Enterprise-erbjudandet för Redis Labs.

## <a name="next-steps"></a>Nästa steg
* [Skapa en Redis-cache med öppen källkod](quickstart-create-redis.md)
* [Skapa en Redis företags-cache](quickstart-create-redis-enterprise.md)
* [Använda Azure cache för Redis i en ASP.NET-webbapp](cache-web-app-howto.md)
* [Använda Azure cache för Redis i .NET Core](cache-dotnet-core-quickstart.md)
* [Använd Azure cache för Redis i .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Använd Azure cache för Redis i Node.js](cache-nodejs-get-started.md)
* [Använda Azure cache för Redis i Java](cache-java-get-started.md)
* [Använda Azure cache för Redis i python](cache-python-get-started.md)