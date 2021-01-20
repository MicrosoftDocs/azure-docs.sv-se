---
title: Skapa en VPN-anslutning mellan Azure och AWS med hanterade lösningar
description: Så här skapar du en VPN-anslutning mellan Azure och AWS med hanterade lösningar, i stället för virtuella datorer eller enheter.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: cherylmc
ms.openlocfilehash: c1bc263ca67a7d05dbb0d40bb07ba1ae43c2db5c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605646"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Skapa en VPN-anslutning mellan Azure och AWS med hjälp av hanterade lösningar

Du kan upprätta en anslutning mellan Azure och AWS med hjälp av hanterade lösningar. Tidigare var du tvungen att använda en installation eller en virtuell dator som fungerar som en svarare. Nu kan du ansluta den virtuella privata gatewayen AWS till Azure VPN Gateway direkt utan att behöva oroa dig för att hantera IaaS-resurser, till exempel virtuella datorer. Den här artikeln hjälper dig att skapa en VPN-anslutning mellan Azure och AWS genom att bara använda hanterade lösningar.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Arkitektur diagram":::

## <a name="configure-azure"></a>Konfigurera Azure

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

Konfigurera ett virtuellt nätverk. Anvisningar finns i snabb starten för [Virtual Network](../virtual-network/quick-create-portal.md).

Följande exempel värden används i den här artikeln:

* **Resurs grupp:** RG – Azure-AWS
* **Region:** USA, östra
* **Virtuellt nätverks namn:** VNet – Azure
* **IPv4-adress utrymme:** 172.10.0.0/16
* **Under näts namn:** undernät – 01
* **Adress intervall för under nätet:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Skapa en VPN-gateway

Skapa en VPN-gateway för det virtuella nätverket. Mer information finns i [Självstudier: skapa och hantera en VPN-gateway](tutorial-create-gateway-portal.md).

Följande exempel värden och inställningar används i den här artikeln:

* **Gateway-typ:** Konfigurera
* **VPN-typ:** Route-baserad
* **SKU:** VpnGw1
* **Generation:** Generation 1
* **Virtuellt nätverk:** Måste vara det virtuella nätverk som du vill skapa gatewayen för.
* **Adress intervall för Gateway-under nätet:** 172.10.0.0/27
* **Offentlig IP-adress:** Skapa ny
* **Offentligt IP-adress namn:** pip-VPN-Azure-AWS
* **Aktivera aktivt-aktivt läge:** Tillåt
* **Konfigurera BGP:** Tillåt

Exempel:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Sammanfattning av virtuell nätverksgateway":::

## <a name="configure-aws"></a>Konfigurera AWS

1. Skapa det virtuella privata molnet (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Skapa VPC-information":::

1. Skapa ett undernät inuti VPC (virtuellt nätverk).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Skapa under nätet":::

1. Skapa en kundgateway som pekar på den offentliga IP-adressen för Azure VPN Gateway. **Kund-gatewayen** är en AWS-resurs som innehåller information för AWS om kundens gateway-enhet, som i det här fallet är Azure-VPN gateway.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Skapa kund-Gateway":::

1. Skapa den virtuella privata gatewayen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Skapa virtuell privat Gateway":::

1. Anslut den virtuella privata gatewayen till VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Bifoga VPG till VPC":::

1. Välj VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Fäst":::

1. Skapa en VPN-anslutning för plats-till-plats.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Skapa VPN-anslutning":::

1. Ange alternativet för routning till **statiskt** och peka på Azure Subnet-01-prefixet **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Ange en statisk väg":::

1. När du har fyllt alternativen **skapar** du anslutningen.

1. Ladda ned konfigurations filen. Du kan hämta rätt konfiguration genom att ändra leverantör, plattform och program vara till **generisk**, eftersom Azure inte är ett giltigt alternativ.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Hämta konfiguration":::

1. Konfigurations filen innehåller den i förväg delade nyckeln och den offentliga IP-adressen för var och en av de två IPsec-tunnlar som skapats av AWS.

   **Tunnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Konfiguration av tunnel 1":::

   **Tunnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Tunnel 2-konfiguration":::

1. När tunnlarna har skapats visas något som liknar det här exemplet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Information om AWS VPN-anslutning":::

## <a name="create-local-network-gateway"></a>Skapa lokal nätverksgateway

I Azure är den lokala Nätverksgatewayen en Azure-resurs som vanligt vis representerar en lokal plats. Den fylls med information som används för att ansluta till den lokala VPN-enheten. Men i den här konfigurationen skapas och fylls den lokala Nätverksgatewayen i med den AWS virtuella privata gatewayens anslutnings information. Mer information om lokala Azure-nätverksgateway finns i [azure VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#lng).

Skapa en lokal nätverksgateway i Azure. Anvisningar finns i [skapa en lokal nätverksgateway](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Ange följande värden:

* **Namn:** I exemplet använder vi LNG-Azure-AWS.
* **Slut punkt:** IP-adress
* **IP-adress:** Den offentliga IP-adressen från AWS virtuella privata gateway och VPC CIDR-prefixet. Du hittar den offentliga IP-adressen i konfigurations filen som du laddade ned tidigare.

AWS skapar två IPsec-tunnlar för hög tillgänglighet. I följande exempel visas den offentliga IP-adressen från IPsec-tunneln #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Lokal nätverksgateway":::

## <a name="create-vpn-connection"></a>Skapa VPN-anslutning

I det här avsnittet skapar du VPN-anslutningen mellan den virtuella Azure-Nätverksgatewayen och AWS-gatewayen.

1. Skapa Azure-anslutningen. Anvisningar för hur du skapar en anslutning finns i [skapa en VPN-anslutning](tutorial-site-to-site-portal.md#CreateConnection).

   I följande exempel hämtade du den delade nyckeln från konfigurations filen som du laddade ned tidigare. I det här exemplet använder vi värdena för IPsec-tunnel #1 som skapats av AWS och beskrivs i konfigurations filen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure Connection-objekt":::

1. Visa anslutningen. Efter några minuter upprättas anslutningen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Fungerande anslutning":::

1. Kontrol lera att AWS IPsec-tunnel #1 är **igång**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Kontrol lera att AWS-tunneln är igång":::

1. Redigera routningstabellen som är associerad med VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Redigera vägen":::

1. Lägg till vägen i Azure-undernätet. Den här vägen överförs genom VPC-gatewayen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Spara väg konfigurationen":::

## <a name="configure-second-connection"></a>Konfigurera en andra anslutning

I det här avsnittet skapar du en andra anslutning för att säkerställa hög tillgänglighet.

1. Skapa en annan lokal nätverksgateway som pekar på den offentliga IP-adressen för IPsec-tunneln #2 på AWS. Detta är en gateway för vänte läge.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Skapa den lokala nätverksgatewayen":::

1. Skapa den andra VPN-anslutningen från Azure till AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Skapa en lokal nätverksgateway för lokal anslutning":::

1. Visa Azure VPN gateway-anslutningarna.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Status för Azure-anslutning":::

1. Visa AWS-anslutningarna.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Status för AWS-anslutning":::

Anslutningarna upprättas nu.

## <a name="test-connections"></a>Test anslutningar

1. Lägg till en Internet-gateway i VPC på AWS. Internet-gatewayen är en logisk anslutning mellan en Amazon-VPN och Internet. Med den här resursen kan du ansluta till den virtuella test datorn från AWS offentliga IP via Internet. Den här resursen krävs inte för VPN-anslutningen. Vi använder det bara för att testa.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Skapa Internet-gatewayen":::

1. Välj **Anslut till VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Ansluta Internet-gatewayen till VPC":::

1. Välj en VPC och **Koppla Internet-gatewayen**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Bifoga gatewayen":::

1. Skapa en väg för att tillåta anslutningar till **0.0.0.0/0** (Internet) via Internet-gatewayen.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Konfigurera vägen genom gatewayen":::

1. I Azure skapas vägen automatiskt. Du kan kontrol lera vägen från den virtuella Azure-datorn genom att välja **VM-> nätverk > nätverks gränssnittet > effektiva vägar**. Du ser 2 vägar, 1 väg per anslutning.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Kontrol lera de effektiva vägarna":::

1. Från en virtuell Linux-dator på Azure liknar miljön följande exempel.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Översikt över Azure från virtuella Linux-datorer":::

1. Från en virtuell Linux-dator på AWS liknar miljön följande exempel.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Översikt över AWS från virtuella Linux-datorer":::

1. Testa anslutningen från den virtuella Azure-datorn.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Ping-test från Azure":::

1. Testa anslutningen från den virtuella AWS-datorn.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Ping-test från AWS":::

## <a name="next-steps"></a>Nästa steg

Mer information om AWS-stöd för IKEv2 finns i [artikeln om AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).
