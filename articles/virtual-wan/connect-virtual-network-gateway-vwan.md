---
title: Ansluta en virtuell nätverksgateway till ett virtuellt Azure-nätverk
description: Den här artikeln hjälper dig att ansluta en virtuell Azure-nätverksgateway till en Azure Virtual WAN-Gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 469d7ba9e86751312ebf6a6c82b35f065ee6cb50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880380"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Anslut en VPN Gateway (virtuell nätverksgateway) till virtuellt WAN

Den här artikeln hjälper dig att konfigurera anslutning från en Azure-VPN Gateway (virtuell nätverksgateway) till ett virtuellt Azure-WAN-nätverk (VPN-gateway). Att skapa en anslutning från en VPN Gateway (virtuell nätverksgateway) till ett virtuellt WAN-nätverk (VPN gateway) är ungefär detsamma som att konfigurera anslutning till ett virtuellt WAN-nätverk från gren-VPN-platser.

För att minimera eventuell förvirring mellan två funktioner påbörjar vi gatewayen med namnet på den funktion som vi refererar till. Till exempel VPN Gateway virtuell nätverksgateway och virtuell WAN-Gateway.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar skapar du följande resurser:

Azure Virtual WAN

* [Skapa ett virtuellt WAN-nätverk](virtual-wan-site-to-site-portal.md#openvwan).
* [Skapa en hubb](virtual-wan-site-to-site-portal.md#hub). Den virtuella hubben innehåller den virtuella WAN-VPN-gatewayen.

Azure Virtual Network

* Skapa ett virtuellt nätverk utan några virtuella nätverks-gatewayer. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. skapa en VPN Gateway virtuell nätverksgateway

Skapa en **VPN gateway** virtuell nätverksgateway i aktivt-aktivt läge för det virtuella nätverket. När du skapar en gateway kan du antingen använda befintliga offentliga IP-adresser för de två instanserna av gatewayen, eller så kan du skapa nya offentliga IP-adresser. Du kommer att använda dessa offentliga IP-adresser när du konfigurerar virtuella WAN-platser. Mer information om aktiva och aktiva VPN-gatewayer och konfigurations steg finns i [Konfigurera aktiva, aktiva VPN-gatewayer](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Inställningen aktiv-aktiv läge

Aktivera aktivt-aktivt läge på sidan **konfiguration** av virtuell nätverksgateway.

![aktiv-aktiv](./media/connect-virtual-network-gateway-vwan/active.png "aktiv-aktiv")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-inställning

På sidan **konfiguration** av virtuell nätverksgateway kan du konfigurera **BGP ASN**. Ändra BGP-ASN. BGP ASN får inte vara 65515. 65515 kommer att användas av Azure Virtual WAN.

![Skärm bild som visar en konfigurations sida för virtuell nätverksgateway med konfigurera BGP ASN valt.](./media/connect-virtual-network-gateway-vwan/bgp.png "BGP")

### <a name="public-ip-addresses"></a><a name="pip"></a>Offentliga IP-adresser

När gatewayen har skapats går du till sidan **Egenskaper** . Egenskaperna och konfigurations inställningarna ser ut ungefär som i följande exempel. Observera de två offentliga IP-adresserna som används för gatewayen.

![egenskaperna](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. skapa virtuella WAN VPN-platser

Om du vill skapa virtuella WAN VPN-platser navigerar du till ditt virtuella WAN och väljer **VPN-platser** under **anslutning**. I det här avsnittet ska du skapa två virtuella WAN-webbplatser som motsvarar de virtuella Nätverksgatewayen som du skapade i föregående avsnitt.

1. Välj **+ Skapa webbplats**.
2. På sidan **skapa VPN-platser** anger du följande värden:

   * **Region** – samma region som Azure VPN gateway virtuell nätverksgateway.
   * **Enhets leverantör** – ange enhets leverantören (valfritt namn).
   * **Privat adress utrymme** – ange ett värde eller lämna tomt när BGP är aktiverat.
   * **Border Gateway Protocol** -Ställ in för att **aktivera** om Azure VPN gateway virtuell nätverksgateway har BGP aktiverat.
   * **Anslut till hubbar** – Välj den hubb som du skapade i kraven i list rutan. Om du inte ser en hubb kontrollerar du att du har skapat en plats-till-plats-VPN-gateway för hubben.
3. Under **länkar** anger du följande värden:

   * **Providernamn** – ange ett namn på länken och ett providernamn (valfritt namn).
   * **Hastighets** hastighet (valfritt tal).
   * **IP-adress** – ange IP-adress (samma som den första offentliga IP-adressen som visas under (VPN gateway) egenskaper för virtuell nätverksgateway).
   * **BGP-adress** och **ASN** -BGP-adress och ASN. De måste vara samma som en av BGP-peer-IP-adresserna och ASN från den VPN Gateway virtuella Nätverksgatewayen som du konfigurerade i [steg 1](#vnetgw).
4. Granska och välj **Bekräfta** för att skapa platsen.
5. Upprepa föregående steg för att skapa den andra platsen så att den matchar den andra instansen av den VPN Gateway virtuella Nätverksgatewayen. Du behåller samma inställningar, förutom att använda andra offentliga IP-adresser och andra IP-adresser för BGP-peer från VPN Gateway konfiguration.
6. Nu har två platser skapats och kan fortsätta till nästa avsnitt för att ladda ned konfigurationsfiler.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Hämta filerna för VPN-konfigurationen

I det här avsnittet laddar du ned VPN-konfigurationsfilen för var och en av de platser som du skapade i föregående avsnitt.

1. Längst upp på sidan virtuella WAN **-webbplatser** för virtuella nätverk väljer du **platsen** och väljer sedan **Ladda ned konfiguration för plats-till-plats-VPN**. Azure skapar en konfigurations fil med inställningarna.

   ![Skärm bild som visar sidan "VPN-webbplatser" med åtgärden "Ladda ned VPN-konfiguration för plats-till-plats" vald.](./media/connect-virtual-network-gateway-vwan/download.png "ladda ned")
2. Ladda ned och öppna konfigurations filen.
3. Upprepa de här stegen för den andra platsen. När båda konfigurationsfilerna är öppna kan du fortsätta till nästa avsnitt.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. skapa lokala nätverksgateway

I det här avsnittet skapar du två Azure VPN Gateway lokala Nätverksgatewayen. Konfigurationsfilerna från föregående steg innehåller gatewayens konfigurations inställningar. Använd de här inställningarna för att skapa och konfigurera Azure VPN Gateway lokala Nätverksgatewayen.

1. Skapa den lokala Nätverksgatewayen med de här inställningarna. Information om hur du skapar en VPN Gateway lokal nätverksgateway finns i VPN Gateway artikeln [skapa en lokal](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway)nätverksgateway.

   * **IP-adress** – Använd Instance0 IP-adressen som visas för *gatewayconfiguration* från konfigurations filen.
   * **BGP** – om anslutningen är över BGP väljer du **Konfigurera BGP-inställningar** och anger ASN-65515. Ange IP-adressen för BGP-peer. Använd "Instance0 BgpPeeringAddresses" för *gatewayconfiguration* från konfigurations filen.
   * **Prenumeration, resurs grupp och plats** är samma som för den virtuella WAN-hubben.
2. Granska och skapa den lokala Nätverksgatewayen. Den lokala Nätverksgatewayen bör se ut ungefär som i det här exemplet.

   ![Skärm bild som visar sidan "konfiguration" med en IP-adress markerad och "Konfigurera BGP-inställningar" vald.](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Upprepa de här stegen för att skapa en annan lokal nätverksgateway, men den här gången använder du värdena ' instance1 ' i stället för ' Instance0 '-värden från konfigurations filen.

   ![Ladda ned konfigurations fil](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. skapa anslutningar

I det här avsnittet skapar du en anslutning mellan VPN Gateway lokala nätverksgateway och virtuell nätverksgateway. Anvisningar för hur du skapar en VPN Gateway-anslutning finns i [Konfigurera en anslutning](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection).

1. I portalen navigerar du till din virtuella nätverksgateway och klickar på **anslutningar**. Klicka på **+Lägg till** överst på sidan Anslutningar och öppna sidan **Lägg till anslutning**.
2. På sidan **Lägg till anslutning** konfigurerar du följande värden för din anslutning:

   * **Namn:** Namnge din anslutning.
   * **Anslutnings typ:** Välj **plats-till-plats (IPSec)**
   * **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
   * **Lokal nätverksgateway:** Den här anslutningen ansluter den virtuella Nätverksgatewayen till den lokala Nätverksgatewayen. Välj en av de lokala Nätverksgatewayen som du skapade tidigare.
   * **Delad nyckel:** Ange en delad nyckel.
   * **IKE-protokoll:** Välj IKE-protokollet.
3. Klicka på **OK** för att skapa din anslutning.
4. Anslutningen visas på sidan **Anslutningar** för den virtuella nätverksgatewayen.

   ![Anslutning](./media/connect-virtual-network-gateway-vwan/connect.png "anslutning")
5. Upprepa föregående steg för att skapa en andra anslutning. För den andra anslutningen väljer du den andra lokala Nätverksgatewayen som du skapade.
6. Om anslutningarna är över BGP, när du har skapat anslutningarna, navigerar du till en anslutning och väljer **konfiguration**. På sidan **konfiguration** för **BGP** väljer du **aktive rad**. Klicka sedan på **Spara**. Upprepa för den andra anslutningen.

## <a name="6-test-connections"></a><a name="test"></a>6. testa anslutningar

Du kan testa anslutningen genom att skapa två virtuella datorer, en på sidan av VPN Gateway virtuell nätverksgateway och en i ett virtuellt nätverk för det virtuella WAN-nätverket och sedan pinga de två virtuella datorerna.

1. Skapa en virtuell dator i det virtuella nätverket (TEST1-VNet) för Azure VPN Gateway (TEST1-VNG). Skapa inte den virtuella datorn i GatewaySubnet.
2. Skapa ett annat virtuellt nätverk för att ansluta till det virtuella WAN-nätverket. Skapa en virtuell dator i ett undernät för det här virtuella nätverket. Det här virtuella nätverket kan inte innehålla några virtuella nätverks-gatewayer. Du kan snabbt skapa ett virtuellt nätverk med hjälp av PowerShell-stegen i artikeln [plats-till-plats-anslutning](virtual-wan-site-to-site-portal.md#vnet) . Se till att ändra värdena innan du kör cmdletarna.
3. Anslut VNet till den virtuella WAN-hubben. På sidan för ditt virtuella WAN väljer du **virtuella nätverks anslutningar** och sedan **+ Lägg till anslutning**. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** för att skapa anslutningen till det virtuella nätverket.
5. Anslutningen har nu angetts mellan de virtuella datorerna. Du bör kunna pinga en virtuell dator från den andra, såvida det inte finns några brand väggar eller andra principer som blockerar kommunikationen.

## <a name="next-steps"></a>Nästa steg

Steg för att konfigurera en anpassad IPsec-princip finns i [Konfigurera en anpassad IPsec-princip för virtuellt WAN](virtual-wan-custom-ipsec-portal.md).
Mer information om virtuellt WAN finns i [om Azure Virtual WAN](virtual-wan-about.md) och [vanliga frågor och svar om Azure Virtual WAN](virtual-wan-faq.md).