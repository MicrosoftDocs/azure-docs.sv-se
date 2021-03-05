---
title: Bästa praxis för Azure Cache for Redis
description: Lär dig hur du använder Azure-cachen för Redis effektivt genom att följa dessa metod tips.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 9754a043c90c01f889be9639d2d045fb1929de17
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178124"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Bästa praxis för Azure Cache for Redis 
Genom att följa dessa rekommendationer kan du maximera prestanda och kostnads effektiv användning av Azure-cachen för Redis-instansen.

## <a name="configuration-and-concepts"></a>Konfiguration och begrepp
 * **Använd standard-eller Premium nivån för produktions system.**  Basic-nivån är ett enda Node-system utan datareplikering och inget service avtal. Använd också minst ett C1-cacheminne.  C0-cachen är avsedda för enkla utvecklings-och test scenarier eftersom de har en delad processor kärna, lite minne och är känsliga för problem med "störningar i grannen".

 * **Kom ihåg att Redis är ett minnes intern data lager.**  [Den här artikeln](cache-troubleshoot-data-loss.md) beskriver några scenarier där data förlust kan uppstå.

 * **Utveckla ditt system så att det kan hantera anslutnings signaler** [på grund av korrigeringar och redundans](cache-failover.md).

 * **Konfigurera [maxmemory-reserverad-inställningen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för att förbättra systemets svars tider** under minnes trycks villkor.  En tillräcklig reservations inställning är särskilt viktig för Skriv tunga arbets belastningar eller om du lagrar större värden (100 KB eller mer) i Redis. Du bör börja med 10% av storleken på cachen och öka den här procent andelen om du har Skriv tung belastningar.

 * **Redis fungerar bäst med mindre värden**, så Överväg att chopping upp större data i flera nycklar.  I [den här Redis-diskussionen](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)visas några saker som du bör tänka på noggrant.  I [den här artikeln](cache-troubleshoot-client.md#large-request-or-response-size) finns ett exempel på problem som kan orsakas av stora värden.

 * **Leta upp din cache-instans och ditt program i samma region.**  Om du ansluter till en cache i en annan region kan latensen öka och tillförlitligheten minska avsevärt.  Även om du kan ansluta från utanför Azure rekommenderas du inte *särskilt när du använder Redis som cache*.  Om du använder Redis som ett nyckel/värde-lager, är det inte säkert att fördröjningen är den primära. 

 * **Återanvänd anslutningar.**  Att skapa nya anslutningar är dyrt och ökar svars tiden, så Återanvänd anslutningar så mycket som möjligt. Om du väljer att skapa nya anslutningar ser du till att stänga de gamla anslutningarna innan du släpper dem (även i hanterade minnes språk som .NET eller Java).

 * **Konfigurera klient biblioteket för att använda en *anslutnings-timeout* på minst 15 sekunder**, vilket ger system tiden att ansluta även under högre CPU-förhållanden.  Ett timeout-värde för små anslutningar garanterar inte att anslutningen upprättas inom den tids perioden.  Om något går fel (hög klient processor, hög server processor och så vidare), kommer ett kort tids gräns värde för anslutningen orsaka att anslutnings försöket Miss lyckas. Det här beteendet gör ofta en dålig situation sämre.  I stället för att hjälpa, förvärrar kortare timeout-problem genom att tvinga systemet att starta om processen att försöka återansluta, vilket kan leda till en *anslutnings > fel-> försök igen* . Vi rekommenderar vanligt vis att du lämnar tids gränsen för anslutningen till 15 sekunder eller högre. Det är bättre att låta ditt anslutnings försök utföras efter 15 eller 20 sekunder än att det inte går att utföra det snabbt. En sådan slinga för återförsök kan orsaka att ditt avbrott blir längre än om du låter systemet ta längre tid.  
     > [!NOTE]
     > Den här vägledningen är unik för *anslutnings försöket* och är inte relaterat till den tid som du är redo att vänta på en *åtgärd* som Get eller set att slutföra.

 * **Undvik dyra åtgärder** – vissa redis-åtgärder, till exempel kommandot [nycklar](https://redis.io/commands/keys) , är *mycket* dyra och bör undvikas.  Mer information finns i några överväganden om [tids krävande kommandon](cache-troubleshoot-server.md#long-running-commands)

 * **Använd TLS-kryptering** – Azure cache för REDIS kräver TLS-krypterad kommunikation som standard.  TLS-versionerna 1,0, 1,1 och 1,2 stöds för närvarande.  TLS 1,0 och 1,1 finns dock på en sökväg till föråldrad bransch, så Använd TLS 1,2 om det är möjligt.  Om klient biblioteket eller verktyget inte stöder TLS kan du göra [det genom att](cache-configure.md#access-ports) aktivera okrypterade anslutningar via [API: erna](/rest/api/redis/redis/update)för Azure Portal eller hantering.  I sådana fall där det inte går att kryptera anslutningar kan du rekommendera att placera cacheminnet och klient programmet i ett virtuellt nätverk.  Mer information om vilka portar som används i det virtuella nätverkets cache-scenario finns i den här [tabellen](cache-how-to-premium-vnet.md#outbound-port-requirements).

 * **Tids gräns för inaktivitet** – Azure Redis har för närvarande 10 minuters inaktivitet för anslutningar, så detta ska vara mindre än 10 minuter.

## <a name="memory-management"></a>Minnes hantering
Det finns flera saker som rör minnes användningen i Redis-serverinstansen som du kanske vill överväga.  Här är några:

 * **Välj en [borttagnings princip](https://redis.io/topics/lru-cache) som fungerar för ditt program.**  Standard principen för Azure Redis är *volatile-LRU*, vilket innebär att endast nycklar som har ett TTL-värde har angetts för borttagning.  Om inga nycklar har ett TTL-värde kommer systemet inte att avlägsna några nycklar.  Om du vill att systemet ska tillåta att en nyckel tas bort om den är under minnes belastning, kan du överväga att använda *allkeys-LRU-* principen.

 * **Ange ett förfallo värde för dina nycklar.**  Ett förfallo datum kommer att ta bort nycklar proaktivt i stället för att vänta tills det finns minnes belastning.  När avlägsnandet sker på grund av minnes belastning kan det orsaka ytterligare belastning på servern.  Mer information finns i dokumentationen för kommandot [Expires](https://redis.io/commands/expire) och [EXPIREAT](https://redis.io/commands/expireat) .

## <a name="client-library-specific-guidance"></a>Vägledning för klient biblioteks information
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-vilken klient ska jag använda?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sallat (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provider för ASP.NET-sessionstillstånd](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>När är det säkert att försöka igen?
Tyvärr finns det inget enkelt svar.  Varje program måste bestämma vilka åtgärder som kan göras om och vilket inte kan göras.  Varje åtgärd har olika krav och beroenden mellan nycklar.  Här följer några saker som du kan tänka på:

 * Du kan få fel på klient sidan även om Redis har kört kommandot som du bad om att köra.  Exempel:
    - Tids gränser är ett koncept på klient sidan.  Om åtgärden har nått servern kör servern kommandot även om klienten ger väntande svar.  
    - När ett fel uppstår på socket-anslutningen är det inte möjligt att veta om åtgärden faktiskt kördes på servern.  Anslutnings felet kan till exempel inträffa efter att servern bearbetat begäran, men innan klienten får svaret.
 * Hur reagerar mitt program om jag råkar köra samma åtgärd två gånger?  Vad händer om jag exempelvis ökar ett heltal två gånger i stället för en gång?  Skrivs mitt program till samma nyckel från flera platser?  Vad händer om min omprövnings logik skriver över ett värde som har angetts av någon annan del av min app?

Om du vill testa hur koden fungerar under fel tillstånd, kan du överväga att använda [funktionen för omstart](cache-administration.md#reboot). Genom att starta om kan du se hur anslutnings signaler påverkar ditt program.

## <a name="performance-testing"></a>Prestandatestning
 * **Börja med att `redis-benchmark.exe` använda** för att få en känsla för möjliga data flöde/svars tider innan du skriver dina egna perf-tester.  Redis – benchmark-dokumentation [finns här](https://redis.io/topics/benchmarks).  Observera att Redis-benchmark inte stöder TLS, så du måste [Aktivera icke-TLS-porten via portalen](cache-configure.md#access-ports) innan du kör testet.  [Du hittar en Windows-kompatibel version av redis-benchmark.exe här](https://github.com/MSOpenTech/redis/releases)
 * Den virtuella klient datorn som används för testning ska finnas **i samma region** som din Redis-cache-instans.
 * **Vi rekommenderar att du använder Dv2 VM-serien** för din klient eftersom de har bättre maskin vara och ger bästa möjliga resultat.
 * Kontrol lera att den virtuella klient datorn som du använder har * minst *lika mycket data bearbetning och bandbredd* som det cacheminne som testas. 
 * **Testa under växlings villkor** i cacheminnet. Det är viktigt att se till att du inte testar ditt cacheminne enbart under stabila tillstånds villkor. Testa också under failover-villkor och mät belastningen på CPU/server i cacheminnet under den tiden. Du kan initiera en redundansväxling genom [att starta om den primära noden](cache-administration.md#reboot). På så sätt kan du se hur programmet beter sig med avseende på data flöde och svars tid under redundansväxlingen (sker under uppdateringar och kan ske under en oplanerad händelse). Vi rekommenderar att du vill se belastningen på CPU/Server-belastningen till mer än 80% även under en redundansväxling, eftersom detta kan påverka prestandan.
 * **Premium P2 och ovan** finns på virtuella datorer med 4 eller fler kärnor. Detta är användbart för att distribuera arbets belastningen TLS-kryptering/dekryptering över flera kärnor för att öka den totala processor användningen.  [Här finns mer information kring VM-storlekar och kärnor](cache-planning-faq.md#azure-cache-for-redis-performance)
 * **Aktivera VRSS** på klient datorn om du använder Windows.  [Mer information finns här](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Exempel på PowerShell-skript:
     >PowerShell – ExecutionPolicy obegränsad Enable-NetAdapterRSS-namn (Get-netadapter). Namn 

 * **Överväg att använda Redis-instanser på Premium-nivå**.  Dessa cachestorlek har bättre nätverks svars tid och data flöde eftersom de körs på bättre maskin vara för både processor och nätverk.

     > [!NOTE]
     > Våra observerade prestanda resultat [publiceras här](cache-planning-faq.md#azure-cache-for-redis-performance) för din referens.   Tänk också på att SSL/TLS lägger till viss overhead, så att du kan få olika fördröjningar och/eller data flöden om du använder transport kryptering.

### <a name="redis-benchmark-examples"></a>Redis-Benchmark exempel
**Före test konfiguration**: Förbered cache-instansen med data som krävs för svar på svars tid och data flöde som visas nedan.
> Redis – prestandatest – h yourcache.redis.cache.windows.net – a yourAccesskey-t SET-n 10-d 1024 

**Så här testar du svars tiden**: testa get-begäranden med en last på 1 KB.
> Redis – prestandatest-h yourcache.redis.cache.windows.net – a yourAccesskey-t GET-d 1024-P 50-c 4

**Testa data flödet:** Pipeline GET-begäranden med nytto lasten 1 KB.
> Redis – prestandatest-h yourcache.redis.cache.windows.net – a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50