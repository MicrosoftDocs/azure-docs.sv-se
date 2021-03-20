---
title: 'Självstudie – ansluta lokalt nätverk till ett virtuellt nätverk: Azure Portal'
description: Skapa en plats-till-plats-VPN Gateway IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via det offentliga Internet som använder portalen.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945210"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Självstudie: skapa en plats-till-plats-anslutning i Azure Portal

Azure VPN-gatewayer ger anslutningar mellan olika platser, t.ex. mellan kundens lokaler och Azure. Den här självstudien visar hur du använder Azure Portal för att skapa en plats-till-plats-VPN gateway-anslutning från ditt lokala nätverk till det virtuella nätverket. Du kan också skapa den här konfigurationen med [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) eller [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagram över plats-till-plats-anslutning med VPN Gateway":::

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en VPN-gateway
> * Skapa en lokal nätverksgateway
> * Skapa en VPN-anslutning
> * Verifiera anslutningen
> * Ansluta till en virtuell dator

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. Om du inte har ett kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk (VNet) med följande värden:

* **Resurs grupp:** TestRG1
* **Namn:** VNet1
* **Region:** (USA) USA, östra
* **IPv4-adress utrymme:** 10.1.0.0/16
* **Under näts namn:** Delen
* **Adress utrymme för undernät:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Skapa en VPN-gateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

### <a name="about-the-gateway-subnet"></a>Om gateway-undernätet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Skapa gatewayen

Skapa en VPN-gateway med följande värden:

* **Namn:** VNet1GW
* **Region:** USA, östra
* **Gateway-typ:** Konfigurera
* **VPN-typ:** Route-baserad
* **SKU:** VpnGw1
* **Generation:** Generation1
* **Virtuellt nätverk:** VNet1
* **Adress intervall för Gateway-under nätet:** 10.1.255.0/27
* **Offentlig IP-adress:** Skapa ny
* **Namn på offentlig IP-adress:** VNet1GWpip
* **Aktivera aktivt-aktivt läge:** Inaktiverats
* **Konfigurera BGP:** Inaktiverats

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Visa den offentliga IP-adressen

Du kan visa den offentliga Gateway-IP-adressen på **översikts** sidan för din gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Översiktssidan":::

Om du vill se mer information om objektet offentlig IP-adress klickar du på länken namn/IP-adress bredvid **offentlig IP-adress**.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Skapa en lokal nätverksgateway

Den lokala Nätverksgatewayen är ett objekt som representerar din lokala plats (platsen) för routning. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Om ditt lokala nätverk ändras eller om du behöver ändra den offentliga IP-adressen för VPN-enheten, kan du enkelt uppdatera värden senare.

Skapa en lokal nätverksgateway med följande värden:

* **Namn:** Site1
* **Resursgrupp:** TestRG1
* **Plats:** USA, östra

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande värden:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Om du vill hitta den offentliga IP-adressen för din VPN-gateway med hjälp av Azure Portal, navigerar du till **virtuella nätverksgateway** och väljer sedan namnet på din gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Skapa en VPN-anslutning

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet.

Skapa en anslutning med följande värden:

* **Namn på lokal nätverksgateway:** Site1
* **Anslutnings namn:** VNet1toSite1
* **Delad nyckel:** I det här exemplet använder vi abc123. Men du kan använda det som är kompatibelt med din VPN-maskinvara. Huvudsaken är att värdena matchar på båda sidorna av anslutningen.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Verifiera VPN-anslutningen

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Valfria steg

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Lägg till ytterligare anslutningar till gatewayen

Du kan lägga till ytterligare anslutningar, förutsatt att ingen av adressutrymmena överlappar mellan anslutningar.

1. Om du vill lägga till ytterligare en anslutning navigerar du till VPN-gatewayen och väljer sedan **anslutningar** för att öppna sidan anslutningar.
1. Välj **+ Lägg** till för att lägga till din anslutning. Justera anslutningstyp för att återspegla antingen VNet-to-VNet (om du ansluter till en annan VNet-gateway) eller Plats-till-plats.
1. Om du ansluter med Plats-till-plats och inte redan har skapat en lokal nätverksgateway för webbplatsen du vill ansluta till kan du skapa en ny.
1. Ange den delade nyckel som du vill använda och välj sedan **OK** för att skapa anslutningen.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ändra storlek på en gateway-SKU

Det finns vissa regler för storleks ändring kontra ändring av en gateway-SKU. I det här avsnittet ska du ändra storlek på SKU: n. Mer information finns i [Gateway-inställningar – ändra storlek och ändra storlek på SKU: er](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Återställa en gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Ytterligare konfigurations överväganden

S2S-konfigurationer kan anpassas på flera olika sätt. Mer information finns i följande artiklar:

* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om Tvingad tunnel trafik finns i [om Tvingad tunnel trafik](vpn-gateway-forced-tunneling-rm.md).
* Mer information om aktiv-aktiv-anslutningar med hög tillgänglighet finns i [Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md).
* Mer information om hur du begränsar nätverkstrafiken till resurser i ett virtuellt nätverk finns i [Nätverkssäkerhet](../virtual-network/network-security-groups-overview.md).
* Mer information om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser finns i [Trafikdirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda programmet eller gå till nästa självstudie, tar du bort dessa resurser med följande steg:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen och välj den från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange resurs gruppen för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat en S2S-anslutning kan du lägga till en P2S-anslutning till samma Gateway.

> [!div class="nextstepaction"]
> [Punkt-till-plats-VPN-anslutningar](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
