---
title: Konfigurera geo-replikering för Premium Azure Cache for Redis instanser
description: Lär dig hur du replikerar Azure Cache for Redis Premium-instanser mellan Azure-regioner
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 0be2bb59b46dc827001d89f8e0f1be23f35a714d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536087"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Konfigurera geo-replikering för Premium Azure Cache for Redis instanser

I den här artikeln får du lära dig hur du konfigurerar en geo-replikerad Azure Cache med hjälp av Azure Portal.

Geo-replikering länkar samman två Premium Azure Cache for Redis instanser och skapar en datareplikeringsrelation. Dessa cacheinstanser finns vanligtvis i olika Azure-regioner, men de krävs inte. En instans fungerar som primär och den andra som sekundär. Den primära hanterar läs- och skrivbegäranden och sprider ändringar till den sekundära. Den här processen fortsätter tills länken mellan de två instanserna tas bort.

> [!NOTE]
> Geo-replikering är utformat som en haveriberedskapslösning.
>
>

## <a name="geo-replication-prerequisites"></a>Förutsättningar för geo-replikering

Följande krav måste vara uppfyllda för att du ska kunna konfigurera geo-replikering mellan två cacheminnen:

- Båda cacheminnena är [cacheminnen på](cache-overview.md#service-tiers) Premium-nivå.
- Båda cacheminnena finns i samma Azure-prenumeration.
- Den sekundära länkade cachen har antingen samma cachestorlek eller en större cachestorlek än den primära länkade cachen.
- Båda cacheminnena skapas och körs.

Vissa funktioner stöds inte med geo-replikering:

- Persistence stöds inte med geo-replikering.
- Klustring stöds om båda cacheminnena har klustring aktiverat och har samma antal shards.
- Cacheminnen i samma virtuella nätverk stöds.
- Cacheminnen i olika virtuella datorer stöds med varningar. Mer [information finns i Kan jag använda geo-replikering med mina cacheminnen i ett VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

När geo-replikering har konfigurerats gäller följande begränsningar för ditt länkade cachepar:

- Den sekundära länkade cachen är skrivskyddade. du kan läsa från den, men du kan inte skriva några data till den. Om du väljer att läsa från Geo-Secondary-instansen är det viktigt att observera att när en fullständig datasynkronisering sker mellan Geo-Primary och Geo-Secondary (sker när antingen Geo-Primary eller Geo-Secondary uppdateras och även vid vissa omstartsscenarier), kommer Geo-Secondary-instansen att skapa fel (som anger att en fullständig datasynkronisering pågår) på en Redis-åtgärd mot den tills den fullständiga datasynkronisering mellan Geo-Primary och Geo-Secondary är klar. Program som läser från Geo-Seocndary bör byggas för att gå tillbaka till Geo-Primary när Geo-Seocndary kastar sådana fel. 
- Alla data som fanns i den sekundära länkade cachen innan länken lades till tas bort. Om geo-replikeringen tas bort senare finns replikerade data kvar i den sekundära länkade cachen.
- Du kan inte skala [någon](cache-how-to-scale.md) cache medan cacheminnena är länkade.
- Du kan inte [ändra antalet shards om](cache-how-to-premium-clustering.md) klustring är aktiverat i cacheminnet.
- Du kan inte aktivera persistence på någon av cacheminnena.
- Du kan [exportera från](cache-how-to-import-export-data.md#export) endera cachen.
- Du kan inte importera [till](cache-how-to-import-export-data.md#import) den sekundära länkade cachen.
- Du kan inte ta bort antingen länkad cache eller resursgruppen som innehåller dem förrän du tar bort länken till cacheminnena. Mer information finns i Varför [misslyckades åtgärden när jag försökte ta bort min länkade cache?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Om cacheminnena finns i olika regioner gäller kostnader för utgående nätverkstrafik för data som flyttas mellan regioner. Mer information finns i Hur [mycket kostar det att replikera mina data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatisk redundans sker inte mellan den primära och sekundära länkade cachen. Mer information och information om hur du redundans redundansar ett klientprogram finns [i Hur fungerar redundans till den sekundära länkade cachen?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Lägga till en länk för geo-replikering

1. Om du vill länka ihop två cacheminnen för geo-replikering klickar du på **Geo-replikering** på menyn Resurs i cachen som du vill ska vara den primära länkade cachen. Klicka sedan på **länken Lägg till cachereplikering** från **bladet Geo-replikering.**

    ![Lägg till länk](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Klicka på namnet på den avsedda sekundära cachen från **listan Kompatibla** cacheminnen. Om den sekundära cachen inte visas i listan kontrollerar du att [kraven för geo-replikering](#geo-replication-prerequisites) för den sekundära cachen är uppfyllda. Om du vill filtrera cacheminnen efter region klickar du på regionen på kartan för att endast visa dessa cacheminnen i **listan Kompatibla** cacheminnen.

    ![Geo-replikeringskompatibla cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Du kan också starta länkningsprocessen eller visa information om den sekundära cachen med hjälp av snabbmenyn.

    ![Snabbmeny för geo-replikering](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Klicka **på** Länka för att länka ihop de två cacheminnena och påbörja replikeringsprocessen.

    ![Länka cacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Du kan visa replikeringsprocessens förlopp på **bladet Geo-replikering.**

    ![Länkningsstatus](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Du kan också visa länkstatusen på **bladet Översikt** för både primära och sekundära cacheminnen.

    ![Skärmbild som visar hur du visar länkningsstatus för primära och sekundära cacheminnen.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    När replikeringen är klar ändras **länkstatusen** till **Lyckades.**

    ![Cachestatus](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Den primära länkade cachen är fortfarande tillgänglig för användning under länkningsprocessen. Den sekundära länkade cachen är inte tillgänglig förrän länkningsprocessen har slutförts.

## <a name="remove-a-geo-replication-link"></a>Ta bort en länk för geo-replikering

1. Om du vill ta bort länken mellan två cacheminnen och stoppa geo-replikering klickar **du på Ta** bort länkcacheminnen från **bladet Geo-replikering.**
    
    ![Ta bort länkcacheminnen](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    När avlänkningsprocessen är klar är den sekundära cachen tillgänglig för både läsningar och skrivningar.

>[!NOTE]
>När länken för geo-replikering tas bort finns replikerade data från den primära länkade cachen kvar i den sekundära cachen.
>
>

## <a name="geo-replication-faq"></a>Vanliga frågor och svar om geo-replikering

- [Kan jag använda geo-replikering med en Standard- eller Basic-nivåcache?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Är mitt cacheminne tillgängligt för användning under länkning eller avlänkning?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kan jag länka ihop fler än två cacheminnen?](#can-i-link-more-than-two-caches-together)
- [Kan jag länka två cacheminnen från olika Azure-prenumerationer?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kan jag länka två cacheminnen med olika storlekar?](#can-i-link-two-caches-with-different-sizes)
- [Kan jag använda geo-replikering med klustring aktiverat?](#can-i-use-geo-replication-with-clustering-enabled)
- [Kan jag använda geo-replikering med mina cacheminnen i ett VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Vad är replikeringsschemat för Geo-replikering i Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Hur lång tid tar replikeringen av geo-replikering?](#how-long-does-geo-replication-replication-take)
- [Garanteras replikeringsåterställningspunkten?](#is-the-replication-recovery-point-guaranteed)
- [Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Hur mycket kostar det att replikera mina data mellan Azure-regioner?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Varför misslyckades åtgärden när jag försökte ta bort mitt länkade cacheminne?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Vilken region ska jag använda för min sekundära länkade cache?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hur fungerar det att gå över till den sekundära länkade cachen?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Kan jag konfigurera brandväggen med geo-replikering?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Kan jag använda geo-replikering med en Standard- eller Basic-nivåcache?

Nej, geo-replikering är bara tillgängligt för cacheminnen på Premium-nivå.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Är mitt cacheminne tillgängligt för användning under länkning eller avlänkning?

- När du länkar förblir den primära länkade cachen tillgänglig medan länkningsprocessen slutförs.
- Vid länkning är den sekundära länkade cachen inte tillgänglig förrän länkningsprocessen har slutförts.
- Vid avlänkning förblir båda cacheminnena tillgängliga medan avlänkningsprocessen slutförs.

### <a name="can-i-link-more-than-two-caches-together"></a>Kan jag länka ihop fler än två cacheminnen?

Nej, du kan bara länka ihop två cacheminnen.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kan jag länka två cacheminnen från olika Azure-prenumerationer?

Nej, båda cacheminnena måste finnas i samma Azure-prenumeration.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kan jag länka två cacheminnen med olika storlekar?

Ja, så länge den sekundära länkade cachen är större än den primära länkade cachen.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kan jag använda geo-replikering med klustring aktiverat?

Ja, så länge båda cacheminnena har samma antal shards.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Kan jag använda geo-replikering med mina cacheminnen i ett VNET?

Ja, geo-replikering av cacheminnen i virtuella datorer stöds med varningar:

- Geo-replikering mellan cacheminnen i samma virtuella nätverk stöds.
- Geo-replikering mellan cacheminnen i olika virtuella datorer stöds också.
  - Om de virtuella nätverken finns i samma region kan du ansluta dem med hjälp av [VNET-peering](../virtual-network/virtual-network-peering-overview.md) eller en [VPN Gateway VNET-till-VNET-anslutning](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
  - Om de virtuella nätverken finns i olika regioner stöds geo-replikering med VNET-peering, men en virtuell klientdator i VNET 1 (region 1) kan inte komma åt cachen i VNET 2 (region 2) via dns-namnet på grund av en begränsning med grundläggande interna lastbalanserare. Mer information om begränsningar för VNET-peering finns [i Virtual Network – Peering – Krav och begränsningar.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Den rekommenderade lösningen är att använda en VPN Gateway VNET-till-VNET-anslutning.
  
Med [den här Azure-mallen](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kan du snabbt distribuera två geo-replikerade cacheminnen till ett VNET som är anslutet VPN Gateway en VNET-till-VNET-anslutning.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Vad är replikeringsschemat för Geo-replikering i Redis?

Replikeringen är kontinuerlig och asynkron och sker inte enligt ett specifikt schema. Alla skrivningar som görs till den primära replikeras omedelbart och asynkront på den sekundära.

### <a name="how-long-does-geo-replication-replication-take"></a>Hur lång tid tar replikeringen av geo-replikering?

Replikeringen är inkrementell, asynkron och kontinuerlig och den tid det tar skiljer sig inte mycket från svarstiden mellan regioner. Under vissa omständigheter kan den sekundära cachen krävas för att göra en fullständig synkronisering av data från den primära. Replikeringstiden i det här fallet beror på antalet faktorer som: belastning på den primära cachen, tillgänglig nätverksbandbredd och svarstid mellan regioner. Vi har upptäckt att replikeringstiden för ett fullständigt 53 GB georeplikerat par kan vara mellan 5 och 10 minuter.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garanteras replikeringsåterställningspunkten?

För cacheminnen i ett geo-replikerat läge är persistence inaktiverat. Om ett geo-replikerat par är avlänkat, till exempel en kundinitierad redundans, behåller den sekundära länkade cachen synkroniserade data fram till den tidpunkten. Ingen återställningspunkt garanteras i sådana situationer.

Om du vill hämta en återställningspunkt [exporterar du](cache-how-to-import-export-data.md#export) från någon av cacheminnena. Du kan senare [importera till](cache-how-to-import-export-data.md#import) den primära länkade cachen.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Kan jag använda PowerShell eller Azure CLI för att hantera geo-replikering?

Ja, geo-replikering kan hanteras med hjälp av Azure Portal, PowerShell eller Azure CLI. Mer information finns i [PowerShell-dokument eller](/powershell/module/az.rediscache/#redis_cache) [Azure CLI-dokument.](/cli/azure/redis/server-link)

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Hur mycket kostar det att replikera mina data mellan Azure-regioner?

När du använder geo-replikering replikeras data från den primära länkade cachen till den sekundära länkade cachen. Dataöverföringen debiteras inte om de två länkade cacheminnena finns i samma region. Om de två länkade cacheminnena finns i olika regioner är dataöverföringsavgiften den utgående nätverkskostnaden för data som flyttas mellan endera region. Mer information finns i [Prisinformation för bandbredd.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Varför misslyckades åtgärden när jag försökte ta bort mitt länkade cacheminne?

Geo-replikerade cacheminnen och deras resursgrupper kan inte tas bort när de är länkade förrän du tar bort länken för geo-replikering. Om du försöker ta bort resursgruppen som innehåller en eller båda av de länkade cacheminnena tas de andra resurserna i resursgruppen bort, men resursgruppen förblir i tillståndet och eventuella länkade cacheminnen i resursgruppen är kvar i `deleting` `running` tillståndet. Om du vill ta bort resursgruppen helt och de länkade cacheminnena i den tar du bort länken till cacheminnena enligt beskrivningen i Ta [bort en geo-replikeringslänk.](#remove-a-geo-replication-link)

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Vilken region ska jag använda för min sekundära länkade cache?

I allmänhet rekommenderar vi att cacheminnet finns i samma Azure-region som det program som har åtkomst till den. För program med separata primära regioner och återställningsregioner rekommenderar vi att dina primära och sekundära cacheminnen finns i samma regioner. Mer information om parkopplade regioner finns i Best Practices – Azure Paired regions (Metodtips [– Azure-parkopplade regioner).](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hur fungerar det att gå över till den sekundära länkade cachen?

Automatisk redundans i Azure-regioner stöds inte för geo-replikerade cacheminnen. I ett haveriberedskapsscenario bör kunderna ta fram hela programstacken på ett samordnat sätt i sin säkerhetskopieringsregion. Att låta enskilda programkomponenter bestämma när de ska växla till sina säkerhetskopior på egen hand kan påverka prestanda negativt. En av de viktigaste fördelarna med Redis är att det är ett lager med mycket korta svarstider. Om kundens huvudprogram finns i en annan region än dess cacheminne skulle den tillagda tur och retur-tiden ha en märkbar inverkan på prestandan. Därför undviker vi automatisk fel vid fel på grund av tillfälliga tillgänglighetsproblem.

Om du vill starta en kundinitierad redundans tar du först bort länken till cacheminnena. Ändra sedan Redis-klienten så att den använder anslutningsslutpunkten för den (tidigare länkade) sekundära cachen. När de två cacheminnena har avlänkats blir den sekundära cachen en vanlig läs/skriv-cache igen och accepterar begäranden direkt från Redis-klienter.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Kan jag konfigurera en brandvägg med geo-replikering?

Ja, du kan konfigurera en [brandvägg](./cache-configure.md#firewall) med geo-replikering. För att geo-replikering ska fungera tillsammans med en brandvägg, se till att den sekundära cachens IP-adress läggs till i brandväggsreglerna för den primära cachen.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Cache for Redis funktioner.

* [Azure Cache for Redis tjänstnivåer](cache-overview.md#service-tiers)
* [Hög tillgänglighet för Azure Cache for Redis](cache-high-availability.md)
