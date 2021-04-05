---
title: 'Lägg till flera VPN gateway plats-till-plats-anslutningar till ett VNet: Azure Portal'
description: Lägg till S2S-anslutningar på flera platser till en VPN-gateway som har en befintlig anslutning
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890176"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Lägg till ytterligare S2S-anslutningar till ett VNet: Azure Portal

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>

Den här artikeln hjälper dig att lägga till ytterligare en plats-till-plats-anslutning (S2S) till en VPN-gateway som har en befintlig anslutning. Den här arkitekturen kallas ofta för en "konfiguration för flera platser". Du kan lägga till en S2S-anslutning till ett VNet som redan har en S2S-anslutning, punkt-till-plats-anslutning eller VNet-till-VNet-anslutning. Det finns vissa begränsningar när du lägger till anslutningar. Se avsnittet [krav](#before) i den här artikeln för att verifiera innan du startar konfigurationen.

Den här artikeln gäller Resource Manager-virtuella nätverk som har en Routningsbaserad VPN-gateway. De här stegen gäller inte för nya ExpressRoute/plats-till-plats-sambefintliga anslutnings konfigurationer. Men om du bara lägger till en ny VPN-anslutning till en redan befintlig, kan du använda de här stegen. Se [ExpressRoute/S2S-befintliga anslutningar](../expressroute/expressroute-howto-coexist-resource-manager.md) för information om sambefintliga anslutningar.

## <a name="prerequisites"></a><a name="before"></a>Förutsättningar

Verifiera följande objekt:

* Du konfigurerar inte en ny samexisterande ExpressRoute och VPN Gateway konfiguration.
* Du har ett virtuellt nätverk som har skapats med hjälp av distributions modellen Resource Manager med en befintlig anslutning.
* Den virtuella Nätverksgatewayen för ditt VNet är Routningsbaserad. Om du har en principbaserad VPN-gateway måste du ta bort den virtuella Nätverksgatewayen och skapa en ny VPN-gateway som Routningsbaserad.
* Inget av adress intervallen överlappar någon av de virtuella nätverk som det här virtuella nätverket ansluter till.
* Du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* Du har en extern offentlig IP-adress för VPN-enheten.

## <a name="configure-a-connection"></a><a name="configure"></a>Konfigurera en anslutning

1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
1. Välj **alla resurser** och leta upp din **virtuella nätverksgateway** från listan över resurser och markera den.
1. På sidan **virtuell nätverksgateway** väljer du **anslutningar**.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN gateway-anslutningar":::
1. På sidan **anslutningar** väljer du **+ Lägg till**.
1. Då öppnas sidan **Lägg till anslutning** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Sidan Lägg till anslutning":::
1. På sidan **Lägg till anslutning** fyller du i följande fält:

   * **Namn:** Det namn som du vill ge till den plats som du skapar anslutningen till.
   * **Anslutnings typ:** Välj **plats-till-plats (IPSec)**.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Lägg till en lokal nätverksgateway

1. För fältet **lokal nätverksgateway** väljer du **_Välj en lokal nätverksgateway_*_. Detta öppnar sidan _* Välj lokal nätverksgateway** .
1. Välj **+ Skapa ny** för att öppna sidan **skapa lokal** nätverksgateway.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Sidan skapa lokal nätverksgateway":::
1. På sidan **skapa lokal** nätverksgateway fyller du i följande fält:

   * **Namn:** Det namn som du vill ge till resursen för den lokala Nätverksgatewayen.
   * **Slut punkt:** Den offentliga IP-adressen för VPN-enheten på den plats som du vill ansluta till eller FQDN för slut punkten.
   * **Adress utrymme:** Adress utrymmet som du vill ska dirigeras till den nya lokala nätverks platsen.
1. Välj **OK** på sidan **skapa lokal** nätverksgateway för att spara ändringarna.

## <a name="add-the-shared-key"></a><a name="part3"></a>Lägg till den delade nyckeln

1. När du har skapat den lokala Nätverksgatewayen går du tillbaka till sidan **Lägg till anslutning** .
1. Fyll i återstående fält. För den **delade nyckeln (PSK)** kan du antingen hämta den delade nyckeln från din VPN-enhet eller göra en här och konfigurera VPN-enheten så att den använder samma delade nyckel. Det viktiga är att nycklarna är exakt desamma.

## <a name="create-the-connection"></a><a name="create"></a>Skapa anslutningen

1. Klicka på **OK** längst ned på sidan för att skapa anslutningen. Anslutningen börjar skapas omedelbart.
1. När anslutningen är klar kan du Visa och kontrol lera den.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Visa och verifiera VPN-anslutningen

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [utbildnings vägar för virtuella datorer](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
