---
title: 'Ansluta till ett virtuellt nätverk med P2S VPN & flera autentiseringstyper: Portal'
titleSuffix: Azure VPN Gateway
description: Anslut till ett virtuellt nätverk via P2S med flera autentiseringstyper i Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746705"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet med flera autentiseringstyper: Azure Portal

Den här artikeln hjälper dig att på ett säkert sätt ansluta enskilda klienter som kör Windows, Linux eller macOS till ett Azure VNet. Punkt-till-plats-VPN-anslutningar är användbara när du vill fjärransluta till ditt VNet, exempelvis när du distansarbetar från hemmet eller en konferens. Du kan också använda P2S i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet. Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol) eller IKEv2. Mer information om punkt-till-plats-VPN finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Ansluta från en dator till ett Azure VNet-punkt-till-plats-anslutnings diagram":::

Mer information om punkt-till-plats-VPN finns i [om punkt-till-plats-VPN](point-to-site-about.md). Om du vill skapa den här konfigurationen med hjälp av Azure PowerShell, se [Konfigurera en punkt-till-plats-VPN med Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

Flera autentiseringstyper på samma VPN-gateway stöds endast med OpenVPN tunnel typ.

### <a name="example-values"></a><a name="example"></a>Exempelvärden

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

* **Namn på virtuellt nätverk:** VNet1
* **Adress utrymme:** 10.1.0.0/16<br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk.
* **Under näts namn:** Delen
* **Adress intervall för under nätet:** 10.1.0.0/24
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp:** TestRG1
* **Plats:** USA, östra
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Namn på virtuell nätverksgateway:** VNet1GW
* **Gateway-typ:** Konfigurera
* **VPN-typ:** Route-baserad
* **Namn på offentlig IP-adress:** VNet1GWpip
* **Anslutnings typ:** Punkt-till-plats
* **Klient-adresspool:** 172.16.201.0/24<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från klientadresspoolen.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Skapa ett virtuellt nätverk

Kontrollera att du har en Azure-prenumeration innan du börjar. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Virtuell nätverksgateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

>[!NOTE]
>Basic Gateway-SKU: n stöder inte tunnel typen OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Klientadresspool

Klientens adresspool är ett intervall med privata IP-adresser som du anger. Klienterna som ansluter via en VPN-anslutning dynamiskt från punkt till plats får en IP-adress från det här intervallet. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Om du konfigurerar flera protokoll och SSTP är ett av protokollen, delas den konfigurerade adresspoolen upp mellan de konfigurerade protokollen på samma sätt.

1. När den virtuella nätverksgatewayen har skapats går du till avsnittet **Inställningar** på sidan för den virtuella nätverksgatewayen. I **Inställningar** väljer **du punkt-till-plats-konfiguration**. Välj **Konfigurera nu** för att öppna konfigurations sidan.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Skärm bild av sidan punkt-till-plats-konfiguration." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. På sidan **punkt-till-plats-konfiguration** kan du konfigurera olika inställningar. I rutan **adresspool** lägger du till det privata IP-adressintervall som du vill använda. VPN-klienter tar dynamiskt emot en IP-adress från intervallet som du anger. Den minsta under nät masken är 29 bitar för aktiv/passiv och 28-bit för aktiv/aktiv-konfiguration.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Skärm bild av adresspool.":::

1. Fortsätt till nästa avsnitt för att konfigurera autentiserings-och tunnel typer.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Autentiserings-och tunnel typer

I det här avsnittet konfigurerar du autentiseringstyp och tunnel typ. På sidan **punkt-till-plats-konfiguration** , om du inte ser **tunnel typ** eller **Autentiseringstyp**, använder din gateway Basic SKU. Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering. Om du vill använda de här inställningarna måste du ta bort och återskapa gatewayen med hjälp av en annan gateway-SKU.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Skärm bild av autentiseringstyp.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltyp

På sidan **punkt-till-plats-konfiguration** väljer du **OpenVPN (SSL)** som tunnel typ.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Autentiseringstyp

För **Autentiseringstyp** väljer du önskade typer. Alternativen är:

* Azure-certifikat
* RADIUS
* Azure Active Directory

Beroende på vilka autentiseringstyper som är markerade visas olika konfigurations inställnings fält som måste fyllas i. Fyll i den information som krävs och välj **Spara** längst upp på sidan för att spara alla konfigurations inställningar.

Mer information om autentiseringstyp finns i:

* [Azure-certifikat](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Konfigurations paket för VPN-klient

VPN-klienter måste konfigureras med klient konfigurations inställningar. Konfigurations paketet för VPN-klienten innehåller filer med inställningarna för att konfigurera VPN-klienter för att ansluta till ett VNet via en P2S-anslutning.

Anvisningar för hur du skapar och installerar konfigurationsfiler för VPN-klienter får du med hjälp av artikeln som hör till din konfiguration:

* [Skapa och installera konfigurationsfiler för VPN-klienten för interna P2s-konfigurationer för Azure-certifikatautentisering](point-to-site-vpn-client-configuration-azure-cert.md).
* [Azure Active Directory autentisering: Konfigurera en VPN-klient för P2s OpenVPN-protokoll anslutningar](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

Det här avsnittet innehåller information om vanliga frågor och svar om punkt-till-plats-konfigurationer. Du kan också visa [VPN gateway vanliga frågor och svar](vpn-gateway-vpn-faq.md) om du vill ha mer information om VPN gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
