---
title: Exempel för Azure Cache for Redis
description: 'Lär dig hur du använder Azure cache för Redis med dessa kod exempel: ansluta till en cache, läsa och skriva data i en cache, ASP.NET Azure cache för Redis-leverantörer.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212285"
---
# <a name="azure-cache-for-redis-samples"></a>Exempel för Azure Cache for Redis
Det här avsnittet innehåller en lista över Azure cache för Redis-exempel, som täcker scenarier som att ansluta till en cache, läsa och skriva data till och från en cache och använda ASP.NET Azure-cache för Redis-leverantörer. Några exempel är nedladdnings bara projekt och vissa innehåller stegvisa instruktioner och innehåller kodfragment, men länkar inte till ett nedladdnings Bart projekt.

## <a name="hello-world-samples"></a>Hello World-exempel
I exemplen i det här avsnittet visas grunderna för att ansluta till en Azure-cache för Redis-instansen och läsa och skriva data till cachen med en mängd olika språk och Redis-klienter.

I [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) -exemplet visas hur du utför olika cache-åtgärder med hjälp av .net-klienten [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) .

Det här exemplet visar hur du:

* Använd olika anslutnings alternativ
* Läsa och skriva objekt till och från cachen med synkrona och asynkrona åtgärder
* Använd Redis MGET/MSET-kommandon för att returnera värden för angivna nycklar
* Utföra Redis-transaktionella åtgärder
* Arbeta med Redis-listor och sorterade uppsättningar
* Lagra .NET-objekt med JsonConvert-serialiserare
* Använd Redis Sets för att implementera taggning
* Arbeta med Redis-kluster

Mer information finns i dokumentationen om [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) på GitHub och för fler användnings scenarier se testen [stackexchange. Redis. test](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) .

[Använda Azure cache för Redis med python](cache-python-get-started.md) visar hur du kommer igång med Azure cache för Redis med hjälp av python och [Redis-py-](https://github.com/andymccurdy/redis-py) klienten.

[Arbeta med .net-objekt i cachen](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) visar ett sätt att serialisera .net-objekt, så att du kan skriva dem till och läsa dem från en Azure-cache för Redis-instansen. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Använd Azure cache för Redis som en skalnings plan för ASP.NET-Signalerare
Exemplet [Använd Azure-cache för Redis som en utskalning för ASP.net SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) -exempel visar hur du kan använda Azure cache för Redis som en Signals-bakplan. Mer information om displanering finns i [signaler scaleY with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure cache för Redis-exempel på kund frågor
Det här exemplet visar jämför prestanda mellan att komma åt data från en cache och att komma åt data från beständiga lagrings enheter. Det här exemplet har två projekt.

* [Lär dig hur Azure cache för Redis kan förbättra prestandan genom att cachelagra data](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Dirigera databasen och cachen för demon](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET för sessionstillstånd och cachelagring av utdata
Exemplet [Använd Azure cache för Redis för att lagra ASP.net sessionState och OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) visar hur du använder Azure cache för Redis för att lagra ASP.net och cachelagring av utdata med sessionState-och OutputCache-providern för Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Hantera Azure cache för Redis med MAML
Exemplet [Hantera Azure cache för Redis med hjälp av Azure Management libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) visar hur du kan använda Azures hanterings bibliotek för att hantera – (Skapa/uppdatera/ta bort) din cache. 

## <a name="custom-monitoring-sample"></a>Exempel på anpassad övervakning
Exemplet [åtkomst till Azure-cache för Redis övervaknings data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) visar hur du kan komma åt övervaknings data för Azure-cachen för Redis utanför Azure-portalen.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>En klonad Twitter-stil som skrivits med PHP och Redis
[Retwis](https://github.com/SyntaxC4-MSFT/retwis) -exemplet är Redis-Hello World. Det är en minimalt Twitter-typ av social nätverks klon som skrivits med Redis och PHP med [Predis](https://github.com/nrk/predis) -klienten. Käll koden är utformad för att vara enkel och samtidigt för att visa olika Redis data strukturer.

## <a name="bandwidth-monitor"></a>Övervakaren bandbredd
Exemplet på [bandbredds övervakaren](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) gör att du kan övervaka den bandbredd som används på klienten. Du kan mäta bandbredden genom att köra exemplet på cache-klientdatorn, ringa till cachen och observera bandbredden som rapporteras av exemplet på bandbredds övervakaren.
