---
title: 'Azure-ExpressRoute: Konfigurera Global Reach'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk och aktivera Global Reach.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: 2365317d83a4c11fa17cb5c449a25b70da17c2eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368374"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurera ExpressRoute Global Reach

Den här artikeln hjälper dig att konfigurera ExpressRoute Global Reach med hjälp av PowerShell. Mer information finns i [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen kontrollerar du följande:

* Du förstår [arbets flöden](expressroute-workflows.md)för ExpressRoute-krets etablering.
* Dina ExpressRoute-kretsar är i ett tillstånd som är etablerade.
* Azures privata peering har kon figurer ATS på dina ExpressRoute-kretsar.
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifiera kretsar

1. För att starta konfigurationen loggar du in på ditt Azure-konto och väljer den prenumeration som du vill använda.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifiera de ExpressRoute-kretsar som du vill använda. Du kan aktivera ExpressRoute Global Reach mellan den privata peering av två ExpressRoute-kretsar, så länge de finns i de länder/regioner som stöds och har skapats på olika peering-platser. 

   * Om din prenumeration äger båda kretsarna kan du välja någon av kretsarna för att köra konfigurationen i följande avsnitt.
   * Om de två kretsarna finns i olika Azure-prenumerationer måste du auktorisera från en Azure-prenumeration. Sedan skickar du in verifierings nyckeln när du kör konfigurations kommandot i den andra Azure-prenumerationen.

## <a name="enable-connectivity"></a>Aktivera anslutning

Aktivera anslutningar mellan dina lokala nätverk. Det finns separata uppsättningar instruktioner för kretsar som finns i samma Azure-prenumeration och kretsar som är olika prenumerationer.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>ExpressRoute-kretsar i samma Azure-prenumeration

1. Använd följande kommandon för att hämta krets 1 och krets 2. De två kretsarna finns i samma prenumeration.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Kör följande kommando mot krets 1 och skicka in det privata peering-ID: t för krets 2. Tänk på följande när du kör kommandot:

   * Det privata peering-ID: t ser ut ungefär som i följande exempel: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* måste vara ett/29 IPv4-undernät, till exempel "10.0.0.0/29". Vi använder IP-adresser i det här under nätet för att upprätta anslutningar mellan de två ExpressRoute-kretsarna. Du bör inte använda adresserna i det här under nätet i dina virtuella Azure-nätverk eller i ditt lokala nätverk.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Spara konfigurationen på krets 1 enligt följande:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När föregående åtgärd har slutförts kommer du att ha anslutning mellan dina lokala nätverk på båda sidorna genom dina två ExpressRoute-kretsar.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om de två kretsarna inte finns i samma Azure-prenumeration behöver du behörighet. I följande konfiguration genereras auktorisering i krets 2-prenumerationen och auktoriseringsregeln skickas till kretsen 1.

1. Generera en nyckel för autentisering.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Anteckna ID för privat peering för krets 2, samt verifierings nyckeln.
2. Kör följande kommando mot krets 1. Skicka in det privata peering-ID: t för krets 2 och verifierings nyckeln.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Spara konfigurationen på krets 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

När föregående åtgärd har slutförts kommer du att ha anslutning mellan dina lokala nätverk på båda sidorna genom dina två ExpressRoute-kretsar.

## <a name="verify-the-configuration"></a>Verifiera konfigurationen

Använd följande kommando för att kontrol lera konfigurationen på kretsen där konfigurationen gjordes (till exempel krets 1 i föregående exempel).
```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Om du bara kör *$ckt _1* i PowerShell visas *CircuitConnectionStatus* i utdata. Det visar om anslutningen har upprättats, "ansluten" eller "frånkopplad". 

## <a name="disable-connectivity"></a>Inaktivera anslutningar

Om du vill inaktivera anslutningar mellan dina lokala nätverk kör du kommandona mot den krets där konfigurationen gjordes (till exempel krets 1 i föregående exempel).

```azurepowershell-interactive
$ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Du kan köra åtgärden Hämta för att verifiera statusen.

När den föregående åtgärden har slutförts har du inte längre någon anslutning mellan ditt lokala nätverk via dina ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg
1. [Läs mer om ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verifiera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
3. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)
