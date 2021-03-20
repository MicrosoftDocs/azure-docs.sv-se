---
title: Felsöka överskridna tidsgränser för Azure Cache for Redis
description: Lär dig hur du löser vanliga timeout-problem med Azure cache för Redis, till exempel redis server patching och StackExchange. Redis timeout-undantag.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88213451"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Felsöka överskridna tidsgränser för Azure Cache for Redis

I det här avsnittet beskrivs fel sökning av tids gräns problem som uppstår vid anslutning till Azure cache för Redis.

- [Redis Server-uppdatering](#redis-server-patching)
- [Timeout-undantag för StackExchange. Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Flera av fel söknings stegen i den här hand boken innehåller instruktioner för att köra Redis-kommandon och övervaka olika prestanda mått. Mer information och instruktioner finns i artiklarna i avsnittet [Ytterligare information](#additional-information) .
>

## <a name="redis-server-patching"></a>Redis Server-uppdatering

Azure cache för Redis uppdaterar regelbundet sin server program vara som en del av den hanterade tjänst funktionen som den tillhandahåller. Den här [uppdaterings](cache-failover.md) aktiviteten sker i stort sett bakom scenen. Under redundansväxlingen när Redis korrigeras kan Redis-klienter som är anslutna till dessa noder uppleva tillfälliga tids gränser när anslutningar växlas mellan dessa noder. Se [Hur kan en redundansväxling påverka klient programmet](cache-failover.md#how-does-a-failover-affect-my-client-application) för mer information om vilka uppdaterings funktioner på sidan som kan ha på ditt program och hur du kan förbättra hanteringen av uppdaterings händelser.

## <a name="stackexchangeredis-timeout-exceptions"></a>Timeout-undantag för StackExchange. Redis

StackExchange. Redis använder en konfigurations inställning med namnet `synctimeout` för synkrona åtgärder med standardvärdet 5000 MS. Om ett synkront anrop inte slutförs i den här tiden, genererar StackExchange. Redis-klienten ett tids gräns fel som liknar följande exempel:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Det här fel meddelandet innehåller mått som kan hjälpa dig att peka på orsaken och möjlig lösning av problemet. Följande tabell innehåller information om måtten för fel meddelanden.

| Mått för fel meddelande | Information |
| --- | --- |
| Inst |I det senaste tillfället, sektorn: 0 kommandon har utfärdats |
| hanterare |Socket Manager gör `socket.select` , vilket innebär att den ber operativ systemet att ange en socket som har något att göra. Läsaren läser inte aktivt från nätverket eftersom det inte tycker att det finns något att göra |
| kö |Det finns 73 totala pågående åtgärder |
| qu |6 av pågående åtgärder finns i den ej skickade kön och har ännu inte skrivits till det utgående nätverket |
| qs |67 av pågående åtgärder har skickats till servern, men ett svar är ännu inte tillgängligt. Svaret kan vara `Not yet sent by the server` eller `sent by the server but not yet processed by the client.` |
| KS |0 av pågående åtgärder har läst svar men har ännu inte marker ATS som slutförda eftersom de väntar på slut för ande av slut för ande |
| WR |Det finns en aktiv skrivare (vilket innebär att 6 ej skickade förfrågningar inte ignoreras) byte/activewriters |
| in |Det finns inga aktiva läsare och inga byte som är tillgängliga för läsning på NÄTVERKSKORTets byte/activereaders |

Du kan använda följande steg för att undersöka möjliga rotor orsaker.

1. Ett bra tips är att se till att du använder följande mönster för att ansluta när du använder StackExchange. Redis-klienten.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Mer information finns i [ansluta till cachen med hjälp av stackexchange. Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Kontrol lera att servern och klient programmet finns i samma region i Azure. Du kan till exempel få tids gränser när cachen är i östra USA men klienten är i västra USA och begäran inte slutförs inom `synctimeout` intervallet, eller så kan du få tids gränser när du felsöker från den lokala utvecklings datorn. 

    Vi rekommenderar starkt att ha cachen och i klienten i samma Azure-region. Om du har ett scenario som omfattar anrop mellan regioner bör du ange `synctimeout` ett värde som är högre än standard intervallet 5000-MS genom att inkludera en `synctimeout` egenskap i anslutnings strängen. I följande exempel visas ett kodfragment till en anslutnings sträng för StackExchange. Redis som tillhandahålls av Azure cache för Redis med en `synctimeout` till 2000 MS.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Se till att du använder den senaste versionen av [stackexchange. Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar som ständigt korrigeras i koden för att göra det mer stabilt för timeout så att den senaste versionen är viktig.
1. Om dina begär Anden är kopplade till bandbredds begränsningar på servern eller klienten tar det längre tid att slutföra dem och kan orsaka timeout. Om du vill se om din tids gräns är på grund av nätverks bandbredd på servern, se [begränsning på Server sidans bandbredd](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Om du vill se om din timeout beror på klientens nätverks bandbredd kan du läsa [bandbredds begränsning på klient sidan](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Får du processor gränser på servern eller på klienten?

   - Kontrol lera om du har bundits till processor på klienten. Hög CPU kan orsaka att begäran inte bearbetas inom `synctimeout` intervallet och orsakar en tids gräns för begäran. Att flytta till en större klient storlek eller distribuera belastningen kan hjälpa dig att kontrol lera det här problemet.
   - Kontrol lera om du får CPU-bindning på servern genom att övervaka [prestanda måttet](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)för processorns cache. Begär Anden som kommer i Redis är processor gränser kan orsaka timeout för dessa begär Anden. För att åtgärda det här tillståndet kan du distribuera belastningen över flera Shards i en Premium-cache eller uppgradera till en större storleks-eller pris nivå. Mer information finns i [begränsning av bandbredd på Server sidan](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Finns det några kommandon som tar lång tid att bearbeta på servern? Tids krävande kommandon som tar lång tid att bearbeta på Redis-servern kan orsaka timeout. Mer information om tids krävande kommandon finns i [långvariga kommandon](cache-troubleshoot-server.md#long-running-commands). Du kan ansluta till Azure-cachen för Redis-instansen med hjälp av Redis-CLI-klienten eller [Redis-konsolen](cache-configure.md#redis-console). Kör sedan kommandot [SLOWLOG](https://redis.io/commands/slowlog) för att se om det finns begär Anden långsammare än förväntat. Redis-servern och StackExchange. Redis är optimerade för många små begär anden i stället för färre stora begär Anden. Att dela upp data i mindre segment kan förbättra saker här.

    Information om hur du ansluter till din Caches TLS/SSL-slutpunkt med Redis-CLI och stunnelserver finns i blogg inlägget [om ASP.net session State Provider för för hands versionen av Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. Hög redis server belastning kan orsaka timeout. Du kan övervaka Server belastningen genom att övervaka `Redis Server Load` [cachens prestanda mått](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). En server belastning på 100 (maximalt värde) betyder att Redis-servern har varit upptagen, utan att det tar tid att bearbeta begär Anden. Om du vill se om vissa begär Anden tar upp all Server kapacitet kör du kommandot SlowLog enligt beskrivningen i föregående stycke. Mer information finns i hög CPU-användning/server belastning.
1. Fanns det någon annan händelse på klient sidan som kunde ha orsakat ett nätverks-blip? Vanliga händelser är: skala upp eller ned antalet klient instanser, distribuera en ny version av klienten eller autoskalning aktive rad. I vår testning har vi upptäckt att automatisk skalning eller skalning upp/ned kan orsaka att utgående nätverks anslutning går förlorad i flera sekunder. StackExchange. Redis-koden är elastisk för sådana händelser och återansluter. Vid åter anslutning kan eventuella begär anden i kön vara timeout.
1. Fanns det en stor begäran innan flera små begär anden till cachen som nådde tids gränsen? Parametern `qs` i fel meddelandet visar hur många begär Anden som skickades från klienten till servern, men inte bearbetat något svar. Det här värdet kan fortsätta att växa eftersom StackExchange. Redis använder en enda TCP-anslutning och bara kan läsa ett svar åt gången. Även om den första åtgärden uppnåddes, stoppas inte data från att skickas till eller från servern. Andra förfrågningar kommer att blockeras tills den stora begäran har avslut ATS och kan orsaka timeout. En lösning är att minimera risken för timeout genom att se till att cachen är tillräckligt stor för arbets belastningen och dela upp stora värden i mindre segment. En annan möjlig lösning är att använda en pool med `ConnectionMultiplexer` objekt i klienten och välja det lägsta som läses in `ConnectionMultiplexer` när en ny begäran skickas. Om du läser in över flera anslutnings objekt bör du förhindra att en tids gräns går ut för andra begär Anden.
1. Om du använder `RedisSessionStateProvider` , kontrol lera att du har angett ett nytt timeout-värde på rätt sätt. `retryTimeoutInMilliseconds` måste vara högre än `operationTimeoutInMilliseconds` , annars sker inga återförsök. I följande exempel `retryTimeoutInMilliseconds` är inställt på 3000. Mer information finns i [ASP.net för session State för Azure cache för Redis](cache-aspnet-session-state-provider.md) och [hur du använder konfigurations parametrar för providern för sessionstillstånd och providern för utdatacache](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Kontrol lera minnes användningen på Azure-cachen för Redis-servern genom att [övervaka](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` och `Used Memory` . Om en princip för borttagning är på plats börjar Redis att avlägsna nycklar när `Used_Memory` den når cachestorleken. Helst `Used Memory RSS` bör helst vara något högre än `Used memory` . En stor skillnad innebär att det finns fragmentering (intern eller extern). När `Used Memory RSS` är mindre än `Used Memory` innebär det att en del av cacheminnet har bytts ut av operativ systemet. Om den här växlingen sker kan du förvänta några viktiga fördröjningar. Eftersom Redis inte har kontroll över hur dess tilldelningar mappas till minnes sidor, är hög `Used Memory RSS` ofta resultatet av en insamling i minnes användningen. När Redis-servern frigör minne, tar allokerat minne, men det kan eventuellt inte ge minnet tillbaka till systemet. Det kan finnas en avvikelse mellan det `Used Memory` värde och den minnes förbrukning som rapporteras av operativ systemet. Minnet kan ha använts och släppts av Redis men inte på datorn. Du kan undvika minnes problem genom att göra följande:

   - Uppgradera cacheminnet till en större storlek så att du inte kör mot minnes begränsningar i systemet.
   - Ange förfallo tider för nycklarna så att äldre värden tas bort proaktivt.
   - Övervaka `used_memory_rss` cache-måttet. När det här värdet närmar sig storleken på cacheminnet kommer du förmodligen att börja se prestanda problem. Distribuera data över flera Shards om du använder en Premium-cache, eller uppgradera till en större cachestorlek.

   Mer information finns i [minnes belastning på Redis-servern](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-klientsidan](cache-troubleshoot-client.md)
- [Felsöka problem på Azure Cache for Redis-serversidan](cache-troubleshoot-server.md)
- [Hur kan jag mäta och testa prestanda för mitt cacheminne?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Övervaka Azure Cache for Redis](cache-how-to-monitor.md)
