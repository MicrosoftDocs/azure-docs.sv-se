---
title: Felsöka problem på Azure Cache for Redis-klientsidan
description: Lär dig hur du löser vanliga problem på klient sidan med Azure cache för Redis, till exempel Redis-klientens minnes tryck, trafik burst, hög CPU, begränsad bandbredd, stora begär Anden eller stora svars storlekar.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008975"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Felsöka problem på Azure Cache for Redis-klientsidan

Det här avsnittet beskriver fel söknings problem som uppstår på grund av ett villkor på Redis-klienten som används av programmet.

- [Minnes belastning på Redis-klienten](#memory-pressure-on-redis-client)
- [Trafik burst](#traffic-burst)
- [Hög CPU-användning för klienter](#high-client-cpu-usage)
- [Bandbredds begränsning på klient Sidan](#client-side-bandwidth-limitation)
- [Storlek på stor begäran eller svars tid](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Minnes belastning på Redis-klienten

Minnes belastningen på klient datorn leder till alla typer av prestanda problem som kan fördröja bearbetningen av svar från cachen. När minnes trycks träffar kan systemet använda data på disk. Den här _sidfel_ gör att systemet saktas ned avsevärt.

Identifiera minnes belastning på klienten:

- Övervaka minnes användningen på datorn för att säkerställa att den inte överskrider det tillgängliga minnet.
- Övervaka klientens `Page Faults/Sec` prestanda räknare. Under normal drift har de flesta system vissa sidfel. Toppar i sidfel i samband med timeouter för begäranden kan indikera minnestryck.

Hög minnes belastning på klienten kan begränsas på flera sätt:

- Använd mönster för minnes användning för att minska minnes användningen på klienten.
- Uppgradera den virtuella klient datorn till en större storlek med mer minne.

## <a name="traffic-burst"></a>Trafik burst

Burst-trafik i kombination med dåliga `ThreadPool` inställningar kan leda till fördröjningar i behandlings data som redan har skickats av Redis-servern men som ännu inte har använts på klient sidan.

Övervaka hur `ThreadPool` statistiken förändras över tid med hjälp av [ett `ThreadPoolLogger` exempel ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Du kan använda  `TimeoutException` meddelanden från stackexchange. Redis som nedan för att undersöka:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

I föregående undantag finns det flera problem som är intressanta:

- Observera att i `IOCP` avsnittet och `WORKER` avsnittet har du ett `Busy` värde som är större än `Min` värdet. Den här skillnaden innebär att `ThreadPool` inställningarna behöver justeras.
- Du kan också se `in: 64221` . Det här värdet anger att 64 211 byte har mottagits på klientens kernel socket-lager men inte lästs av programmet. Den här skillnaden innebär vanligt vis att ditt program (t. ex. StackExchange. Redis) inte läser data från nätverket så snabbt som servern skickar det till dig.

Du kan [Konfigurera dina `ThreadPool` Inställningar](cache-management-faq.md#important-details-about-threadpool-growth) för att se till att trådpoolen skalas snabbt under burst-scenarier.

## <a name="high-client-cpu-usage"></a>Hög CPU-användning för klienter

Hög CPU-användning för klienter anger att systemet inte kan fortsätta med det arbete som den har blivit ombedd att göra. Även om cachen skickade svaret snabbt, kan klienten Miss lyckas med att bearbeta svaret inom rimlig tid.

Övervaka klientens processor användning på hela systemet med hjälp av mått som är tillgängliga i Azure Portal eller via prestanda räknare på datorn. Var noga med att inte övervaka *process* -CPU eftersom en enda process kan ha låg processor användning, men hela systemets CPU kan vara hög. Titta efter toppar i CPU-användning som motsvarar tids gränser. Hög processor kan också orsaka höga `in: XXX` värden i `TimeoutException` fel meddelanden enligt beskrivningen i [Traffic burst](#traffic-burst) -avsnittet.

> [!NOTE]
> StackExchange. Redis 1.1.603 och senare innehåller `local-cpu` måttet i `TimeoutException` fel meddelanden. Se till att du använder den senaste versionen av [stackexchange. Redis NuGet-paketet](https://www.nuget.org/packages/StackExchange.Redis/). Det finns buggar som ständigt korrigeras i koden för att göra det mer stabilt för timeout så att den senaste versionen är viktig.
>

Minimera en klients höga CPU-användning:

- Undersök vad som orsakar CPU-toppar.
- Uppgradera klienten till en större VM-storlek med mer processor kapacitet.

## <a name="client-side-bandwidth-limitation"></a>Bandbredds begränsning på klient Sidan

Beroende på klient datorernas arkitektur kan de ha begränsningar för hur mycket nätverks bandbredd de har tillgängliga. Om klienten överskrider den tillgängliga bandbredden genom att överbelasta nätverks kapaciteten bearbetas inte data på klient sidan så snabbt som servern skickar den. Den här situationen kan leda till timeout.

Övervaka hur bandbredds användningen förändras över tid med hjälp av [ett exempel `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Den här koden kanske inte kan köras i vissa miljöer med begränsade behörigheter (t. ex. Azure Web Sites).

Minska, minska användningen av nätverks bandbredd eller öka storleken på klientens virtuella dator till en med mer nätverks kapacitet.

## <a name="large-request-or-response-size"></a>Storlek på stor begäran eller svars tid

En stor begäran/svar kan orsaka timeout. Anta till exempel att ditt timeout-värde som kon figurer ATS på klienten är 1 sekund. Ditt program begär två nycklar (till exempel "A" och "B") samtidigt (med samma fysiska nätverks anslutning). De flesta klienter stöder begäran "pipelining" där både begär Anden "A" och "B" skickas efter den andra utan att vänta på deras svar. Servern skickar Svaren tillbaka i samma ordning. Om svaret "A" är stort kan det få ut mesta möjliga av tids gränsen för senare förfrågningar.

I följande exempel skickas begäran "A" och "B" snabbt till-servern. Servern börjar skicka Svaren "A" och "B" snabbt. På grund av data överförings tider måste svars-B vänta efter svars tiden "A", trots att servern svarade snabbt.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Den här begäran/svaret är ett svårt att mäta. Du kan Instrumenta din klient kod för att spåra stora förfrågningar och svar.

Lösningar för stora svars storlekar varierar, men inkluderar:

1. Optimera ditt program för ett stort antal små värden i stället för några få stora värden.
    - Den bästa lösningen är att dela upp data i relaterade mindre värden.
    - Se inlägget [Vad är det idealiska värde storleks intervallet för Redis? Är 100 KB för stort?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Mer information om varför mindre värden rekommenderas.
1. Öka storleken på den virtuella datorn för att få högre bandbredds kapacitet
    - Mer bandbredd på din klient eller virtuell serverdator kan minska data överförings tiden för större svar.
    - Jämför den aktuella nätverks användningen på båda datorerna med gränserna för din aktuella VM-storlek. Mer bandbredd på bara servern eller bara på klienten är kanske inte tillräckligt.
1. Öka antalet anslutnings objekt som programmet använder.
    - Använd en metod för resursallokering för att göra förfrågningar över olika anslutnings objekt.

## <a name="additional-information"></a>Ytterligare information

- [Felsöka problem på Azure Cache for Redis-serversidan](cache-troubleshoot-server.md)
- [Hur kan jag mäta och testa prestanda för mitt cacheminne?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
