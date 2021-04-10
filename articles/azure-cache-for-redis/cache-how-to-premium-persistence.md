---
title: Konfigurera data beständighet – Premium Azure-cache för Redis
description: Lär dig hur du konfigurerar och hanterar data beständige din Premium-nivå i Azure-cache för Redis-instanser
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 9d11d17f90dcd6335fcaf6bd48a44037777a087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601393"
---
# <a name="configure-data-persistence-for-a-premium-azure-cache-for-redis-instance"></a>Konfigurera data beständighet för en Premium Azure-cache för Redis-instans

Med [Redis beständighet](https://redis.io/topics/persistence) kan du spara data som lagras i Redis. Du kan också ta ögonblicks bilder och säkerhetskopiera data som du kan läsa in i händelse av maskin varu problem. Detta är en enorm fördel jämfört med Basic-eller standard-nivån där alla data lagras i minnet och det kan finnas potentiell data förlust vid ett haveri där cache-noderna är nere. 

Azure cache för Redis erbjuder Redis beständighet med hjälp av följande modeller:

* **RDB beständighet** – när RDB (Redis Database) har kon figurer ATS, sparar Azure cache för Redis en ögonblicks bild av Azure cache för Redis i ett Redis binärformat till disk (i ett Azure Storage konto) baserat på en konfigurerbar säkerhets kopierings frekvens. Om en oåterkallelig händelse inträffar som inaktiverar både den primära cachen och replik cachen, rekonstrueras cachen med den senaste ögonblicks bilden. Läs mer om [fördelarna](https://redis.io/topics/persistence#rdb-advantages) och [nack delarna](https://redis.io/topics/persistence#rdb-disadvantages) med RDB persistence.
* **AOF beständighet** – när AOF (append Only File) har kon figurer ATS, sparar Azure cache för Redis varje Skriv åtgärd till en logg som sparas minst en gång i taget till ett Azure Storage-konto. Om en oåterkallelig händelse inträffar som inaktiverar både den primära cachen och replik cachen, rekonstrueras cachen med hjälp av lagrade Skriv åtgärder. Läs mer om [fördelarna](https://redis.io/topics/persistence#aof-advantages) och [nack delarna](https://redis.io/topics/persistence#aof-disadvantages) med AOF persistence.

Persistence skriver Redis-data till ett Azure Storage-konto som du äger och hanterar. Du kan konfigurera från bladet **ny Azure-cache för Redis** under skapandet av cacheminnet och på **resurs menyn** för befintliga Premium-cacheminnen.

> [!NOTE]
> 
> Azure Storage krypterar data automatiskt när de sparas. Du kan använda dina egna nycklar för krypteringen. Mer information finns i [kund hanterade nycklar med Azure Key Vault](../storage/common/storage-service-encryption.md).
> 
> 

## <a name="set-up-data-persistence"></a>Konfigurera data persistence

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

7. På fliken **Avancerat** för en Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet. För data persistens kan du välja antingen **RDB** eller **AOF** persistence. 

8. Om du vill aktivera RDB beständighet klickar du på **RDB** och konfigurerar inställningarna. 
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Säkerhets kopierings frekvens** | List rutan och välj ett intervall för säkerhets kopieringar, alternativ omfattar **15 minuter**, **30 minuter**, **60 minuter**, **6 timmar**, **12 timmar** och **24 timmar**. | Intervallet börjar räkna upp när den tidigare säkerhets kopieringen har slutförts och när en ny säkerhets kopia har initierats. | 
   | **Lagrings konto** | List rutan och välj ditt lagrings konto. | Du måste välja ett lagrings konto i samma region och prenumeration som cachen, och ett **Premium Storage** konto rekommenderas eftersom Premium Storage har högre genomflöde.  | 
   | **Lagrings nyckel** | List rutan och välj antingen den **primära nyckeln** eller **sekundära nyckeln** som ska användas. | Om lagrings nyckeln för ditt beständiga konto återskapas måste du konfigurera om önskad nyckel i list rutan **lagrings nyckel** . | 

    Den första säkerhets kopieringen initieras när intervallet för säkerhets kopierings frekvensen förflutit.
    
   > [!NOTE]
   > När RDB-filer säkerhets kopie ras till lagring, lagras de i form av sid-blobbar.

9. Om du vill aktivera AOF beständighet klickar du på **AOF** och konfigurerar inställningarna. 
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Första lagrings konto** | List rutan och välj ditt lagrings konto. | Det här lagrings kontot måste finnas i samma region och prenumeration som cachen, och ett **Premium Storage** konto rekommenderas eftersom Premium Storage har högre genomflöde. | 
   | **Första lagrings nyckel** | List rutan och välj antingen den **primära nyckeln** eller **sekundära nyckeln** som ska användas. | Om lagrings nyckeln för ditt beständiga konto återskapas måste du konfigurera om önskad nyckel i list rutan **lagrings nyckel** . | 
   | **Andra lagrings konto** | Valfritt List rutan och välj ditt sekundära lagrings konto. | Du kan också konfigurera ett ytterligare lagrings konto. Om ett andra lagrings konto har kon figurer ATS skrivs skrivningar till replik-cachen till det andra lagrings kontot. | 
   | **Sekundär lagrings nyckel** | Valfritt List rutan och välj antingen den **primära nyckeln** eller **sekundära nyckeln** som ska användas. | Om lagrings nyckeln för ditt beständiga konto återskapas måste du konfigurera om önskad nyckel i list rutan **lagrings nyckel** . | 

    När AOF persistence har Aktiver ATS, sparas Skriv åtgärder till cacheminnet på det angivna lagrings kontot (eller kontona om du har konfigurerat ett andra lagrings konto). I händelse av ett oåterkalleligt haveri som tar ner både den primära cachen och repliken, används den lagrade AOF-loggen för att återskapa cacheminnet.

10. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

11. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

12. Välj **Granska + skapa**. Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

13. När meddelandet grön verifiering har skickats visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. 

## <a name="persistence-faq"></a>Beständiga vanliga frågor och svar
Följande lista innehåller svar på vanliga frågor om Azure cache för Redis persistence.

* [Kan jag Aktivera persistence i en tidigare skapad cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan jag aktivera AOF och RDB persistence samtidigt?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Vilken beständiga modell ska jag välja?](#which-persistence-model-should-i-choose)
* [Vad händer om jag har skalat till en annan storlek och en säkerhets kopia har återställts före skalnings åtgärden?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Kan jag använda samma lagrings konto för persistence över två olika cacheminnen?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)
* [Debiteras jag för det lagrings utrymme som används i data persisten](#will-i-be-charged-for-the-storage-being-used-in-data-persistence)

### <a name="rdb-persistence"></a>RDB persistence
* [Kan jag ändra frekvensen för RDB säkerhets kopiering när jag har skapat cacheminnet?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Varför om jag har en RDB säkerhets kopierings frekvens på 60 minuter finns det över 60 minuter mellan säkerhets kopieringar?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Vad händer med de gamla säkerhets kopiorna av RDB när en ny säkerhets kopia görs?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF persistence
* [När ska jag använda ett andra lagrings konto?](#when-should-i-use-a-second-storage-account)
* [Påverkar AOF persistence hela tiden, fördröjningen eller prestandan i mitt cacheminne?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hur kan jag ta bort det andra lagrings kontot?](#how-can-i-remove-the-second-storage-account)
* [Vad är en omskrivning och hur påverkar den min cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Vad ska jag förvänta mig när jag skalar en cache med AOF aktiverat?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hur organiseras mina AOF-data i lagringen?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan jag Aktivera persistence i en tidigare skapad cache?
Ja, Redis persistence kan konfigureras både vid skapande av cache och på befintliga Premium-cacheminnen.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan jag aktivera AOF och RDB persistence samtidigt?

Nej, du kan bara aktivera RDB eller AOF, men inte båda samtidigt.

### <a name="which-persistence-model-should-i-choose"></a>Vilken beständiga modell ska jag välja?

AOF persist sparar varje skrivning till en logg, som har viss påverkan på data flödet, jämfört med RDB persistence som sparar säkerhets kopior baserat på det konfigurerade säkerhets kopierings intervallet, med minimal påverkan på prestanda. Välj AOF persiste om det primära målet är att minimera data förlust och du kan hantera en minskning i data flödet för cacheminnet. Välj RDB persiste om du vill upprätthålla optimalt data flöde i cacheminnet, men ändå vill ha en mekanism för data återställning.

* Läs mer om [fördelarna](https://redis.io/topics/persistence#rdb-advantages) och [nack delarna](https://redis.io/topics/persistence#rdb-disadvantages) med RDB persistence.
* Läs mer om [fördelarna](https://redis.io/topics/persistence#aof-advantages) och [nack delarna](https://redis.io/topics/persistence#aof-disadvantages) med AOF persistence.

Mer information om prestanda när du använder AOF persistence finns i [AOF-persistence påverkar hela, svars tid eller prestanda för mitt cacheminne?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Vad händer om jag har skalat till en annan storlek och en säkerhets kopia har återställts före skalnings åtgärden?

För både RDB och AOF persistence:

* Om du har skalat till en större storlek påverkas ingen påverkan.
* Om du har skalat till en mindre storlek och du har en inställning för anpassade [databaser](cache-configure.md#databases) som är större än [databasernas gräns](cache-configure.md#databases) för den nya storleken återställs inte data i dessa databaser. Mer information finns i [är mina anpassade databas inställningar som påverkas under skalning?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Om du har skalat till en mindre storlek och det inte finns tillräckligt med utrymme i den mindre storleken för att lagra alla data från den senaste säkerhets kopieringen, tas nycklar bort under återställnings processen, vanligt vis med hjälp av [allkeys-LRU](https://redis.io/topics/lru-cache) .

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Kan jag använda samma lagrings konto för persistence över två olika cacheminnen?
Ja, du kan använda samma lagrings konto för persistence över två olika cacheminnen

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan jag ändra frekvensen för RDB säkerhets kopiering när jag har skapat cacheminnet?
Ja, du kan ändra säkerhets kopierings frekvensen för RDB persistence på bladet **data persistes** . Instruktioner finns i Konfigurera Redis persistence.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Varför om jag har en RDB säkerhets kopierings frekvens på 60 minuter finns det över 60 minuter mellan säkerhets kopieringar?
Intervallet för säkerhets kopiering av RDB som inte startar förrän den tidigare säkerhets kopieringen har slutförts. Om säkerhets kopierings frekvensen är 60 minuter och den tar en säkerhets kopiering 15 minuter att slutföra, kommer nästa säkerhets kopiering inte att starta förrän 75 minuter efter start tiden för den tidigare säkerhets kopieringen.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Vad händer med de gamla säkerhets kopiorna av RDB när en ny säkerhets kopia görs?
Alla beständiga säkerhets kopior av RDB, förutom de senaste, tas bort automatiskt. Den här borttagningen kanske inte sker omedelbart, men äldre säkerhets kopieringar är inte kvar på obestämd tid.


### <a name="when-should-i-use-a-second-storage-account"></a>När ska jag använda ett andra lagrings konto?

Du bör använda ett andra lagrings konto för AOF persistence när du tror att du har högre än förväntat uppsättnings åtgärder i cacheminnet.  Genom att konfigurera det sekundära lagrings kontot kan du se till att cachen inte når gränser för lagrings bandbredd.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Påverkar AOF persistence hela tiden, fördröjningen eller prestandan i mitt cacheminne?

AOF persistence påverkar data flödet genom cirka 15% – 20% när cachen är under maximal belastning (CPU och server belastning både i 90%). Det bör inte finnas några latens-problem när cachen ligger inom dessa gränser. Cachen kommer dock att uppnå dessa gränser närmast när AOF har Aktiver ATS.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hur kan jag ta bort det andra lagrings kontot?

Du kan ta bort det sekundära lagrings kontot AOF persistence genom att ange att det andra lagrings kontot ska vara samma som det första lagrings kontot. För befintliga cacheminnen öppnas bladet **data persistes** från **resurs menyn** för cacheminnet. Klicka på **inaktive rad** om du vill inaktivera AOF persistence.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Vad är en omskrivning och hur påverkar den min cache?

När AOF-filen blir tillräckligt stor placeras en omskrivning automatiskt i kö i cacheminnet. Omskrivning av filen AOF med den minsta uppsättning åtgärder som krävs för att skapa den aktuella data uppsättningen. Vid omskrivning förväntar sig att uppnå prestanda begränsningar tidigare vid hantering av stora data mängder. Omskrivning sker mindre ofta när AOF-filen blir större, men tar en stor del av tiden när den inträffar.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Vad ska jag förvänta mig när jag skalar en cache med AOF aktiverat?

Om AOF-filen vid skalnings tillfället är betydligt stor kan skalnings åtgärden ta längre tid än förväntat eftersom den laddar om filen när skalningen har slutförts.

Mer information om skalning finns i [Vad händer om jag har skalat till en annan storlek och en säkerhets kopia har återställts före skalnings åtgärden?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hur organiseras mina AOF-data i lagringen?

Data som lagras i AOF-filer delas upp i flera sid-blobar per nod för att öka prestanda för att spara data till lagring. I följande tabell visas hur många sid-blobar som används för varje pris nivå:

| Premiumnivå | Blobar |
|--------------|-------|
| P1           | 4 per Shard    |
| P2           | 8 per Shard    |
| P3           | 16 per Shard   |
| P4           | 20 per Shard   |

När klustring är aktiverat har varje Shard i cacheminnet en egen uppsättning Page blobbar, som anges i föregående tabell. Till exempel kan en P2-cache med tre Shards distribuera sin AOF-fil över 24 Page blobbar (8 blobbar per Shard, med 3 Shards).

Efter en omskrivning finns det två uppsättningar av AOF-filer i lagrings utrymmet. Omskrivningar sker i bakgrunden och läggs till i den första uppsättningen filer, medan set-åtgärder som skickas till cachen under omskrivning läggs till i den andra uppsättningen. En säkerhets kopia lagras tillfälligt vid omskrivning Om det skulle uppstå ett problem, men tas i omedelbart när en omskrivning har slutförts.

### <a name="will-i-be-charged-for-the-storage-being-used-in-data-persistence"></a>Kommer jag att debiteras för lagrings utrymmet som används i data persisten?

Ja, du debiteras för det lagrings utrymme som används enligt pris sättnings modellen för det lagrings konto som används.


## <a name="next-steps"></a>Nästa steg
Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
