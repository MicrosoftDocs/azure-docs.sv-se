---
title: Skala en Azure Cache for Redis instans
description: Lär dig hur du skalar Azure Cache for Redis instanser med hjälp av Azure Portal och verktyg som Azure PowerShell och Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43db8d4c094ec1b08a24c29fdaccf97f63ef29b9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833984"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Skala en Azure Cache for Redis instans
Azure Cache for Redis har olika cacheerbjudanden, vilket ger flexibilitet i valet av cachestorlek och funktioner. För en Basic-, Standard- eller Premium-cache kan du ändra dess storlek och nivå när den har skapats för att hålla koll på dina programbehov. Den här artikeln visar hur du skalar din cache med hjälp Azure Portal och verktyg som Azure PowerShell och Azure CLI.

## <a name="when-to-scale"></a>När ska du skala
Du kan använda [övervakningsfunktionerna](cache-how-to-monitor.md) i Azure Cache for Redis för att övervaka hälsotillstånd och prestanda för cacheminnet och avgöra när cachen ska skalas. 

Du kan övervaka följande mått för att avgöra om du behöver skala.

* Redis-serverbelastning
* Minnesanvändning
* Nätverksbandbredd
* Processoranvändning

Om du fastställer att cacheminnet inte längre uppfyller programmets krav kan du skala till en större eller mindre cacheprisnivå som är rätt för ditt program. Mer information om hur du avgör vilken prisnivå för cachelagring som ska användas finns [i Välja rätt nivå.](cache-overview.md#choosing-the-right-tier)

## <a name="scale-a-cache"></a>Skala en cache
Om du vill skala din cache [bläddrar du](cache-configure.md#configure-azure-cache-for-redis-settings) till cachen [i Azure Portal](https://portal.azure.com) och klickar **på Skala** på **resursmenyn**.

![Skala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Välj önskad prisnivå på bladet **Välj prisnivå** och klicka på **Välj**.

![Prisnivå][redis-cache-pricing-tier-blade]


Du kan skala till en annan prisnivå med följande begränsningar:

* Du kan inte skala från en högre prisnivå till en lägre prisnivå.
  * Du kan inte skala från en **Premium-cache** till en **Standard- eller** **Basic-cache.**
  * Du kan inte skala från en **Standard-cache** till en **Basic-cache.**
* Du kan skala från **en Basic-cache** till **en Standard-cache** men du kan inte ändra storleken på samma gång. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Börja med att skala **från Basic** **till Standard** i en skalningsåtgärd och sedan från **Standard** **till Premium** i en efterföljande skalningsåtgärd.
* Du kan inte skala från en större storlek ned till **storleken C0 (250 MB).** Du kan dock skala ned till valfri annan storlek inom samma prisnivå. Du kan till exempel skala ned från C5 Standard till C1 Standard.
 
När cacheminnet skalar till den nya prisnivån visas **skalningsstatusen** på **bladet Azure Cache for Redis.**

![Skalning][redis-cache-scaling]

När skalningen är klar ändras statusen från **Skalning till** **Körs.**

## <a name="how-to-automate-a-scaling-operation"></a>Automatisera en skalningsåtgärd
Förutom att skala dina cacheinstanser i Azure Portal kan du skala med Hjälp av PowerShell-cmdlets, Azure CLI och med hjälp av Microsoft Azure Management Libraries (MAML). 

* [Skala med PowerShell](#scale-using-powershell)
* [Skala med Azure CLI](#scale-using-azure-cli)
* [Skala med MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skala med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skala dina Azure Cache for Redis instanser med PowerShell med hjälp av cmdleten [Set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) när egenskaperna `Size` , eller `Sku` `ShardCount` ändras. I följande exempel visas hur du skalar en cache med `myCache` namnet till en 2,5 GB cache. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Mer information om skalning med PowerShell finns i Skala en Azure Cache for Redis [med PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skala med Azure CLI
Om du vill skala dina Azure Cache for Redis-instanser med Azure CLI anropar du kommandot och skickar önskade konfigurationsändringar som innehåller en ny storlek, SKU eller klusterstorlek, beroende på önskad `azure rediscache set` skalningsåtgärd.

Mer information om skalning med Azure CLI finns i [Ändra inställningar för en befintlig Azure Cache for Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skala med MAML
Om du vill skala Azure Cache for Redis-instanser [med Microsoft Azure-hanteringsbiblioteken (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)anropar du metoden och skickar `IRedisOperations.CreateOrUpdate` den nya storleken för `RedisProperties.SKU.Capacity` .

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Mer information finns i exemplet [Hantera Azure Cache for Redis MAML.](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)

## <a name="scaling-faq"></a>Vanliga frågor och svar om skalning
Följande lista innehåller svar på vanliga frågor om Azure Cache for Redis skalning.

* [Kan jag skala till, från eller inom en Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Efter skalningen, måste jag ändra mitt cachenamn eller åtkomstnycklar?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hur fungerar skalning?](#how-does-scaling-work)
* [Kommer jag att förlora data från mitt cacheminne under skalningen?](#will-i-lose-data-from-my-cache-during-scaling)
* [Påverkas min anpassade databasinställning under skalningen?](#is-my-custom-databases-setting-affected-during-scaling)
* [Kommer mitt cacheminne att vara tillgängligt under skalningen?](#will-my-cache-be-available-during-scaling)
* Varför kan jag inte skala mitt cacheminne eller ändra shards i ett kluster när geo-replikering har konfigurerats?
* [Åtgärder som inte stöds](#operations-that-are-not-supported)
* [Hur lång tid tar skalningen?](#how-long-does-scaling-take)
* [Hur vet jag när skalningen är klar?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan jag skala till, från eller inom en Premium-cache?
* Du kan inte skala från en **Premium-cache** till **prisnivån Basic** **eller** Standard.
* Du kan skala från en **Prisnivå för Premium-cache** till en annan.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Börja med att skala **från Basic** **till Standard** i en skalningsåtgärd och sedan från **Standard** till **Premium** i en efterföljande skalningsåtgärd.
* Om du aktiverade klustring när du skapade **din Premium-cache** kan [du ändra klusterstorleken](cache-how-to-premium-clustering.md#cluster-size). Om ditt cacheminne har skapats utan att klustring är aktiverat kan du konfigurera klustring vid ett senare tillfälle.
  
  Mer information finns i Så [här konfigurerar du klustring för en Premium-Azure Cache for Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Efter skalningen, måste jag ändra mitt cachenamn eller åtkomstnycklar?
Nej, cachenamnet och nycklarna ändras inte under en skalningsåtgärd.

### <a name="how-does-scaling-work"></a>Hur fungerar skalning?
* När en **Basic-cache** skalas till en annan storlek stängs den av och en ny cache etableras med den nya storleken. Under den här tiden är cachen inte tillgänglig och alla data i cacheminnet går förlorade.
* När en **Basic-cache** skalas till **en standardcache** etableras en replikcache och data kopieras från den primära cachen till replikcachen. Cachen förblir tillgänglig under skalningsprocessen.
* När en **Standard-cache** skalas till en annan storlek eller till en **Premium-cache** stängs en av replikerna av och ometablerar till den nya storleken och de data som överförs, och sedan utför den andra repliken en redundans innan den ometablerar, ungefär som den process som inträffar vid ett fel i en av cachenoderna.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Kommer jag att förlora data från mitt cacheminne under skalningen?
* När en **Basic-cache** skalas till en ny storlek går alla data förlorade och cachen är inte tillgänglig under skalningsåtgärden.
* När en **Basic-cache** skalas till **en standardcache** bevaras vanligtvis data i cacheminnet.
* När en **Standard-cache** skalas till en större storlek eller nivå, eller om en **Premium-cache** skalas till en större storlek, bevaras vanligtvis alla data. När du skalar ned **en Standard-** eller **Premium-cache** till en mindre storlek kan data gå förlorade beroende på hur mycket data som finns i cacheminnet som är relaterade till den nya storleken när den skalas. Om data går förlorade vid nedskalning avlägsnas nycklar med hjälp av avlägsningsprincipen [allkeys-lru.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Påverkas min anpassade databasinställning under skalningen?
Om du konfigurerade ett anpassat värde för inställningen när du skapade cacheminnet bör du komma ihåg att vissa `databases` prisnivåer har olika [databasgränser.](cache-configure.md#databases) Här är några saker att tänka på när du skalar i det här scenariot:

* När du skalar till en prisnivå med en lägre `databases` gräns än den aktuella nivån:
  * Om du använder standardantalet `databases` , som är 16 för alla prisnivåer, går inga data förlorade.
  * Om du använder ett anpassat antal som faller inom gränserna för den nivå som du skalar till bevaras den här inställningen och `databases` `databases` inga data går förlorade.
  * Om du använder ett anpassat antal som överskrider gränserna för den nya nivån sänks inställningen till gränserna för den nya nivån och alla data i de borttagna `databases` `databases` databaserna går förlorade.
* När du skalar till en prisnivå med samma eller högre gräns än den aktuella nivån behålls din inställning och `databases` `databases` inga data går förlorade.

Standard- och Premium-cacheminnen har ett serviceavtal på 99,9 % för tillgänglighet, men det finns inget serviceavtal för dataförlust.

### <a name="will-my-cache-be-available-during-scaling"></a>Kommer mitt cacheminne att vara tillgängligt under skalningen?
* **Standard-** och Premium-cacheminnen är fortfarande tillgängliga under skalningsåtgärden.  Anslutningslyckckningar kan dock uppstå vid skalning av Standard- och Premium-cacheminnen, och även vid skalning från Basic till Standard-cacheminnen. Dessa anslutningssnackor förväntas vara små och Redis-klienter bör kunna återupprätta anslutningen omedelbart.
* **Grundläggande** cacheminnen är offline under skalning till en annan storlek. Grundläggande cacheminnen är fortfarande tillgängliga vid **skalning från Basic** till **Standard,** men kan uppleva en liten anslutnings blip. Om en anslutningskoppling inträffar bör Redis-klienterna kunna återupprätta anslutningen omedelbart.


### <a name="scaling-limitations-with-geo-replication"></a>Skalningsbegränsningar med geo-replikering

När du har lagt till en geo-replikeringslänk mellan två cacheminnen kan du inte längre initiera en skalningsåtgärd eller ändra antalet shards i ett kluster. Du måste ta bort länken till cacheminnet för att utfärda dessa kommandon. Mer information finns i [Konfigurera geo-replikering.](cache-how-to-geo-replication.md)


### <a name="operations-that-are-not-supported"></a>Åtgärder som inte stöds
* Du kan inte skala från en högre prisnivå till en lägre prisnivå.
  * Du kan inte skala från en **Premium-cache** till en **Standard- eller** **Basic-cache.**
  * Du kan inte skala från en **Standard-cache** till en **Basic-cache.**
* Du kan skala från **en Basic-cache** till **en standardcache,** men du kan inte ändra storlek på samma gång. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
* Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Skala först från **Basic** till **Standard** i en skalningsåtgärd och skala sedan från **Standard** till **Premium** i en efterföljande åtgärd.
* Du kan inte skala från en större storlek ned till **storleken C0 (250 MB).**

Om en skalningsåtgärd misslyckas försöker tjänsten återställa åtgärden och cacheminnet återgår till den ursprungliga storleken.


### <a name="how-long-does-scaling-take"></a>Hur lång tid tar skalningen?
Skalningstiden beror på hur mycket data som finns i cacheminnet, och större mängder data tar längre tid att slutföra. Skalning tar cirka 20 minuter. För klustrade cacheminnen tar skalningen cirka 20 minuter per fragment.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hur ser jag när skalningen är klar?
I Azure Portal kan du se den pågående skalningsåtgärden. När skalningen är klar ändras cachens status till **Körs.**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
