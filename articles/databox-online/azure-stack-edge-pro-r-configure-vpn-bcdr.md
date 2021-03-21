---
title: Konfigurera verksamhets kontinuitet och haveri beredskap (BCDR) på Azure Stack Edge virtuellt privat nätverk (VPN)
description: Beskriver hur du konfigurerar BCDR på Azure Stack Edge VPN.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367698"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Konfigurera verksamhets kontinuitet och haveri beredskap för Azure Stack Edge VPN

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du konfigurerar verksamhets kontinuitet och haveri beredskap (BCDR) på ett virtuellt privat nätverk (VPN) som kon figurer ATS på en Azure Stack Edge-enhet.

Den här artikeln gäller både Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheten.

## <a name="configure-failover-to-a-paired-region"></a>Konfigurera redundans till en kopplad region

Azure Stack Edge-enheten använder andra Azure-tjänster, till exempel Azure Storage. Du kan konfigurera BCDR på valfri Azure-tjänst som används av Azure Stack Edge-enheten. Om en Azure-tjänst som används av Azure Stack Edge växlar till dess kopplade region, kommer Azure Stack Edge-enheten nu att ansluta till de nya IP-adresserna och kommunikationen kommer inte att vara dubblerad. 

Azure Stack Edge-enheten använder delade tunnlar och alla data och tjänster som har kon figurer ATS i hem regionen (den region som är kopplad till din Azure Stack Edge-enhet) går över VPN-tunneln. Om Azure-tjänsterna växlar över till en kopplad region som ligger utanför hem regionen, går det inte längre att gå över VPN och därför är den inte en dubblerad kryptering. 

I det här scenariot påverkas vanligt vis bara en fåtal av Azure-tjänster. För att lösa det här problemet ska följande ändringar göras i Azure Stack Edge VPN-konfigurationen:

1. Lägg till Azure-tjänstens IP-intervall i de inkluderade vägarna för VPN på Azure Stack Edge. Tjänsterna börjar sedan att dirigeras via VPN.

    Om du vill lägga till inkluderade vägar måste du ladda ned den JSON-fil som innehåller tjänstens speciella vägar. Se till att uppdatera den här filen med de nya vägarna.
2. Lägg till motsvarande IP-intervall för Azure-tjänsten i Azure Route-tabellen.
3. Lägg till vägarna i brand väggen.

> [!NOTE]
>
> 1. Redundansväxlingen av en Azure VPN-gateway och Azure Virtual Network (VNET) behandlas i avsnittet [återställa från en Azure-region som misslyckades på grund av en katastrof](#recover-from-a-failed-azure-region).
> 2. IP-adressintervall som läggs till i Azure Route-tabellen kan överskrida gränsen på 400. Om detta inträffar måste du följa anvisningarna i avsnittet, [flytta från en Azure-region till en annan Azure-region](#move-from-an-azure-region-to-another).

## <a name="recover-from-a-failed-azure-region"></a>Återställ från en misslyckad Azure-region

I händelse av att hela Azure-regionen växlar över till följd av en katastrof händelse, till exempel jord bävning, kommer alla Azure-tjänster i regionen, inklusive Azure Stack Edge-tjänsten att redundansväxla. Eftersom det finns flera tjänster kan de inkluderade vägarna enkelt variera till några hundra. Azure har en begränsning på 400 vägar. 

När regionen växlar över växlar det virtuella nätverket (VNet) också över till den nya regionen, så den virtuella Nätverksgatewayen (VPN-gateway). Om du vill åtgärda den här ändringen gör du följande ändringar i din Azure Stack Edge VPN-konfiguration:

1. Flytta ditt VNet till mål regionen. Mer information finns i: [Flytta ett virtuellt Azure-nätverk till en annan region via Azure Portal](../virtual-network/move-across-regions-vnet-portal.md).
2. Distribuera en ny Azure VPN-gateway i mål regionen där du flyttade VNet. Mer information finns i [skapa en virtuell nätverksgateway](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Uppdatera Azure Stack Edge VPN-konfiguration för att använda VPN-gatewayen ovan i VPN-anslutningen och välj sedan mål regionen för att lägga till vägar som använder VPN-gatewayen.
4. Uppdatera tabellen inkommande Azure-väg om klient-adresspoolen också ändras. 

## <a name="move-from-an-azure-region-to-another"></a>Flytta från en Azure-region till en annan

Du kan flytta Azure Stack gräns enheten från en plats till en annan plats. Om du vill använda en region som är närmast den plats där enheten distribueras måste du konfigurera enheten för en ny hem region. Gör följande ändringar:

1. Du kan uppdatera Azure Stack Edge VPN-konfiguration för att använda en ny regions VPN-gateway och välja den nya regionen för att lägga till vägar som använder VPN gateway.

## <a name="next-steps"></a>Nästa steg

[Säkerhetskopiera Azure Stack Edge-enheten](azure-stack-edge-gpu-prepare-device-failure.md).