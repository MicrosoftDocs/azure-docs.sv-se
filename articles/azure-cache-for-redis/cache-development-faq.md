---
title: Vanliga frågor och svar om Azure cache för Redis-utveckling
description: Läs om svaren på vanliga frågor som hjälper dig att utveckla för Azure cache för Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585776"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Vanliga frågor och svar om Azure cache för Redis-utveckling

Den här artikeln innehåller svar på vanliga frågor om hur du utvecklar för Azure cache för Redis.

## <a name="common-questions-and-answers"></a>Vanliga frågor och svar
I det här avsnittet beskrivs följande vanliga frågor och svar:

* [Hur kan jag komma igång med Azure cache för Redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Vad är konfigurations alternativen för StackExchange. Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Vilken Azure-cache för Redis-klienter kan jag använda?](#what-azure-cache-for-redis-clients-can-i-use)
* [Finns det en lokal emulator för Azure cache för Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hur kan jag köra Redis-kommandon?](#how-can-i-run-redis-commands)
* [Varför har inte Azure cache för Redis någon referens för MSDN-klass bibliotek?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Kan jag använda Azure cache för Redis som ett PHP-sessionsnamn?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Vad är Redis-databaser?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hur kan jag komma igång med Azure cache för Redis?
Det finns flera sätt att komma igång med Azure cache för Redis.

* Du kan titta på någon av våra självstudier som är tillgängliga för [.net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)och [python](cache-python-get-started.md).
* Du kan se [hur du skapar High-Performance appar med hjälp av Microsoft Azure cache för Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Du kan läsa klient dokumentationen för de klienter som matchar ditt projekts utvecklings språk och se hur du använder Redis. Det finns många Redis-klienter som kan användas med Azure cache för Redis. En lista över Redis-klienter finns i [https://redis.io/clients](https://redis.io/clients) .

Om du inte redan har ett Azure-konto kan du:

* [Öppna ett Azure-konto kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda kostnadsfria Azure-tjänster och -funktioner.
* [Aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Din MSDN-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Vad är konfigurations alternativen för StackExchange. Redis?
StackExchange. Redis har många alternativ. I det här avsnittet beskrivs några av de vanliga inställningarna. Mer detaljerad information om StackExchange. Redis-alternativ finns i [stackexchange. Redis-konfiguration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Beskrivning | Rekommendation |
| --- | --- | --- |
| AbortOnConnectFail |När värdet är true kommer anslutningen inte att återanslutas efter ett nätverks haveri. |Ange till false och låt StackExchange. Redis ansluta automatiskt. |
| ConnectRetry |Antalet gånger som anslutnings försöken upprepas under den första anslutningen. |Se följande information om vägledning. |
| ConnectTimeout |Timeout i MS för Connect-åtgärder. |Se följande information om vägledning. |

Vanligt vis räcker standardvärdena för-klienten. Du kan finjustera alternativen utifrån din arbets belastning.

* **Antal försök**
  * I ConnectRetry och ConnectTimeout är den allmänna vägledningen att sluta fungera snabbt och försöka igen. Den här vägledningen baseras på arbets belastningen och hur lång tid det tar för din klient att utfärda ett Redis-kommando och få ett svar.
  * Låt StackExchange. Redis återansluta automatiskt i stället för att kontrol lera anslutnings status och ansluta igen. **Undvik att använda egenskapen ConnectionMultiplexer. IsConnected**.
  * Snowballing – ibland kan du stöta på ett problem där du försöker igen och Återförsöken Snowball och aldrig återställer. Om Snowballing inträffar bör du överväga att använda en exponentiell backoff för nya försök enligt beskrivningen i den [allmänna vägledningen för försök](/azure/architecture/best-practices/transient-faults) som publicerats av Microsoft patterns & Practices Group.
  
* **Timeout-värden**
  * Överväg din arbets belastning och ange värdena efter behov. Om du lagrar stora värden anger du tids gränsen till ett högre värde.
  * Ange `AbortOnConnectFail` till false och låt stackexchange. Redis återansluta åt dig.
  * Använd en enda ConnectionMultiplexer-instans för programmet. Du kan använda en LazyConnection för att skapa en enda instans som returneras av en anslutnings egenskap, som du ser i [Anslut till cachen med klassen ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Ange `ConnectionMultiplexer.ClientName` egenskapen till ett unikt namn för App-instansen.
  * Använd flera `ConnectionMultiplexer` instanser för anpassade arbets belastningar.
    * Du kan följa den här modellen om du har varierande belastning i ditt program. Exempel:
    * Du kan ha en multiplexor för att hantera stora nycklar.
    * Du kan ha en multiplexor för att hantera små nycklar.
    * Du kan ange olika värden för timeout för anslutningar och omprövnings logik för varje ConnectionMultiplexer som du använder.
    * Ange `ClientName` egenskapen för varje multiplexor för att hjälpa med diagnostik.
    * Den här vägledningen kan leda till mer strömlinjeformad svars tid per `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Vilken Azure-cache för Redis-klienter kan jag använda?
En av de fantastiska sakerna som Redis är att det finns många klienter som har stöd för många olika utvecklings språk. En aktuell lista över klienter finns i [Redis-klienter](https://redis.io/clients). För självstudier som rör flera olika språk och klienter, se [hur du använder Azure cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och dess artiklar på samma nivå i innehålls förteckningen.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Finns det en lokal emulator för Azure cache för Redis?
Det finns ingen lokal emulator för Azure cache för Redis, men du kan köra MSOpenTech-versionen av redis-server.exe från [kommando rads verktygen för Redis](https://github.com/MSOpenTech/redis/releases/) på den lokala datorn och ansluta till den för att få en liknande upplevelse till en lokal cache-emulator, som du ser i följande exempel:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Om du vill kan du konfigurera en [Redis. conf](https://redis.io/topics/config) -fil så att den stämmer bättre överens med [standardcache-inställningarna](cache-configure.md#default-redis-server-configuration) för din online Azure-cache för Redis.

### <a name="how-can-i-run-redis-commands"></a>Hur kan jag köra Redis-kommandon?
Du kan använda något av de kommandon som anges i [Redis-kommandon](https://redis.io/commands#) förutom de kommandon som visas i Redis-kommandon som [inte stöds i Azure cache för Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Det finns flera alternativ för att köra Redis-kommandon.

* Om du har en standard-eller Premium-cache kan du köra Redis-kommandon med hjälp av [Redis-konsolen](cache-configure.md#redis-console). Redis-konsolen är ett säkert sätt att köra Redis-kommandon i Azure Portal.
* Du kan också använda kommando rads verktygen för Redis. Utför följande steg för att använda dem:
* Hämta [kommando rads verktygen för Redis](https://github.com/MSOpenTech/redis/releases/).
* Anslut till cacheminnet med hjälp av `redis-cli.exe` . Skicka i cache-slutpunkten med växeln-h och nyckeln med-a som visas i följande exempel:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Kommando rads verktygen för Redis fungerar inte med TLS-porten, men du kan använda ett verktyg `stunnel` för att på ett säkert sätt ansluta verktygen till TLS-porten genom att följa anvisningarna i anvisningarna i [använda kommando rads verktyget Redis med Azure cache för Redis](./cache-how-to-redis-cli-tool.md) -artikeln.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Varför har inte Azure cache för Redis någon referens för MSDN-klass bibliotek?
Microsoft Azure cache för Redis baseras på det populära data lagret med öppen källkod i minnet, Redis. Den kan nås av en mängd olika [Redis-klienter](https://redis.io/clients) för många programmeringsspråk. Varje klient har sitt eget API som gör anrop till Azure cache för Redis-instans med hjälp av [Redis-kommandon](https://redis.io/commands).

Eftersom varje klient är annorlunda finns det ingen centraliserad klass referens på MSDN, och varje klient upprätthåller sin egen referens dokumentation. Förutom referens dokumentationen finns det flera självstudier som visar hur du kommer igång med Azure cache för Redis med olika språk och cache-klienter. Information om hur du kommer åt de här självstudierna finns i [så här använder du Azure cache för Redis](cache-dotnet-how-to-use-azure-redis-cache.md) och dess artiklar på samma nivå i innehålls förteckningen.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan jag använda Azure cache för Redis som ett PHP-sessionsnamn?
Ja, om du vill använda Azure cache för Redis som en PHP-session, anger du anslutnings strängen till Azure-cachen för Redis-instansen i `session.save_path` .

> [!IMPORTANT]
> När du använder Azure cache för Redis som ett PHP-sessionsobjekt måste du URL-koda den säkerhets nyckel som används för att ansluta till cachen, som du ser i följande exempel:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Om nyckeln inte är URL-kodad kan du få ett undantag med ett meddelande som: `Failed to parse session.save_path`
>

Mer information om hur du använder Azure cache för Redis som en PHP-sessions-cache med PhpRedis-klienten finns i [php-session hanterare](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Vad är Redis-databaser?

Redis-databaser är bara en logisk separering av data inom samma Redis-instans. Cacheminnet delas mellan alla databaser och den faktiska minnes användningen för en specifik databas beror på de nycklar/värden som lagras i databasen. Till exempel har en C6-cache 53 GB minne och en P5 har 120 GB. Du kan välja att publicera alla 53 GB/120 GB i en databas eller dela upp den mellan flera databaser. 

> [!NOTE]
> När du använder en Premium Azure-cache för Redis med klustring aktive rad är endast databas 0 tillgängligt. Den här begränsningen är en inbyggd Redis-begränsning och är inte särskilt för Azure cache för Redis. Mer information finns i [behöver jag göra ändringar i klient programmet för att använda kluster?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Azure cache för vanliga frågor och svar om Redis](cache-faq.md).