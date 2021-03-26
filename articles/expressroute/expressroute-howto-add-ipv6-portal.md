---
title: 'Azure-ExpressRoute: Lägg till stöd för IPv6 med hjälp av Azure Portal'
description: Lär dig hur du lägger till IPv6-stöd för att ansluta till Azure-distributioner med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561669"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Lägg till stöd för IPv6 för privat peering med hjälp av Azure Portal (för hands version)

Den här artikeln beskriver hur du lägger till IPv6-stöd för att ansluta via ExpressRoute till dina resurser i Azure med hjälp av Azure Portal. 

> [!Note]
> Den här funktionen är för närvarande tillgänglig för för hands version i [Azure-regioner med Tillgänglighetszoner](../availability-zones/az-region.md#azure-regions-with-availability-zones). ExpressRoute-kretsen kan därför skapas med valfri peering-plats, men de IPv6-baserade distributioner som den ansluter till måste finnas i en region med Tillgänglighetszoner.

## <a name="register-for-public-preview"></a>Registrera dig för offentlig för hands version
Innan du lägger till stöd för IPv6 måste du först registrera din prenumeration. Registrera genom att köra följande kommandon via Azure PowerShell:

1.  Logga in på Azure och Välj prenumerationen. Kör de här kommandona för prenumerationen som innehåller din ExpressRoute-krets och prenumerationen som innehåller dina Azure-distributioner (om de är olika).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registrera din prenumeration för offentlig för hands version med följande kommando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Din begäran kommer sedan att godkännas av ExpressRoute-teamet inom 2-3 arbets dagar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Från en webbläsare går du till [Azure Portal](https://portal.azure.com)och loggar sedan in med ditt Azure-konto.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Lägg till en offentlig IPv6-peering till din ExpressRoute-krets

1. [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) eller navigera till den befintliga krets som du vill ändra.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Skärm bild av ExpressRoute krets List.":::

1. Välj **Azures privata** peering-konfiguration.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Skärm bild av ExpressRoute översikts sida.":::

1. Lägg till en privat IPv6-peering till din befintliga IPv4-privata peering-konfiguration genom att välja "båda" för **undernät** eller endast aktivera IPv6 privat peering på din nya krets genom att välja "IPv6". Ange ett par med/126 IPv6-undernät som du äger för din primära länk och sekundära länkar. Från vart och ett av dessa undernät tilldelar du den första användbara IP-adressen till routern som Microsoft använder den andra användbara IP-adressen för sin router. **Spara** peering-konfigurationen när du har angett alla parametrar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="Skärm bild av Lägg till IPv6 på privat peering-sida.":::

1. När konfigurationen har accepterats bör du se något som liknar följande exempel.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="Skärm bild av IPv6 konfigurerad för privat peering.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Uppdatera anslutningen till ett befintligt virtuellt nätverk

Följ stegen nedan om du har en befintlig miljö av Azure-resurser i en region med Tillgänglighetszoner som du vill använda din IPv6-privata peering med.

1. Navigera till det virtuella nätverk som din ExpressRoute-krets är ansluten till.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="Skärm bild av lista över virtuella nätverk.":::

1. Navigera till fliken **adress utrymme** och Lägg till ett IPv6-adressutrymme i det virtuella nätverket. **Spara** adress utrymmet.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="Skärm bild av Lägg till IPv6-adressutrymme i det virtuella nätverket.":::

1. Gå till fliken **undernät** och välj **GatewaySubnet**. Markera **Lägg till IPv6-adressutrymme** och ange ett IPv6-adressutrymme för ditt undernät. Gatewayens IPv6-undernät ska vara/64 eller större. **Spara** konfigurationen när du har angett alla parametrar.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="Skärm bild av Lägg till IPv6-adressutrymme till under nätet.":::

1. Om du har en befintlig zon – redundant Gateway navigerar du till din ExpressRoute-gateway och uppdaterar resursen för att aktivera IPv6-anslutning. Annars [skapar du den virtuella Nätverksgatewayen](expressroute-howto-add-gateway-portal-resource-manager.md) med en zon-redundant SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Om du planerar att använda FastPath använder du ErGw3AZ.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="Skärm bild av uppdateringen av gatewayen.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>Skapa en anslutning till ett nytt virtuellt nätverk

Följ stegen nedan om du planerar att ansluta till en ny uppsättning Azure-resurser i en region med Tillgänglighetszoner med din IPv6-privata peering.

1. Skapa ett virtuellt nätverk med dubbla staplar med både IPv4-och IPv6-adressutrymme. Mer information finns i [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md#create-a-virtual-network).

1. [Skapa Gateway-undernätet med dubbla stackar](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. [Skapa den virtuella Nätverksgatewayen](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) med en zon-redundant SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ). Om du planerar att använda FastPath använder du ErGw3AZ (Observera att det här alternativet endast är tillgängligt för kretsar som använder ExpressRoute Direct).

1. [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>Begränsningar
Även om IPv6-stöd är tillgängligt för anslutningar till distributioner i regioner med Tillgänglighetszoner, stöder den inte följande användnings fall:

* Anslutningar till distributioner i Azure via en icke-AZ ExpressRoute Gateway-SKU
* Anslutningar till distributioner i icke-AZ regioner
* Global Reach anslutningar mellan ExpressRoute-kretsar
* Användning av ExpressRoute med virtuellt WAN
* FastPath med icke-ExpressRoute Direct-kretsar
* FastPath med kretsar på följande peering-platser: Dubai
* Samexistens med VPN Gateway

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker ExpressRoute-problem finns i följande artiklar:

* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverks prestanda](expressroute-troubleshooting-network-performance.md)