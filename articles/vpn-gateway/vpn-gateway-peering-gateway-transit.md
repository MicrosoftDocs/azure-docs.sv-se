---
title: Konfigurera VPN-gatewayöverföring för peer-kopplade virtuella nätverk
description: Konfigurera Gateway-överföring för peering av virtuella nätverk för att sömlöst ansluta två virtuella Azure-nätverk till ett för anslutnings syfte.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872187"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurera VPN-gatewayöverföring för peer-kopplade virtuella nätverk

Den här artikeln beskriver hur du konfigurerar gatewayöverföring för peer-kopplade virtuella nätverk. [Peer-kopplade virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) syftar på två virtuella Azure-nätverk som kopplats ihop och slagits samman för anslutningsändamål. [Gateway-överföring](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) är en peering-egenskap som låter ett virtuellt nätverk använda VPN-gatewayen i det peer-anslutna virtuella nätverket för anslutningar mellan olika platser eller VNet-till-VNET-anslutning. Följande diagram illustrerar hur gatewayöverföring fungerar med peer-kopplade virtuella nätverk.

![Diagram över Gateway-överföring](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

I diagrammet gör gatewayöverföringen att de peer-kopplade virtuella nätverken kan använda Azure VPN-gatewayen i Hub-RM. Anslutningar som är tillgängliga på VPN-gatewayen, inklusive S2S, P2S och anslutningar mellan virtuella nätverk gäller för alla tre virtuella nätverk. Överförings alternativet är tillgängligt för peering mellan samma eller olika distributions modeller. Om du konfigurerar överföring mellan olika distributions modeller måste hubbens virtuella nätverk och virtuella nätverksgateway vara i distributions modellen för Resource Manager, inte den klassiska distributions modellen.
>

I nätverksarkitekturer av typen ”nav och eker” (hub-and-spoke) innebär gatewayöverföring att virtuella ekernätverk kan dela VPN-gatewayen i navet, i stället för att en VPN-gateway distribueras i varje virtuellt ekernätverk. Vägarna till de gatewayanslutna virtuella nätverken eller till de lokala nätverken distribueras till de peer-kopplade virtuella nätverkens routningstabeller med hjälp av gatewayöverföring. Du kan inaktivera den automatiska distributionen av vägar från VPN-gatewayen. Skapa en routningstabell med alternativet ”**Inaktivera spridning av BGP-väg**” och associera routningstabellen till undernäten om du vill förhindra distribution av vägar till dessa undernät. Mer information finns i [routningstabellen för virtuella nätverk](../virtual-network/manage-route-table.md).

Det finns två scenarier i den här artikeln:

* **Samma distributions modell**: båda virtuella nätverken skapas i distributions modellen för Resource Manager.
* **Olika distributions modeller**: det virtuella eker-nätverket skapas i den klassiska distributions modellen och det virtuella hubb nätverket och gatewayen finns i distributions modellen för Resource Manager.

>[!NOTE]
> Om du gör en ändring i nätverkets topologi och har Windows VPN-klienter, måste VPN-klientprogrammet för Windows-klienter laddas ned och installeras igen för att ändringarna ska tillämpas på klienten.
>

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har följande virtuella nätverk och behörigheter:

### <a name="virtual-networks"></a><a name="vnet"></a>Virtuella nätverk

|Virtuellt nätverk|Distributionsmodell| Virtuell nätverksgateway|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Ja](tutorial-create-gateway-portal.md)|
| Eker-RM | [Resource Manager](./tutorial-site-to-site-portal.md)| Inga |
| Spoke-Classic | [Form](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | Inga |

### <a name="permissions"></a><a name="permissions"></a>Behörigheter

De konton som du använder för att skapa peer-kopplade virtuella nätverk måste ha nödvändiga roller eller behörigheter. Om du peer-koppla de två virtuella nätverken med namnet **Hub-RM** och **ekrar-klassiskt** i exemplet nedan, måste ditt konto ha följande roller eller behörigheter för varje virtuellt nätverk:

|Virtuellt nätverk|Distributionsmodell|Roll|Behörigheter|
|---|---|---|---|
|Hub-RM|Resource Manager|[Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisk|[Klassisk nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Ej tillämpligt|
|Spoke-Classic|Resource Manager|[Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisk|[Klassisk nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och hur du tilldelar särskilda behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (endast Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Samma distributions modell

I det här scenariot är de virtuella nätverken båda i distributions modellen för Resource Manager. Använd följande steg för att skapa eller uppdatera de virtuella nätverkets peering för att aktivera Gateway-överföring.

### <a name="to-add-a-peering-and-enable-transit"></a>Så här lägger du till en peering och aktiverar överföring

1. Skapa eller uppdatera det virtuella nätverkets peering från hubben – RM i [Azure Portal](https://portal.azure.com). Navigera till **hubben-RM** Virtual Network. Välj **peer** kopplingar och sedan **+ Lägg** till för att öppna **Lägg till peering**.
1. Konfigurera värdena för **det här virtuella nätverket** på sidan **Lägg till peering** .

   * Namn på peering-länk: ge länken ett namn. Exempel: **HubRMToSpokeRM**
   * Trafik till fjärranslutet virtuellt nätverk: **Tillåt**
   * Trafik som vidarebefordras från fjärran slutet virtuellt nätverk: **Tillåt**
   * Virtuell nätverksgateway: **Använd det här virtuella nätverkets gateway**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Skärm bild som visar Lägg till peering.":::

1. På samma sida fortsätter du med att konfigurera värdena för det **virtuella fjärrnätverket**.

   * Namn på peering-länk: ge länken ett namn. Exempel: **SpokeRMtoHubRM**
   * Distributions modell: **Resource Manager**
   * Virtual Network: **ekrar – RM**
   * Trafik till fjärranslutet virtuellt nätverk: **Tillåt**
   * Trafik som vidarebefordras från fjärran slutet virtuellt nätverk: **Tillåt**
   * Virtuell nätverksgateway: **Använd det fjärranslutna virtuella nätverkets gateway**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Skärm bild som visar värden för fjärran sluten virtuellt nätverk.":::

1. Välj **Lägg till** för att skapa peer koppling.
1. Kontrol lera peering-statusen som **ansluten** i båda de virtuella nätverken.

### <a name="to-modify-an-existing-peering-for-transit"></a>Ändra en befintlig peering för överföring

Om peering redan har skapats kan du ändra peering för överföring.

1. Navigera till det virtuella nätverket. Välj **peer** kopplingar och välj den peering som du vill ändra.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Skärm bild som visar Välj peering.":::

1. Uppdatera VNet-peering.

   * Trafik till fjärranslutet virtuellt nätverk: **Tillåt**
   * Trafik som vidarebefordras till det virtuella nätverket; **Tillåt**
   * Virtuell nätverksgateway: **Använd det virtuella** nätverkets gateway

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Skärm bild som visar modifiera peering-Gateway.":::

1. **Spara** peering-inställningarna.

### <a name="powershell-sample"></a><a name="ps-same"></a>PowerShell-exempel

Du kan också använda PowerShell för att skapa eller uppdatera peer-kopplingen i exemplet ovan. Ersätt variablerna med namnen på dina virtuella nätverk och resursgrupper.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different-deployment-models"></a><a name="different"></a>Olika distributions modeller

I den här konfigurationen är eker VNet **ekrar – klassisk** i den klassiska distributions modellen och Hub VNet **-hubben – RM** finns i distributions modellen för Resource Manager. När du konfigurerar överföring mellan distributions modeller måste den virtuella Nätverksgatewayen konfigureras för Resource Manager VNet, inte det klassiska VNet.

För den här konfigurationen behöver du bara konfigurera **hubben – RM** Virtual Network. Du behöver inte konfigurera något i **eker-klassiskt** VNet.

1. I Azure Portal navigerar du till **hubben-RM** Virtual Network, väljer **peer**-kopplingar och väljer sedan **+ Lägg till**.
1. Konfigurera följande värden på sidan **Lägg till peering** :

   * Namn på peering-länk: ge länken ett namn. Exempel: **HubRMToClassic**
   * Trafik till fjärranslutet virtuellt nätverk: **Tillåt**
   * Trafik som vidarebefordras från fjärran slutet virtuellt nätverk: **Tillåt**
   * Virtuell nätverksgateway: **Använd det här virtuella nätverkets gateway**
   * Fjärran slutet virtuellt nätverk: **klassiskt**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Lägg till peering-sida för eker – klassisk":::

1. Kontrol lera att prenumerationen är korrekt och välj sedan det virtuella nätverket i list rutan.
1. Välj **Lägg till** för att lägga till peer kopplingen.
1. Kontrol lera peering-statusen som **ansluten** på hubben – RM Virtual Network. 

I den här konfigurationen behöver du inte konfigurera något i det **ekrar-klassiska** virtuella nätverket. När statusen **är ansluten** kan det virtuella nätverket i eker använda anslutningen via VPN-gatewayen i det virtuella hubb nätverket.

### <a name="powershell-sample"></a><a name="ps-different"></a>PowerShell-exempel

Du kan också använda PowerShell för att skapa eller uppdatera peer-kopplingen i exemplet ovan. Ersätt variablerna och prenumerations-ID:t med värdena för ditt virtuella nätverk, dina resursgrupper och din prenumeration. Du behöver bara skapa peer-kopplingen för det virtuella navnätverket.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [begränsningar och beteenden vid peer-koppling av virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) samt om [inställningar för peer-koppling av virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#create-a-peering) innan du skapar en peer-koppling av virtuella nätverk för användning i produktion.
* Lär dig hur du [skapar topologin för ett nav- och ekernätverk](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peer-koppling av virtuella nätverk och gatewayöverföring.
* [Skapa virtuell nätverks-peering med samma distributions modell](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Skapa virtuell nätverks-peering med olika distributions modeller](../virtual-network/create-peering-different-deployment-models.md).