---
title: 'Självstudie: Azure-ExpressRoute – lägga till en gateway till ett VNet (Azure Portal)'
description: Den här självstudien vägleder dig genom att lägga till en virtuell nätverksgateway i ett VNet för ExpressRoute med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: e70b41dfba03b7439313350746724c6f8e90bc34
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564780"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Självstudie: Konfigurera en virtuell nätverksgateway för ExpressRoute med hjälp av Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisk – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Den här självstudien vägleder dig genom stegen för att lägga till en virtuell nätverksgateway för ett redan befintligt VNet. Den här artikeln vägleder dig genom stegen för att lägga till, ändra storlek på och ta bort en virtuell nätverks-Gateway (VNet) för ett befintligt VNet. Stegen för den här konfigurationen är specifika för virtuella nätverk som skapats med hjälp av distributions modellen Resource Manager som kommer att användas i en ExpressRoute-konfiguration. Mer information om virtuella nätverksgateway och gateway-konfigurationsinställningar för ExpressRoute finns i [om virtuella nätverksgateway för ExpressRoute](expressroute-about-virtual-network-gateways.md). 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa ett Gateway-undernät.
> - Skapa Virtual Network Gateway.

## <a name="prerequisites"></a>Förutsättningar

Stegen för den här aktiviteten använder ett VNet baserat på värdena i följande konfigurations referens lista. Vi använder den här listan i våra exempel steg. Du kan kopiera listan som ska användas som referens och ersätta värdena med dina egna.

**Konfigurations referens lista**

* Virtual Network namn = "TestVNet"
* Virtual Network adress utrymme = 192.168.0.0/16
* Under näts namn = "FrontEnd" 
    * Adress utrymme för undernät = "192.168.1.0/24"
* Resurs grupp = "TestRG"
* Plats = "östra USA"
* Gateway-undernätets namn: "GatewaySubnet" du måste alltid namnge ett Gateway-undernät *GatewaySubnet*.
    * Gateway-undernät adress utrymme = "192.168.200.0/26"
* Gateway-namn = "ERGW"
* Gateway offentlig IP-namn = "MyERGWVIP"
* Gateway type = "ExpressRoute" den här typen krävs för en ExpressRoute-konfiguration.

Du kan visa en [video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) om de här stegen innan du påbörjar konfigurationen.

> [!IMPORTANT]
> IPv6-stöd för privat peering är för närvarande en **offentlig för hands version**. Om du vill ansluta ditt virtuella nätverk till en ExpressRoute-krets med IPv6-baserad privat peering konfigurerad kontrollerar du att ditt virtuella nätverk är dubbel stack och följer rikt linjerna för [IPv6 för Azure VNet](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="create-the-gateway-subnet"></a>Skapa gateway-undernätet

1. I [portalen](https://portal.azure.com)navigerar du till det virtuella Resource Manager-nätverket för vilket du vill skapa en virtuell nätverksgateway.
1. I avsnittet **Inställningar** i ditt VNet väljer du **undernät** för att expandera under näts inställningarna.
1. I **under näts** inställningarna väljer du **+ Gateway-undernät** för att lägga till ett Gateway-undernät. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Lägg till gateway-undernätet":::

1. **Namnet** på undernätet fylls automatiskt i med värdet GatewaySubnet. Det här värdet krävs för att Azure ska kunna identifiera undernätet som gateway-undernätet. Justera värdena för automatiskt ifyllt **adress intervall** så att de överensstämmer med dina konfigurations krav. Vi rekommenderar att du skapar ett Gateway-undernät med en/27 eller större (/26,/25 osv.).

    Om du använder ett virtuellt nätverk med dubbla stackar och planerar att använda IPv6-baserad privat peering över ExpressRoute, klickar du på **Lägg till ip6-adress utrymme** och värden för **IPv6** -adressintervall.

    Välj **OK** för att spara värdena och skapa Gateway-undernätet.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Lägga till ett undernät":::

## <a name="create-the-virtual-network-gateway"></a>Skapa den virtuella nätverksgatewayen

1. På den vänstra sidan i portalen väljer du **skapa en resurs** och skriver "Virtual Network Gateway" i Sök. Leta upp **virtuell nätverksgateway** i Sök returen och välj posten. På sidan **virtuell nätverksgateway** väljer du **skapa**.
1. På sidan **Skapa virtuell nätverksgateway** anger eller väljer du följande inställningar:

    | Inställning | Värde |
    | --------| ----- |
    | Prenumeration | Kontrol lera att rätt prenumeration har valts. |
    | Resursgrupp | Resurs gruppen väljs automatiskt när du har valt det virtuella nätverket. | 
    | Name | Namnge din gateway. Detta är inte samma sak som att namnge ett Gateway-undernät. Det är namnet på det Gateway-objekt som du skapar.|
    | Region | Ändra fältet **region** så att det pekar på den plats där det virtuella nätverket finns. Om platsen inte pekar på den region där det virtuella nätverket finns visas inte det virtuella nätverket i list rutan "Välj ett virtuellt nätverk". |
    | Gateway-typ | Välj **ExpressRoute**|
    | SKU | Välj Gateway-SKU: n i list rutan. |
    | Virtuellt nätverk | Välj *TestVNet*. |
    | Offentlig IP-adress | Välj **Skapa ny**.|
    | Namn på offentlig IP-adress | Ange ett namn för den offentliga IP-adressen. |

    > [!IMPORTANT]
    > Om du planerar att använda IPv6-baserad privat peering över ExpressRoute, se till att välja en AZ SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ) för **SKU**.
    > 
    > 

1. Välj **Granska + skapa** och sedan **skapa** för att börja skapa gatewayen. Inställningarna verifieras och gatewayen distribueras. Det kan ta upp till 45 minuter att skapa en virtuell nätverksgateway.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Sid fält för att skapa virtuell nätverksgateway":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver ExpressRoute-gatewayen letar du rätt på gatewayen i den virtuella nätverks resurs gruppen och väljer **ta bort**. Se till att gatewayen inte har några anslutningar till en krets.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Ta bort virtuell nätverksgateway":::

## <a name="next-steps"></a>Nästa steg
När du har skapat VNet-gatewayen kan du länka ditt VNet till en ExpressRoute-krets. 

> [!div class="nextstepaction"]
> [Länka en Virtual Network till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)