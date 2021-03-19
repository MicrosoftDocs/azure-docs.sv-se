---
title: Skala en Azure-cache för Redis-instans
description: Lär dig hur du skalar Azure cache för Redis-instanser med hjälp av Azure Portal och verktyg som Azure PowerShell och Azure CLI
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d91b62afacad31d78feb7d4743cd4050fcc1bd4e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581651"
---
# <a name="scale-an-azure-cache-for-redis-instance"></a>Skala en Azure-cache för Redis-instans
Azure cache för Redis har olika cache-erbjudanden, vilket ger flexibilitet i valet av cache-storlek och-funktioner. För en Basic-, standard-eller Premium-cache kan du ändra storlek och nivå när den har skapats för att hålla dig uppdaterad med dina program behov. Den här artikeln visar hur du skalar cacheminnet med hjälp av Azure Portal och verktyg som Azure PowerShell och Azure CLI.

## <a name="when-to-scale"></a>När ska du skala
Du kan använda [övervakningsfunktionerna](cache-how-to-monitor.md) i Azure cache för Redis för att övervaka hälso tillståndet och prestandan för cacheminnet och för att avgöra när cachen ska skalas. 

Du kan övervaka följande mått för att hjälpa dig att avgöra om du behöver skala.

* Redis server-belastning
* Minnesanvändning
* Nätverks bandbredd
* Processoranvändning

Om du fastställer att cachen inte längre uppfyller programmets krav kan du skala till en större eller mindre cache-pris nivå som passar ditt program. Mer information om hur du avgör vilken pris nivå för cache som ska användas finns i [välja rätt nivå](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Skala en cache
Om du vill skala cacheminnet [bläddrar du till cachen](cache-configure.md#configure-azure-cache-for-redis-settings) i [Azure Portal](https://portal.azure.com) och klickar på **skala** på **resurs-menyn**.

![Skala](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Välj önskad pris nivå på bladet **Välj pris nivå** och klicka på **Välj**.

![Prisnivå][redis-cache-pricing-tier-blade]


Du kan skala till en annan pris nivå med följande begränsningar:

* Du kan inte skala från en högre pris nivå till en lägre pris nivå.
  * Du kan inte skala från en **Premium** -cache till en **standard** -eller **Basic** -cache.
  * Du kan inte skala från **en** standardcache till en **grundläggande** cache.
* Du kan skala från en **grundläggande** **cache till en** standardcache, men du kan inte ändra storlek på samma tid. Om du behöver en annan storlek kan du utföra en efterföljande skalnings åtgärd till önskad storlek.
* Du kan inte skala från en **grundläggande** cache direkt till en **Premium** -cache. Börja med att skala från **Basic** till **standard** i en skalnings åtgärd och sedan från **standard** till **Premium** i en efterföljande skalnings åtgärd.
* Du kan inte skala från en större storlek till storleken på **C0 (250 MB)** . Du kan dock skala ned till en annan storlek inom samma pris nivå. Du kan till exempel skala ned från C5 standard till C1 standard.
 
Medan cachen skalas till den nya pris nivån visas en **skalnings** status i bladet **Azure cache för Redis** .

![Skalning][redis-cache-scaling]

När skalningen är klar ändras statusen från **skalning** till **körs**.

## <a name="how-to-automate-a-scaling-operation"></a>Automatisera en skalnings åtgärd
Förutom att skala dina cache-instanser i Azure Portal kan du skala med hjälp av PowerShell-cmdletar, Azure CLI och med hjälp av Microsoft Azure hanterings bibliotek (MAML). 

* [Skala med PowerShell](#scale-using-powershell)
* [Skala med Azure CLI](#scale-using-azure-cli)
* [Skala med MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Skala med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan skala Azure-cachen för Redis-instanser med PowerShell genom att använda cmdleten [set-AzRedisCache](/powershell/module/az.rediscache/set-azrediscache) när `Size` `Sku` egenskaperna,, eller `ShardCount` ändras. I följande exempel visas hur du skalar en cache med namnet `myCache` till en 2,5 GB-cache. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Mer information om skalning med PowerShell finns i [skala en Azure-cache för Redis med hjälp av PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Skala med Azure CLI
Om du vill skala Azure-cachen för Redis-instanser med Azure CLI anropar du `azure rediscache set` kommandot och överför önskade konfigurations ändringar som innehåller en ny storlek, SKU eller kluster storlek, beroende på den önskade skalnings åtgärden.

Mer information om skalning med Azure CLI finns i [ändra inställningar för en befintlig Azure-cache för Redis](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Skala med MAML
Om du vill skala Azure-cachen för Redis-instanser med hjälp av [Microsoft Azure hanterings bibliotek (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)anropar du `IRedisOperations.CreateOrUpdate` metoden och skickar den nya storleken för `RedisProperties.SKU.Capacity` .

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

Mer information finns i avsnittet [Hantera Azure-cache för Redis med hjälp av MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) -exemplet.

## <a name="scaling-faq"></a>Vanliga frågor om skalning
Följande lista innehåller svar på vanliga frågor om Azure cache för Redis-skalning.

* [Kan jag skala till, från eller inom en Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [När du har skalat måste jag ändra mitt cache-namn eller åtkomst nycklar?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hur fungerar skalningen?](#how-does-scaling-work)
* [Kommer jag att förlora data från mitt cacheminne under skalning?](#will-i-lose-data-from-my-cache-during-scaling)
* [Påverkas min anpassade databas inställning under skalning?](#is-my-custom-databases-setting-affected-during-scaling)
* [Kommer mitt cacheminne att vara tillgängligt under skalning?](#will-my-cache-be-available-during-scaling)
* När geo-replikering har kon figurer ATS kan jag inte skala mitt cacheminne eller ändra Shards i ett kluster?
* [Åtgärder som inte stöds](#operations-that-are-not-supported)
* [Hur lång tid tar det att skala?](#how-long-does-scaling-take)
* [Hur kan jag se när skalningen är klar?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan jag skala till, från eller inom en Premium-cache?
* Du kan inte skala från en **Premium** -cache till en **Basic** -eller **standard** -pris nivå.
* Du kan skala från en pris nivå för **Premium** -cache till en annan.
* Du kan inte skala från en **grundläggande** cache direkt till en **Premium** -cache. Börja med att skala från **Basic** till **standard** i en skalnings åtgärd och sedan från **standard** till **Premium** i en efterföljande skalnings åtgärd.
* Om du har aktiverat kluster när du skapade **Premium** -cachen kan du [Ändra kluster storleken](cache-how-to-premium-clustering.md#cluster-size). Om din cache har skapats utan att klustring Aktiver ATS, kan du konfigurera kluster vid ett senare tillfälle.
  
  Mer information finns i [så här konfigurerar du klustring för en Premium Azure-cache för Redis](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>När du har skalat måste jag ändra mitt cache-namn eller åtkomst nycklar?
Nej, ditt cache-namn och nycklar är oförändrade under en skalnings åtgärd.

### <a name="how-does-scaling-work"></a>Hur fungerar skalningen?
* När en **grundläggande** cache skalas till en annan storlek, stängs den av och en ny cache tillhandahålls med den nya storleken. Under den här tiden är cachen otillgänglig och alla data i cachen går förlorade.
* När en **grundläggande** **cache skalas till en** standardcache, allokeras en replik-cache och data kopieras från den primära cachen till replik-cachen. Cachen är fortfarande tillgänglig under skalnings processen.
* När en **standardcache** skalas till en annan storlek eller till en **Premium** -cache, stängs en av replikerna ned och etableras om till den nya storleken och de data som överförs, och sedan utför den andra repliken en redundansväxling innan den reserveras, liknande den process som inträffar under ett fel i en av cache-noderna.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Kommer jag att förlora data från mitt cacheminne under skalning?
* När en **grundläggande** cache skalas till en ny storlek förloras alla data och cachen är inte tillgänglig under skalnings åtgärden.
* När en **grundläggande** **cache skalas till en** standardcache bevaras data i cacheminnet vanligt vis.
* När en **standard** -cache skalas till en större storlek eller nivå, eller om en **Premium** -cache skalas till en större storlek, bevaras alla data vanligt vis. När du skalar en **standard** -eller **Premium** -cache nedåt till en mindre storlek kan data gå förlorade beroende på hur mycket data som finns i cachen som är relaterade till den nya storleken när den skalas. Om data förloras vid nedskalning avlägsnas nycklar med hjälp av [allkeys-LRU-](https://redis.io/topics/lru-cache) borttagnings principen. 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Påverkas min anpassade databas inställning under skalning?
Om du har konfigurerat ett anpassat värde för `databases` inställningen när du skapade cacheminnet bör du tänka på att vissa pris nivåer har olika [begränsningar i databasen](cache-configure.md#databases). Här följer några saker att tänka på när du skalar i det här scenariot:

* Vid skalning till en pris nivå med en lägre `databases` gräns än den aktuella nivån:
  * Om du använder standard antalet `databases` , som är 16 för alla pris nivåer, går inga data förlorade.
  * Om du använder ett anpassat antal `databases` som ligger inom gränserna för nivån som du skalar till, `databases` behålls den här inställningen och inga data förloras.
  * Om du använder ett anpassat antal `databases` som överskrider gränserna för den nya nivån, `databases` sänks inställningen till gränserna för den nya nivån och alla data i de borttagna databaserna går förlorade.
* När du skalar till en pris nivå med samma eller högre `databases` gräns än den aktuella nivån, `databases` behålls inställningen och inga data förloras.

Även om standard-och Premium-cacheminnen har ett service avtal på 99,9% för tillgänglighet finns det inget service avtal för data förlust.

### <a name="will-my-cache-be-available-during-scaling"></a>Kommer mitt cacheminne att vara tillgängligt under skalning?
* **Standard** -och **Premium** -cachen är fortfarande tillgängliga under skalnings åtgärden. Anslutningens signaler kan dock ske samtidigt som standard-och Premium-cachen skalas, och även vid skalning från Basic till standard-cache. Dessa anslutnings signaler förväntas vara små och Redis-klienter ska kunna återupprätta anslutningen direkt.
* **Grundläggande** cacheminnen är offline vid skalnings åtgärder till en annan storlek. Grundläggande cacheminnen är tillgängliga vid skalning från **Basic** till **standard** , men kan ha en liten anslutning blip. Om en anslutning blip inträffar ska Redis-klienterna kunna återupprätta anslutningen direkt.


### <a name="scaling-limitations-with-geo-replication"></a>Skalnings begränsningar med geo-replikering

När du har lagt till en geo-replikeringslänk mellan två cacheminnen kommer du inte längre att kunna initiera en skalnings åtgärd eller ändra antalet Shards i ett kluster. Du måste ta bort länken till cacheminnet för att kunna utfärda dessa kommandon. Mer information finns i [Konfigurera geo-replikering](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Åtgärder som inte stöds
* Du kan inte skala från en högre pris nivå till en lägre pris nivå.
  * Du kan inte skala från en **Premium** -cache till en **standard** -eller **Basic** -cache.
  * Du kan inte skala från **en** standardcache till en **grundläggande** cache.
* Du kan skala från en **grundläggande** **cache till en** standardcache, men du kan inte ändra storlek på samma tid. Om du behöver en annan storlek kan du utföra en efterföljande skalnings åtgärd till önskad storlek.
* Du kan inte skala från en **grundläggande** cache direkt till en **Premium** -cache. Skala först från **Basic** till **standard** i en skalnings åtgärd och skala sedan från **standard** till **Premium** i en efterföljande åtgärd.
* Du kan inte skala från en större storlek till storleken på **C0 (250 MB)** .

Om en skalnings åtgärd Miss lyckas försöker tjänsten återställa åtgärden och cachen återgår till den ursprungliga storleken.


### <a name="how-long-does-scaling-take"></a>Hur lång tid tar det att skala?
Skalnings tiden beror på hur mycket data som finns i cacheminnet, med större mängder data som tar längre tid att slutföra. Skalning tar cirka 20 minuter. För klustrade cacheminnen tar skalning cirka 20 minuter per Shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hur kan jag se när skalningen är klar?
I Azure Portal kan du se att skalnings åtgärden pågår. När skalningen är klar ändras statusen för cachen till **körs**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
