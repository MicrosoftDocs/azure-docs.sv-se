---
title: 'Självstudie: Konfigurera peering för ExpressRoute-krets – Azure PowerShell'
description: Den här självstudien visar hur du skapar och hanterar konfiguration av routning för en ExpressRoute-krets i distributions modellen för Resource Manager med hjälp av PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721947"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Självstudie: skapa och ändra peering för en ExpressRoute-krets med PowerShell

Den här självstudien hjälper dig att skapa och hantera konfiguration av routning för en ExpressRoute-krets i distributions modellen för Resource Manager med hjälp av PowerShell. Du kan också kontrol lera status, uppdatera eller ta bort och avetablera peer-kopplingar för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Offentlig peering](about-public-peering.md)
> * [Video-privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis en IPVPN, t. ex. MPLS), kommer anslutnings leverantören konfigurera och hantera routning åt dig.

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontakta tjänst leverantören innan du konfigurerar BGP-peering.
> 

Du kan konfigurera privat peering och Microsoft-peering för en ExpressRoute-krets (offentlig Azure-peering är inaktuell för nya kretsar). Peering kan konfigureras i vilken ordning du väljer. Dock måste du se till att du slutför konfigurationen av en peering i taget. Mer information om routningsdomäner och peering-domäner finns i [ExpressRoute](expressroute-circuit-peerings.md). Information om offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Konfigurera, uppdatera och ta bort Microsoft-peering för en krets
> - Konfigurera, uppdatera och ta bort Azures privata peering för en krets

## <a name="prerequisites"></a>Förutsättningar

* Kontrol lera att du har granskat följande sidor innan du påbörjar konfigurationen:
    * [Förutsättningar](expressroute-prerequisites.md) 
    * [Routningskrav](expressroute-routing.md)
    * [Arbetsflöden](expressroute-workflows.md)
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och se till att kretsen aktive ras av anslutnings leverantören innan du fortsätter. ExpressRoute-kretsen måste vara i ett tillhandahållet och aktiverat tillstånd för att du ska kunna köra cmdletarna i den här artikeln.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Microsofts peering-konfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänst prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Mer information finns i [Konfigurera ett flödes filter för Microsoft-peering](how-to-routefilter-powershell.md).
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Logga in och välj din prenumeration.

   Om du har installerat PowerShell lokalt loggar du in. Om du använder Azure Cloud Shell kan du hoppa över det här steget.

   ```azurepowershell
   Connect-AzAccount
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.

   Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Microsoft-peering åt dig. Du behöver inte följa instruktionerna som anges i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg. 

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Svaret liknar följande exempel:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Konfigurera Microsoft-peering för kretsen. Kontrol lera att du har följande information innan du fortsätter.

   * Ett/30-eller/126-undernät för den primära länken. Adress blocket måste vara ett giltigt offentligt IPv4-eller IPv6-prefix som du äger och registrerat i en RIR/IR.
   * Ett/30-eller/126-undernät för den sekundära länken. Adress blocket måste vara ett giltigt offentligt IPv4-eller IPv6-prefix som du äger och registrerat i en RIR/IR.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: du anger en lista över alla prefix som du planerar att annonsera över BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR. IPv4 BGP-sessioner kräver IPv4-annonserade prefix och IPv6 BGP-sessioner kräver IPv6-annonserade prefix. 
   * Routningens registernamn: Du kan ange den RIR/IR mot vilken AS-numret och prefixet är registrerade.
   * Valfritt:
     * Kund-ASN: om du annonserar prefix som inte har registrerats på peering AS Number kan du ange det AS-nummer som de är registrerade på.
     * En MD5-hash om du väljer att använda en.

> [!IMPORTANT]
> Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas "AdvertisedPublicPrefixesState" som "verifiering krävs" i resultatet av "Get-AzExpressRouteCircuitPeeringConfig" (se "för att hämta information om Microsofts peering" i följande avsnitt). 
> 
> Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering genom att öppna ett support ärende. 
> 

   Använd följande exempel för att konfigurera Microsoft-peering för din krets:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Så här hämtar du Microsofts peering-information

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Så här uppdaterar du Microsofts peering-konfiguration

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Privat peering i Azure

Det här avsnittet hjälper dig att skapa, Hämta, uppdatera och ta bort Azures konfiguration för privata peering för en ExpressRoute-krets.

> [!IMPORTANT]
> IPv6-stöd för privat peering är för närvarande en **offentlig för hands version**. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Importera PowerShell-modulen för ExpressRoute.

   Installera det senaste installations programmet för PowerShell från [PowerShell-galleriet](https://www.powershellgallery.com/). Importera sedan Azure Resource Manager modulerna till PowerShell-sessionen för att börja använda ExpressRoute-cmdlets. Du måste köra PowerShell som administratör.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importera alla AZ. \* moduler inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Du kan också importera en SELECT-modul inom det kända semantiska versions intervallet.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Logga in på ditt konto.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Välj den prenumeration som du vill skapa ExpressRoute-kretsen.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Skapa en ExpressRoute-krets.

   Följ anvisningarna för att skapa en [ExpressRoute-krets](expressroute-howto-circuit-arm.md) och etablera den med anslutningsprovidern. Om din anslutnings leverantör erbjuder hanterade Layer 3-tjänster kan du be din anslutnings leverantör att aktivera Azures privata peering åt dig. Du behöver inte följa instruktionerna som anges i nästa avsnitt. Om din anslutnings leverantör inte hanterar routning åt dig, kan du, när du har skapat din krets, fortsätta med konfigurationen med hjälp av nästa steg.

3. Kontrol lera ExpressRoute-kretsen för att se till att den är etablerad och också aktive rad. Använd följande exempel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Svaret liknar följande exempel:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Konfigurera Azures privata peering för kretsen. Kontrol lera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett par undernät som inte ingår i något adress utrymme som är reserverat för virtuella nätverk. Ett undernät kommer att användas för den primära länken, medan det andra används för den sekundära länken. Från vart och ett av dessa undernät tilldelar du den första användbara IP-adressen till routern som Microsoft använder den andra användbara IP-adressen för sin router. Det finns tre alternativ för det här paret undernät:
       * IPv4: två/30 undernät.
       * IPv6: två/126-undernät.
       * Både: två/30 undernät och två/126-undernät.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen. Se till att du inte använder 65515.
   * Valfritt:
     * En MD5-hash om du väljer att använda en.

   Använd följande exempel för att konfigurera Azures privata peering för din krets:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Om du väljer att använda en MD5-hash använder du följande exempel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Kontrollera att du anger AS-talet som peering-ASN, inte kund-ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Så här hämtar du information om Azure privat peering

Du kan hämta konfigurations information med hjälp av följande exempel:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Uppdatera konfigurationen av Azures privata peering

Du kan uppdatera valfri del av konfigurationen med hjälp av följande exempel. I det här exemplet uppdateras VLAN-ID: t för kretsen från 200 till 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Så här tar du bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att köra följande cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Så här tar du bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att köra följande exempel:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global Reach-anslutningar tas bort innan du kör det här exemplet. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azures privata peering kan du skapa en ExpressRoute-Gateway för att länka ett virtuellt nätverk till kretsen. 

> [!div class="nextstepaction"]
> [Konfigurera en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
