---
title: 'ExpressRoute: flytta kretsar från klassisk till Azure Resource Manager'
description: Lär dig mer om vad som händer när du flyttar en Azure ExpressRoute-krets från den klassiska till Azure Resource Manager distributions modellen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97807949"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Flytta ExpressRoute-kretsar från den klassiska till Resource Manager-distributionsmodellen
Den här artikeln innehåller en översikt över vad som händer när du flyttar en Azure ExpressRoute-krets från den klassiska till Azure Resource Manager distributions modell.

Du kan använda en enda ExpressRoute-krets för att ansluta virtuella nätverk som distribueras både i de klassiska och Resource Manager-distributions modellerna.

![En ExpressRoute-krets som länkar till virtuella nätverk via båda distributionsmodellerna](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>ExpressRoute-kretsar som skapas i den klassiska distributionsmodellen
ExpressRoute-kretsar som skapats i den klassiska distributions modellen måste migreras till distributions modellen för Resource Manager först. Du kan bara aktivera anslutning till både de klassiska och Resource Manager-distributions modellerna. Anslutningen går inte förlorad eller avbryts när en anslutning flyttas. Alla länkar från krets till virtuella nätverk i den klassiska distributions modellen i samma prenumeration och kors prenumeration bevaras.

När flytten har slutförts fungerar ExpressRoute-kretsen exakt som en ExpressRoute-krets som skapades i distributions modellen för Resource Manager. Du kan nu skapa anslutningar till virtuella nätverk i Resource Manager-distributionsmodellen.

När du har flyttat ExpressRoute-kretsen till distributions modellen för Resource Manager kan du bara hantera den i distributions modellen för Resource Manager. Åtgärder för att hantera peering, uppdatera krets egenskaper och ta bort kretsar är bara tillgängliga via distributions modellen för Resource Manager. I följande avsnitt finns mer information om hur du kan hantera åtkomst till båda distributions modellerna.

Du behöver inte involvera din anslutnings leverantör för att flytta kretsen till distributions modellen för Resource Manager.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>ExpressRoute-kretsar som skapas i Resource Manager-distributionsmodellen
Du kan aktivera ExpressRoute-kretsar som skapas i Resource Manager-distributionsmodellen till att vara tillgängliga från båda distributionsmodellerna. Alla ExpressRoute-kretsar i din prenumeration kan konfigureras att ha åtkomst från båda distributions modellerna.

* ExpressRoute-kretsar som har skapats i distributions modellen för Resource Manager har som standard inte åtkomst till den klassiska distributions modellen.
* ExpressRoute-kretsar som har flyttats från den klassiska distributions modellen till Resource Manager-distributions modellen är tillgängliga från båda distributions modellerna som standard.
* En ExpressRoute-krets har alltid åtkomst till distributions modellen för Resource Manager, oavsett om den skapades i Resource Manager eller den klassiska distributions modellen. Du kan skapa anslutningar till virtuella nätverk genom att följa anvisningarna för [hur du länkar virtuella nätverk](expressroute-howto-linkvnet-arm.md).
* Åtkomst till den klassiska distributionsmodellen styrs av parametern **allowClassicOperations** i ExpressRoute-kretsen.

> [!IMPORTANT]
> Alla kvoter som finns dokumenterade på sidan med [tjänstbegränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) gäller. Exempelvis kan en standardkrets ha högst 10 virtuella nätverkslänkar/anslutningar i både klassiska och Resource Manager-distributionsmodeller.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Styra åtkomst till den klassiska distributionsmodellen
Du kan aktivera en ExpressRoute-krets för att länka till virtuella nätverk i båda distributions modellerna. Det gör du genom att ställa in parametern **allowClassicOperations** på ExpressRoute-kretsen.

Genom att ange **allowClassicOperations** till TRUE kan du länka virtuella nätverk från båda distributionsmodellerna till ExpressRoute-kretsen. 
* Information om hur du länkar virtuella nätverk i den klassiska distributions modellen finns i [så här länkar du virtuella nätverk för den klassiska distributions modellen](expressroute-howto-linkvnet-classic.md).
* Information om hur du länkar virtuella nätverk i distributions modellen för Resource Manager finns i [så här länkar du virtuella nätverk i distributions modellen för Resource Manager](expressroute-howto-linkvnet-arm.md).

Genom att ange **allowClassicOperations** till FALSE blockeras kretsen från den klassiska distributionsmodellen. Alla virtuella nätverk som är länkade i den klassiska distributions modellen bevaras dock fortfarande. ExpressRoute-kretsen syns inte i den klassiska distributions modellen.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Åtgärder som stöds i den klassiska distributionsmodellen
Följande klassiska åtgärder stöds på en ExpressRoute-krets när **allowClassicOperations** har angetts till TRUE:

* Hämta information om ExpressRoute-kretsen
* Skapa/uppdatera/hämta/ta bort virtuella nätverkslänkar till klassiska virtuella nätverk
* Skapa/uppdatera/hämta/ta bort auktoriseringar för virtuella nätverkslänkar till anslutningen mellan prenumerationer

Men när **allowClassicOperations** har angetts till true kan du inte köra följande klassiska åtgärder:

* Skapa/uppdatera/hämta/ta bort BGP-peerings (Border Gateway Protocol) för Azures privata, Azures offentliga och Microsoft-peerings
* Ta bort ExpressRoute-kretsar

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Kommunikation mellan klassiska och Resource Manager-distributionsmodeller
ExpressRoute-kretsen fungerar som en brygga mellan klassiska och Resource Manager-distributionsmodeller. Trafik mellan virtuella nätverk för båda distributions modellerna kan passera genom ExpressRoute-kretsen om båda virtuella nätverken är länkade till samma krets.

Det sammanlagda dataflödet begränsas av den virtuella nätverksgatewayens dataflödeskapacitet. Trafiken anger inte anslutnings leverantörens nätverk eller nätverket i sådana fall. Det fullständiga trafikflödet mellan de virtuella nätverken finns i Microsoft-nätverket.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Åtkomst till Azures offentliga och Microsofts peeringresurser
Du kan fortsätta att få åtkomst till resurser som normalt är tillgängliga via Azures offentliga peering och Microsofts peering utan störningar.  

## <a name="whats-supported"></a>Vad som stöds
I det här avsnittet beskrivs vad som stöds för ExpressRoute-kretsar:

* Du kan använda en enda ExpressRoute-krets för att få åtkomst till virtuella nätverk som har distribuerats i klassiska och Resource Manager-distributionsmodeller.
* Du kan flytta en ExpressRoute-krets från den klassiska till Resource Manager-distributionsmodellen. När ExpressRoute-kretsen har flyttats fortsätter den att fungerar som alla andra ExpressRoute-kretsar som skapas i distributions modellen för Resource Manager.
* Du kan bara flytta ExpressRoute-kretsen. Kretslänkar, virtuella nätverk och VPN-gatewayer kan inte flyttas med den här åtgärden.
* När en ExpressRoute-krets har flyttats till Resource Manager-distributionsmodellen kan du hantera livscykeln för ExpressRoute-kretsen med hjälp av modellen. Det innebär att du kan köra åtgärder som att lägga till/uppdatera/ta bort peer-kopplingar, uppdatera krets egenskaper (till exempel bandbredd, SKU och fakturerings typ) och ta bort kretsar endast i distributions modellen för Resource Manager.
* ExpressRoute-kretsen fungerar som en brygga mellan klassiska och Resource Manager-distributionsmodeller. Trafik mellan virtuella datorer i klassiska virtuella nätverk och virtuella datorer i Resource Manager virtuella nätverk kan kommunicera via ExpressRoute om båda virtuella nätverken är länkade till samma ExpressRoute-krets.
* Anslutningen mellan prenumerationer stöds i både de klassiska och Resource Manager-distributionsmodellerna.
* När du har flyttat en ExpressRoute-krets från den klassiska modellen till Azure Resource Manager-modellen kan du [migrera de virtuella nätverk som är länkade till ExpressRoute-kretsen](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Vad som inte stöds
I det här avsnittet beskrivs vad som inte stöds för ExpressRoute-kretsar:

* Hantera livscykeln för en ExpressRoute-krets från den klassiska distributionsmodellen.
* Azure-rollbaserad åtkomst kontroll (Azure RBAC) stöds för den klassiska distributions modellen. Du kan inte köra Azure RBAC-kontroller till en krets i den klassiska distributions modellen. En administratör/medadministratör för prenumerationen kan länka eller avlänka virtuella nätverk för kretsen.

## <a name="configuration"></a>Konfiguration
Följ anvisningarna som beskrivs i [Flytta en ExpressRoute-krets från den klassiska till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Nästa steg
* [Migrera de virtuella nätverk som är länkade till ExpressRoute-kretsen från den klassiska modellen till Azure Resource Manager-modellen](expressroute-migration-classic-resource-manager.md)
* Arbetsflödesinformation finns i [ExpressRoute-kretsens etablering av arbetsflöden och kretsstatus](expressroute-workflows.md).
* Så här konfigurerar du ExpressRoute-anslutningen:
  
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)

