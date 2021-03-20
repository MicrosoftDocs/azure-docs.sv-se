---
title: Vanliga frågor och svar om Azure cache för Redis-planering
description: Läs om svaren på vanliga frågor som hjälper dig att planera för Azure cache för Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537413"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Vanliga frågor och svar om Azure cache för Redis-planering

Den här artikeln innehåller svar på vanliga frågor om hur du planerar för Azure cache för Redis.

## <a name="common-questions-and-answers"></a>Vanliga frågor och svar
I det här avsnittet beskrivs följande vanliga frågor och svar:

* [Azure cache för Redis-prestanda](#azure-cache-for-redis-performance)
* [I vilken region ska jag hitta mitt cacheminne?](#in-what-region-should-i-locate-my-cache)
* [Var finns mina cachelagrade data?](#where-do-my-cached-data-reside)
* [Hur faktureras jag för Azure cache för Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan jag använda Azure cache för Redis med Azure Government Cloud, Azure Kina 21Vianet-molnet eller Microsoft Azure Tyskland?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure cache för Redis-prestanda
I följande tabell visas de maximala bandbredds värden som observerats vid testning av olika storlekar på standard-och Premium-cachen med hjälp av `redis-benchmark.exe` en IaaS VM mot Azure-cachen för Redis-slutpunkten. För TLS-genomflöde används Redis-benchmark med stunnelserver för att ansluta till Azure-cachen för Redis-slutpunkten.

>[!NOTE] 
>Dessa värden är inte garanterade och det finns inget service avtal för dessa nummer, men det bör vara typiskt. Du bör läsa in testa ditt eget program för att fastställa rätt cachestorlek för ditt program.
>De här talen kan ändras när vi regelbundet publicerar nya resultat.
>

I den här tabellen kan vi Rita följande slut satser:

* Data flödet för cacheminnena som har samma storlek är högre på Premium nivån jämfört med standard nivån. Till exempel med en 6 GB cache är data flödet för P1 180 000 begär Anden per sekund (RPS) jämfört med 100 000 RPS for C3.
* Med Redis-klustring ökar data flödet linjärt när du ökar antalet Shards (noder) i klustret. Om du till exempel skapar ett P4-kluster på 10 Shards är det tillgängliga data flödet 400 000 * 10 = 4 000 000 RPS.
* Data flödet för större nyckel storlekar är högre på Premium-nivån jämfört med standard nivån.

| Prisnivå | Storlek | Processorkärnor | Tillgänglig bandbredd | värde storlek 1 KB | värde storlek 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Standardcache-storlekar** | | |**Megabit per sekund (MB/s)/megabyte per sekund (MB/s)** |**Begär Anden per sekund (RPS) icke-SSL** |**Begär Anden per sekund (RPS) SSL** |
| CO | 250 MB | Delad | 100/12,5  |  15 000 |   7 500 |
| C1 |   1 GB | 1      | 500/62,5  |  38 000 |  20 720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41 000 |  37 000 |
| C3 |   6 GB | 4      | 1000/125  | 100 000 |  90 000 |
| C4 |  13 GB | 2      | 500/62,5  |  60 000 |  55 000 |
| C5 |  26 GB | 4      | 1 000/125 | 102 000 |  93 000 |
| C6 |  53 GB | 8      | 2 000/250 | 126 000 | 120 000 |
| **Storlek på Premium-cache** | |**PROCESSOR kärnor per Shard** | **Megabit per sekund (MB/s)/megabyte per sekund (MB/s)** |**Begär Anden per sekund (RPS) som inte är SSL, per Shard** |**Begär Anden per sekund (RPS) SSL, per Shard** |
| P1 |   6 GB |  2 | 1 500/187,5 | 180 000 | 172 000 |
| P2 |  13 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P3 |  26 GB |  4 | 3 000/375   | 350 000 | 341 000 |
| P4 |  53 GB |  8 | 6 000/750   | 400,000 | 373 000 |
| P5 | 120 GB | 20 | 6 000/750   | 400,000 | 373 000 |

Anvisningar om hur du konfigurerar stunnelserver eller laddar ned Redis-verktyg som finns `redis-benchmark.exe` i [Hur kan jag köra Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>I vilken region ska jag hitta mitt cacheminne?
För bästa prestanda och lägsta latens, letar du upp Azure-cachen för Redis i samma region som ditt cache-klientcertifikat.

### <a name="where-do-my-cached-data-reside"></a>Var finns mina cachelagrade data?
Azure cache för Redis lagrar dina program data i RAM-minnet för virtuella datorer eller virtuella datorer, beroende på nivån, som är värd för din cache. Dina data finns endast i den Azure-region som du har valt som standard. Det finns två fall där dina data kan lämna en region:
* När du aktiverar persistence i cacheminnet, kommer Azure cache för Redis att säkerhetskopiera dina data till ett Azure Storage-konto som du äger. Om det lagrings konto som du anger ska finnas i en annan region, kommer en kopia av dina data att sluta fungera där.
* Om du ställer in geo-replikering och din sekundära cache finns i en annan region, vilket är fallet normalt, kommer dina data att replikeras till den regionen.

Du måste uttryckligen konfigurera Azure cache för Redis att använda dessa funktioner. Du har också fullständig kontroll över den region där lagrings kontot eller den sekundära cachen finns.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hur faktureras jag för Azure cache för Redis?
Azure cache för Redis-priser finns [här](https://azure.microsoft.com/pricing/details/cache/). Pris sidan visar pris per timme och månads pris. Cache debiteras per minut från den tidpunkt då cacheminnet skapas tills en cache tas bort. Det finns inget alternativ för att stoppa eller pausa faktureringen av en cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Kan jag använda Azure cache för Redis med Azure Government Cloud, Azure Kina 21Vianet-molnet eller Microsoft Azure Tyskland?
Ja, Azure cache för Redis finns i Azure Government Cloud, Azure Kina 21Vianet-molnet och Microsoft Azure Tyskland. URL: erna för åtkomst och hantering av Azure-cache för Redis skiljer sig åt i dessa moln jämfört med Azures offentliga moln.

| Moln   | DNS-suffix för Redis            |
|---------|---------------------------------|
| Offentliga  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Tyskland | *. redis.cache.cloudapi.de       |
| Kina   | *. redis.cache.chinacloudapi.cn  |

Mer information om att tänka på när du använder Azure cache för Redis med andra moln finns i följande länkar.

- [Azure Government-databaser – Azure cache för Redis](../azure-government/compare-azure-government-global-azure.md)
- [Azure Kina 21Vianet-moln – Azure cache för Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/)

Information om hur du använder Azure cache för Redis med PowerShell i Azure Government Cloud, Azure Kina 21Vianet-molnet och Microsoft Azure Tyskland finns i [så här ansluter du till andra moln – Azure cache för Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Azure cache för vanliga frågor och svar om Redis](cache-faq.md).