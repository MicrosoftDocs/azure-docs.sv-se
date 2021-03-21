---
title: Konfigurera Redis Clustering – Premium Azure-cache för Redis
description: Lär dig hur du skapar och hanterar Redis-kluster för din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f1e84c838d310721cba604274388ae2767eb1502
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389679"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Konfigurera Redis-klustring för en Premium Azure-cache för Redis-instansen

Azure cache för Redis erbjuder Redis-kluster som [implementerat i Redis](https://redis.io/topics/cluster-tutorial). Med Redis-kluster får du följande fördelar: 

* Möjlighet att automatiskt dela din data uppsättning mellan flera noder. 
* Möjligheten att fortsätta utföra åtgärder när en delmängd av noderna är fel eller inte kan kommunicera med resten av klustret. 
* Mer data flöde: data flödet ökar linjärt när du ökar antalet Shards. 
* Mer minnes storlek: ökar linjärt när du ökar antalet Shards.  

Klustring ökar inte antalet anslutningar som är tillgängliga för en klustrad cache. Mer information om storlek, data flöde och bandbredd med Premium-cacheminnen finns i [välja rätt nivå](cache-overview.md#choosing-the-right-tier)

I Azure erbjuds Redis-kluster som en primär modell där varje Shard har ett primärt/replik par med replikering där replikeringen hanteras av Azure cache för Redis-tjänsten. 

## <a name="set-up-clustering"></a>Konfigurera klustring

Klustring är aktiverat på bladet **ny Azure-cache för Redis** under skapandet av cache. 

1. Om du vill skapa en Premium-cache loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs**. Förutom att skapa cacheminnen i Azure Portal, kan du också skapa dem med hjälp av Resource Manager-mallar, PowerShell eller Azure CLI. Mer information om hur du skapar en Azure-cache för Redis finns i [skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Skapa resurs.":::
   
2. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Välj Azure-cache för Redis.":::

3. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya Premium-cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Cachestorlek** | Klicka på list rutan och välj en Premium-cache för att konfigurera Premium-funktioner. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |

4. Välj fliken **nätverk** eller klicka på knappen **nätverk** längst ned på sidan.

5. På fliken **nätverk** väljer du anslutnings metod. För Premium-cache-instanser kan du ansluta antingen offentligt, via offentliga IP-adresser eller tjänst slut punkter eller privat, med hjälp av en privat slut punkt.

6. Välj **Nästa: fliken Avancerat** eller klicka på **Nästa: Avancerat** längst ned på sidan.

7. På fliken **Avancerat** för en Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet. Klicka på **Aktivera** om du vill aktivera klustring.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Växlar mellan kluster.":::

    Du kan ha upp till 10 Shards i klustret. När du har klickat på **Aktivera** drar du skjutreglaget eller anger ett tal mellan 1 och 10 för **antalet Shard** och klickar på **OK**.

    Varje Shard är ett primärt/replik-cache-par som hanteras av Azure och den totala storleken på cachen beräknas genom att antalet Shards multipliceras med den cachestorlek som valts på pris nivån.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Kluster växling är markerad.":::

    När cachen har skapats ansluter du till den och använder den precis som en icke-klustrad cache, och Redis distribuerar data i cache-Shards. Om diagnostik är [aktiverat](cache-how-to-monitor.md#enable-cache-diagnostics)samlas måtten separat för varje Shard och kan [visas](cache-how-to-monitor.md) i bladet Azure cache för Redis. 

8. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

9. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

10. Välj **Granska + skapa**. Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

11. När meddelandet grön verifiering har skickats visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. 

> [!NOTE]
> 
> Det krävs några mindre skillnader i klient programmet när klustring har kon figurer ATS. Mer information finns i [behöver jag göra ändringar i klient programmet för att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Exempel kod för att arbeta med kluster med StackExchange. Redis-klienten finns i delen [Clustering. cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) i [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Ändra kluster storleken på en Premium-cache som körs
Om du vill ändra kluster storleken på en Premium-cache som körs med klustrad aktive rad klickar du på **kluster storlek** på **resurs menyn**.

![Redis kluster storlek][redis-cache-redis-cluster-size]

Om du vill ändra kluster storleken använder du skjutreglaget eller skriver ett tal mellan 1 och 10 i text rutan **Shard Count** och klickar på **OK** för att spara.

Att öka kluster storleken ökar det maximala data flödet och cache-storleken. Att öka kluster storleken ökar inte max. anslutningar som är tillgängliga för klienter.

> [!NOTE]
> Skalning av ett kluster kör kommandot [migrera](https://redis.io/commands/migrate) , vilket är ett dyrt kommando, så för minimal påverkan bör du överväga att köra den här åtgärden under låg belastnings tider. Under migreringsprocessen visas en ökning i Server belastningen. Att skala ett kluster är en tids krävande process och hur lång tid det tar beror på antalet nycklar och storleken på de värden som är associerade med dessa nycklar.
> 
> 

## <a name="clustering-faq"></a>Vanliga frågor och svar om kluster

Följande lista innehåller svar på vanliga frågor om Azure cache för Redis-klustring.

* [Behöver jag göra några ändringar i klient programmet för att använda kluster?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hur distribueras nycklar i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* [Vilken största cachestorlek kan jag skapa?](#what-is-the-largest-cache-size-i-can-create)
* [Stöder alla Redis-klienter kluster?](#do-all-redis-clients-support-clustering)
* [Hur gör jag för att ansluta till mitt cacheminne när klustring är aktiverat?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan jag ansluta direkt till den enskilda Shards i cacheminnet?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan jag konfigurera kluster för en tidigare skapad cache?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan jag konfigurera kluster för en Basic-eller standard-cache?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan jag använda kluster med Redis ASP.NET-sessionstillstånd och providers för cachelagring av utdata?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Jag får flytta undantag när jag använder StackExchange. Redis och klustring, vad ska jag göra?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Behöver jag göra några ändringar i klient programmet för att använda kluster?
* När klustring är aktiverat är endast databas 0 tillgängligt. Om ditt klient program använder flera databaser och försöker läsa eller skriva till en annan databas än 0, genereras följande undantag. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Mer information finns i [Redis Cluster Specification-Implemented delmängd](https://redis.io/topics/cluster-spec#implemented-subset).
* Om du använder [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/)måste du använda 1.0.481 eller senare. Du ansluter till cachen med samma [slut punkter, portar och nycklar](cache-configure.md#properties) som du använder när du ansluter till ett cacheminne som inte har kluster aktiverat. Den enda skillnaden är att alla läsningar och skrivningar måste göras till databas 0.
  
  Andra klienter kan ha olika krav. Se att [alla Redis-klienter stöder klustring?](#do-all-redis-clients-support-clustering)
* Om programmet använder flera viktiga operationer i ett enda kommando, måste alla nycklar finnas i samma Shard. Information om hur du hittar nycklar i samma Shard finns i [hur är nycklar distribuerade i ett kluster?](#how-are-keys-distributed-in-a-cluster)
* Om du använder Redis ASP.NET-providern för sessionstillstånd måste du använda 2.0.1 eller senare. Se [kan jag använda kluster med Redis ASP.net och providers för cachelagring av utdata?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hur distribueras nycklar i ett kluster?
Enligt dokumentationen för Redis- [distributions modell](https://redis.io/topics/cluster-spec#keys-distribution-model) : nyckel utrymmet delas in på 16384 fack. Varje nyckel är hashad och tilldelas till någon av dessa platser, som distribueras mellan noderna i klustret. Du kan konfigurera vilken del av nyckeln som ska hashas för att säkerställa att flera nycklar finns i samma Shard med hash-taggar.

* Nycklar med en hash-tagg – om någon del av nyckeln är innesluten i `{` och `}` , är det bara den delen av nyckeln hash-kodad i syfte att fastställa en nyckels hash-plats. Följande tre nycklar skulle till exempel finnas i samma Shard: `{key}1` , `{key}2` och `{key}3` eftersom endast en `key` del av namnet är hashed. En fullständig lista över nycklar för hash-koder finns i [nycklar hash-Taggar](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Nycklar utan hash-tagg – hela nyckel namnet används för hashing. Detta resulterar i en statistiskt jämn fördelning över Shards i cacheminnet.

Vi rekommenderar att du distribuerar nycklarna jämnt för bästa prestanda och data flöde. Om du använder nycklar med en hash-tagg är programmets ansvar för att säkerställa att nycklarna fördelas jämnt.

Mer information finns i [nyckel distributions modell](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis kluster data horisontell partitionering](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)och [nycklar hash-Taggar](https://redis.io/topics/cluster-spec#keys-hash-tags).

Exempel kod för att arbeta med kluster och hitta nycklar i samma Shard med StackExchange. Redis-klienten finns i avsnittet [klustring. cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) i [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplet.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Vilken största cachestorlek kan jag skapa?
Den största storleken på Premium-cache är 120 GB. Du kan skapa upp till 10 Shards och ge den maximala storleken 1,2 TB GB. Om du behöver en större storlek kan du [begära mer](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Stöder alla Redis-klienter kluster?
Alla klienter har inte stöd för Redis-klustring! Kontrol lera dokumentationen för biblioteket som du använder för att kontrol lera att du använder ett bibliotek och en version som stöder klustring. StackExchange. Redis är ett bibliotek som stöder klustring i senare versioner. Mer information om andra klienter finns i avsnittet [spela upp med kluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) i [självstudien om Redis-kluster](https://redis.io/topics/cluster-tutorial). 

Redis-kluster protokollet kräver att varje klient ansluter till varje Shard direkt i kluster läge, och definierar även nya fel svar som "flyttad" CROSSSLOTS ". Försök att använda en klient som inte har stöd för kluster med en cache för kluster läge kan leda till att det finns många [flyttade omdirigerings undantag](https://redis.io/topics/cluster-spec#moved-redirection), eller bara bryta ditt program, om du utför flera nyckel förfrågningar på flera platser.

> [!NOTE]
> Om du använder StackExchange. Redis som klient kontrollerar du att du använder den senaste versionen av [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 eller senare för att klustring ska fungera korrekt. Om du har problem med att flytta undantag, se [Flytta undantag](#move-exceptions) för mer information.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hur gör jag för att ansluta till mitt cacheminne när klustring är aktiverat?
Du kan ansluta till cacheminnet med hjälp av samma [slut punkter](cache-configure.md#properties), [portar](cache-configure.md#properties)och [nycklar](cache-configure.md#access-keys) som du använder när du ansluter till ett cacheminne som inte har kluster aktiverat. Redis hanterar klustringen på Server delen så att du inte behöver hantera den från klienten.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan jag ansluta direkt till den enskilda Shards i cacheminnet?
Kluster protokollet kräver att klienten gör rätt Shard-anslutningar. Klienten bör därför göra detta korrekt åt dig. I detta fall består varje Shard av ett primärt/replik-cache-par, gemensamt kallat en cache-instans. Du kan ansluta till dessa cache-instanser med Redis-CLI-verktyget i [instabilt](https://redis.io/download) gren av Redis-lagringsplatsen på GitHub. Den här versionen implementerar grundläggande support när den startas med `-c` växeln. Mer information finns i [spela upp med klustret](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) i [https://redis.io](https://redis.io) [självstudien om Redis-kluster](https://redis.io/topics/cluster-tutorial).

Använd följande kommandon för icke-TLS.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Ersätt med för TLS `1300N` `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan jag konfigurera kluster för en tidigare skapad cache?
Ja. Se först till att cachen är Premium, genom att skala om inte är det. Sedan bör du kunna se kluster konfigurations alternativen, inklusive ett alternativ för att aktivera kluster. Du kan ändra kluster storleken när cachen har skapats, eller när du har aktiverat kluster för första gången.

   >[!IMPORTANT]
   >Du kan inte ångra aktiveringen av klustring. Och en cache med klustring aktive rad och endast en Shard fungerar *annorlunda* än en *cache med samma storlek utan klustring* .

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan jag konfigurera kluster för en Basic-eller standard-cache?
Klustring är bara tillgängligt för Premium-cacheminnen.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan jag använda kluster med Redis ASP.NET-sessionstillstånd och providers för cachelagring av utdata?
* **Redis för utgående cache** – inga ändringar krävs.
* **Redis-providern för sessionstillstånd** – om du vill använda klustring måste du använda [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 eller högre, annars genereras ett undantag. Detta är en avbrytande ändring. Mer information finns i [v 2.0.0-brytande ändrings information](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Jag får flytta undantag när jag använder StackExchange. Redis och klustring, vad ska jag göra?
Om du använder StackExchange. Redis och får `MOVE` undantag när du använder kluster bör du kontrol lera att du använder [stackexchange. Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) eller senare. Anvisningar om hur du konfigurerar dina .NET-program för att använda StackExchange. Redis finns i [Konfigurera cache-klienter](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
