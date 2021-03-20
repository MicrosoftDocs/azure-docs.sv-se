---
title: 'Självstudie – Skapa och hantera en VPN Gateway: Azure Portal'
description: Följ den här självstudien för att lära dig att skapa, distribuera och hantera en Azure-VPN Gateway med hjälp av portalen
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 820482a268af038737557c517ccda086cd65d943
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880566"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Självstudie: skapa och hantera en VPN-gateway med Azure Portal

Azure VPN-gatewayer ger anslutningar mellan olika platser, t.ex. mellan kundens lokaler och Azure. Den här självstudien beskriver grundläggande distributionsobjekt i Azure VPN-gatewayen, till exempel att skapa och hantera en VPN-gateway. Du kan också skapa en gateway med [Azure CLI](create-routebased-vpn-gateway-cli.md) eller [Azure PowerShell](create-routebased-vpn-gateway-powershell.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en VPN-gateway
> * Se den offentliga IP-adressen för gatewayen
> * Ändra storlek på en VPN-gateway (SKU för storleks ändring)
> * Återställ en VPN gateway

Följande diagram visar det virtuella nätverket och den VPN-gateway som skapats som en del av den här självstudien.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagram över VNet-och VPN-gateway":::

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. Om du inte har ett kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Skapa ett virtuellt nätverk

Skapa ett VNet med följande värden:

* **Resurs grupp:** TestRG1
* **Namn:** VNet1
* **Region:** (USA) USA, östra
* **IPv4-adress utrymme:** 10.1.0.0/16
* **Under näts namn:** Delen
* **Adress utrymme för undernät:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Skapa en VPN-gateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

Skapa en virtuell nätverksgateway med följande värden:

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

## <a name="view-the-public-ip-address"></a><a name="view"></a>Visa den offentliga IP-adressen

Du kan visa den offentliga Gateway-IP-adressen på **översikts** sidan för din gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Översiktssidan":::

Om du vill se mer information om objektet offentlig IP-adress klickar du på länken namn/IP-adress bredvid **offentlig IP-adress**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ändra storlek på en gateway-SKU

Det finns vissa regler för storleks ändring kontra ändring av en gateway-SKU. I det här avsnittet ska du ändra storlek på SKU: n. Mer information finns i [Gateway-inställningar – ändra storlek och ändra storlek på SKU: er](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Återställa en gateway

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda programmet eller gå till nästa självstudie, tar du bort dessa resurser med följande steg:

1. Ange namnet på din resurs grupp i rutan **Sök** högst upp i portalen och välj den från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange resurs gruppen för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

När du har en VPN-gateway kan du konfigurera anslutningar. Artiklarna nedan hjälper dig att skapa några av de vanligaste konfigurationerna:

> [!div class="nextstepaction"]
> [Plats-till-plats-VPN-anslutningar](./tutorial-site-to-site-portal.md)

> [!div class="nextstepaction"]
> [Punkt-till-plats-VPN-anslutningar](vpn-gateway-howto-point-to-site-resource-manager-portal.md)