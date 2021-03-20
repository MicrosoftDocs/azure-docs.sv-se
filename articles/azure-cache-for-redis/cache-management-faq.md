---
title: Vanliga frågor och svar om Azure cache för Redis Management
description: Läs om svaren på vanliga frågor som hjälper dig att hantera Azure cache för Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537668"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Vanliga frågor och svar om Azure cache för Redis Management
Den här artikeln innehåller svar på vanliga frågor om hur du hanterar Azure cache för Redis.

## <a name="common-questions-and-answers"></a>Vanliga frågor och svar
I det här avsnittet beskrivs följande vanliga frågor och svar:

* [När ska jag aktivera icke-TLS/SSL-porten för anslutning till Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Vad är några produktions metod tips?](#what-are-some-production-best-practices)
* [Vad är några av överväganden när du använder vanliga Redis-kommandon?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hur kan jag mäta och testa prestanda för mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Viktig information om hur du växer i trådpool](#important-details-about-threadpool-growth)
* [Aktivera server-GC för att få mer data flöde på klienten när du använder StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestanda överväganden kring anslutningar](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>När ska jag aktivera icke-TLS/SSL-porten för anslutning till Redis?
Redis-servern har inte inbyggt stöd för TLS, men Azure cache för Redis gör det. Om du ansluter till Azure cache för Redis och klienten stöder TLS, t. ex. StackExchange. Redis, ska du använda TLS.

>[!NOTE]
>Icke-TLS-porten är inaktive rad som standard för nya Azure-cache för Redis-instanser. Om klienten inte stöder TLS måste du aktivera icke-TLS-porten genom att följa anvisningarna i avsnittet [åtkomst portar](cache-configure.md#access-ports) i artikeln [Konfigurera en cache i Azure cache för Redis](cache-configure.md) .
>
>

Redis-verktyg som `redis-cli` inte fungerar med TLS-porten, men du kan använda ett verktyg för `stunnel` att på ett säkert sätt ansluta verktygen till TLS-porten genom att följa anvisningarna i avsnittet [presentera ASP.net för Redis för för hands version](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) .

Instruktioner för hur du hämtar Redis-verktygen finns i avsnittet [Hur kan jag köra Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands) .

### <a name="what-are-some-production-best-practices"></a>Vad är några produktions metod tips?
* [Metod tips för StackExchange. Redis](#stackexchangeredis-best-practices)
* [Konfiguration och begrepp](#configuration-and-concepts)
* [Prestandatestning](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Metod tips för StackExchange. Redis
* Ange `AbortConnect` till false och låt ConnectionMultiplexer återansluta automatiskt. [Mer information finns här](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Återanvänd ConnectionMultiplexer – skapa inte någon ny för varje begäran. `Lazy<ConnectionMultiplexer>`Mönstret som [visas här](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) rekommenderas.
* Redis fungerar bäst med mindre värden, så Överväg att chopping upp större data i flera nycklar. I [den här Redis-diskussionen](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)betraktas 100 kB som stor. I [den här artikeln](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) finns ett exempel på problem som kan orsakas av stora värden.
* Konfigurera dina [Inställningar för trådpool](#important-details-about-threadpool-growth) för att undvika tids gränser.
* Använd minst standard connectTimeout på 5 sekunder. Intervallet ger StackExchange. Redis tillräckligt med tid för att återupprätta anslutningen i händelse av ett nätverks blip.
* Tänk på de prestanda kostnader som är kopplade till olika åtgärder som du kör. Till exempel `KEYS` är kommandot en O (n)-åtgärd och bör undvikas. [Redis.io-webbplatsen](https://redis.io/commands/) innehåller information kring tids komplexiteten för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.

#### <a name="configuration-and-concepts"></a>Konfiguration och begrepp
* Använd standard-eller Premium nivån för produktions system. Basic-nivån är ett system med en nod utan datareplikering och serviceavtal. Använd också minst ett C1-cacheminne. C0-cache används vanligt vis för enkla utvecklings-och test scenarier.
* Kom ihåg att Redis är ett **minnes intern** data lager. Läs [den här artikeln](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) så att du är medveten om scenarier där data förlust kan uppstå.
* Utveckla ditt system så att det kan hantera anslutnings signaler [på grund av korrigeringar och redundans](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestandatestning
* Börja med `redis-benchmark.exe` att använda för att få en känsla för eventuella data flöden innan du skriver dina egna perf-tester. Eftersom inte `redis-benchmark` stöder TLS måste du [Aktivera icke-TLS-porten via Azure Portal](cache-configure.md#access-ports) innan du kör testet. Exempel finns i [Hur kan jag mäta och testa prestanda för mitt cacheminne?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Den virtuella klient datorn som används för testning ska finnas i samma region som Azure-cachen för Redis-instansen.
* Vi rekommenderar att du använder Dv2 VM-serien för din klient eftersom de har bättre maskin vara och ger bästa möjliga resultat.
* Kontrol lera att den virtuella klient datorn du väljer har minst lika mycket data behandlings-och bandbredds kapacitet som det cacheminne som du testar.
* Aktivera VRSS på klient datorn om du använder Windows. [Mer information finns här](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Redis-instanser på Premium-nivå har bättre nätverks svars tid och data flöde eftersom de körs på bättre maskin vara för både CPU och nätverk.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Vad är några av överväganden när du använder vanliga Redis-kommandon?

* Undvik att använda vissa redis-kommandon som tar lång tid att slutföra, om du inte helt förstår effekten av dessa kommandon. Kör till exempel inte kommandot [nycklar](https://redis.io/commands/keys) i produktion. Beroende på antalet nycklar kan det ta lång tid att returnera. Redis är en enkel tråds Server och bearbetar kommandon en i taget. Om du har andra kommandon som utfärdas efter nycklar bearbetas de inte förrän Redis bearbetar kommandot nycklar. [Redis.io-webbplatsen](https://redis.io/commands/) innehåller information kring tids komplexiteten för varje åtgärd som stöds. Klicka på varje kommando för att se komplexiteten för varje åtgärd.
* Nyckel storlekar – ska jag använda små nycklar/värden eller stora nycklar/värden? Det beror på scenariot. Om ditt scenario kräver större nycklar kan du justera ConnectionTimeout och sedan försöka igen och justera din logik för återförsök. Från ett redis server-perspektiv ger mindre värden bättre prestanda.
* Dessa överväganden innebär inte att du inte kan lagra större värden i Redis; Du måste vara medveten om följande överväganden. Fördröjningen är högre. Om du har en uppsättning data som är större och en som är mindre, kan du använda flera ConnectionMultiplexer-instanser, var och en som kon figurer ATS med en annan uppsättning tids gräns och nya försök, enligt beskrivningen i föregående [vad gör avsnittet stackexchange. Redis konfigurations alternativ](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) .

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hur kan jag mäta och testa prestanda för mitt cacheminne?
* [Aktivera cachediagnostik](cache-how-to-monitor.md#enable-cache-diagnostics) så att du kan [övervaka](cache-how-to-monitor.md) hälsotillståndet för cacheminnet. Du kan visa måtten i Azure Portal och du kan också [Hämta och granska](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) dem med hjälp av de verktyg du väljer.
* Du kan använda redis-benchmark.exe för att läsa in testa din redis-server.
* Se till att belastnings test klienten och Azure-cachen för Redis finns i samma region.
* Använd redis-cli.exe och övervaka cachen med hjälp av kommandot INFO.
* Om din belastning orsakar hög fragmentering i minnet, bör du skala upp till en större cachestorlek.
* Instruktioner för hur du hämtar Redis-verktygen finns i avsnittet [Hur kan jag köra Redis-kommandon?](cache-development-faq.md#how-can-i-run-redis-commands) .

Följande kommandon innehåller ett exempel på hur du använder redis-benchmark.exe. Kör dessa kommandon från en virtuell dator i samma region som ditt cacheminne för korrekta resultat.

* Testa Pipeliniska UPPSÄTTNINGs begär Anden med en last i 1 KB

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testa pipeline GET-begäranden med en nytto last på 1 KB.

>[!NOTE]
> Kör det UPPSÄTTNINGs test som visas ovan först för att fylla i cachen
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Viktig information om hur du växer i trådpool
CLR-trådpool har två typer av trådar – "Work" och "I/O-sluten port" (IOCP) trådar.

* Arbets trådar används för saker som att bearbeta `Task.Run(…)` metoderna, eller `ThreadPool.QueueUserWorkItem(…)` . Dessa trådar används också av olika komponenter i CLR när arbetet måste inträffa i en bakgrunds tråd.
* IOCP-trådar används när asynkront IO sker, till exempel vid läsning från nätverket.

Trådpoolen ger nya arbets trådar eller I/O-slutförande-trådar på begäran (utan begränsning) tills den når inställningen "minimum" för varje typ av tråd. Som standard anges det minsta antalet trådar som är antalet processorer i ett system.

När antalet befintliga (upptagna) trådar träffar "minsta antal trådar", kommer trådpoolen att reglera den hastighet med vilken den infogar nya trådar till en tråd per 500 millisekunder. Normalt, om ditt system får en burst av arbete som behöver en IOCP-tråd, kommer den att bearbeta arbetet snabbt. Men om arbets belastningen är mer än den konfigurerade "lägsta"-inställningen kommer det att finnas en fördröjning vid bearbetningen av en del av arbetet eftersom trådpoolen väntar på att ett av två saker sker.

* En befintlig tråd blir fri att bearbeta arbetet.
* Ingen befintlig tråd blir kostnads fri för 500 ms, så en ny tråd skapas.

I princip innebär det att när antalet upptagna trådar är större än minsta antal trådar betalar du troligen en 500-ms-fördröjning innan nätverks trafiken bearbetas av programmet. Det är också viktigt att Observera att när en befintlig tråd är inaktiv i mer än 15 sekunder (baserat på vad jag minns) kommer den att rensas och den här cykeln för tillväxt och krympning kan upprepas.

Om vi tittar på ett exempel fel meddelande från StackExchange. Redis (build 1.0.450 eller senare), kommer du att se att den nu skriver ut trådpool-statistik (se IOCP och WORKer-information nedan).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

I det tidigare exemplet kan du se att för IOCP-tråden det finns sex upptagna trådar och att systemet är konfigurerat att tillåta fyra minsta trådar. I det här fallet skulle klienten troligt vis sett 2 500-ms fördröjningar, eftersom 6 > 4.

Observera att StackExchange. Redis kan trycka på timeout om tillväxten för antingen IOCP eller arbets trådar blir begränsad.

#### <a name="recommendation"></a>Rekommendation

Med hänsyn till den här informationen rekommenderar vi starkt att kunderna anger det minsta konfiguration svärdet för IOCP och arbets trådar till något större än standardvärdet. Vi kan inte ge en storleks topp – all vägledning om vad det här värdet ska bero på att rätt värde för ett program sannolikt är för högt eller lågt för ett annat program. Den här inställningen kan också påverka prestandan för andra delar av komplexa program, så att varje kund måste finjustera den här inställningen efter deras specifika behov. En bra start punkt är 200 eller 300 och sedan testa och justera efter behov.

Så här konfigurerar du den här inställningen:

* Vi rekommenderar att du ändrar den här inställningen program mässigt genom att använda filen [trådpool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) i `global.asax.cs` . Exempel:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Värdet som anges av den här metoden är en global inställning som påverkar hela AppDomain. Om du till exempel har en dator med 4 kärnor och vill ställa in *minWorkerThreads* och *minIoThreads* på 50 per processor under körningen använder du **trådpool. SetMinThreads (200, 200)**.

* Du kan också ange minsta antal trådar genom att använda [konfigurations inställningen *MinIoThreads* eller *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) under `<processModel>` konfigurations elementet i, som `Machine.config` vanligt vis finns på `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Att ställa in antalet minsta trådar på det här sättet rekommenderas vanligt vis inte, eftersom det är en systemtäckande inställning.**

  > [!NOTE]
  > Värdet som anges i det här konfigurations elementet är en inställning *per kärna* . Om du till exempel har en dator med 4 kärnor och vill att *minIoThreads* -inställningen ska vara 200 vid körning använder du `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivera server-GC för att få mer data flöde på klienten när du använder StackExchange. Redis
Att aktivera server GC kan optimera klienten och tillhandahålla bättre prestanda och data flöde när du använder StackExchange. Redis. Mer information om Server GC och hur du aktiverar den finns i följande artiklar:

* [Så här aktiverar du Server GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Grunderna i skräp insamling](/dotnet/standard/garbage-collection/fundamentals)
* [Skräp insamling och prestanda](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Prestanda överväganden kring anslutningar

Varje pris nivå har olika gränser för klient anslutningar, minne och bandbredd. Varje cache-storlek tillåter *upp till* ett visst antal anslutningar, och varje anslutning till Redis har kopplad till sig. Ett exempel på sådana kostnader är CPU och minnes användning som ett resultat av TLS/SSL-kryptering. Den maximala anslutnings gränsen för en specifik cachestorlek förutsätter en lätt inläst cache. Om belastningen från anslutnings belastning *plus* belastning från klient åtgärder överskrider kapaciteten för systemet, kan cachen uppleva kapacitets problem även om du inte har överskridit anslutnings gränsen för den aktuella cachestorleken.

Mer information om olika anslutnings begränsningar för varje nivå finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). Mer information om anslutningar och andra standardkonfigurationer finns i [standard konfigurationen för Redis-servern](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Azure cache för vanliga frågor och svar om Redis](cache-faq.md).