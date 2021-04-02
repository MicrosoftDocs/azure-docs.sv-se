---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6cb30b96f9c2094e6d690b565edf487d6508d520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487124"
---
1. Från [Azure Portal](https://portal.azure.com), i **Sök efter resurser, tjänster och dokument (G +/)** typ **virtuell nätverksgateway**. Leta upp en **virtuell nätverksgateway** i Sök resultaten och markera den.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/search.png" alt-text="Sök fält" lightbox="./media/vpn-gateway-add-gw-rm-portal-empty/search-expand.png":::

1. På sidan **virtuell nätverksgateway** väljer du **+ Lägg till**. Då öppnas sidan **Skapa virtuell nätverksgateway**.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/add.png" alt-text="Sidan för virtuella nätverksgateway":::
1. På fliken **grundläggande** fyller du i värdena för din virtuella nätverksgateway.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway.png" alt-text="Gateway-fält":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-empty/gateway-vnet.png" alt-text="Ytterligare Gateway-fält":::

   **Projektinformation**

   * **Prenumeration**: Välj den prenumeration som du vill använda i list rutan.
   * **Resurs grupp**: den här inställningen fylls i när du väljer ditt virtuella nätverk på den här sidan.

   **Instansinformation**

   * **Namn**: namnge din gateway. Att namnge din gateway är inte samma sak som att namnge ett Gateway-undernät. Det här är namnet på det gatewayobjekt som du skapar.
   * **Region**: Välj den region där du vill skapa den här resursen. Regionen för gatewayen måste vara samma som det virtuella nätverket.
   * **Gatewaytyp**: välj **VPN**. En VPN-gateway använder **VPN** som virtuell nätverksgateway.
   * **VPN-typ**: Välj den VPN-typ som har angetts för din konfiguration. De flesta konfigurationer kräver en ruttbaserad VPN-typ.
   * **SKU**: Välj en gateway-SKU från listrutan. SKU:erna som visas i listrutan beror på vilken VPN-typ du har valt. Se [Gateway-SKU:er](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) för information om gateway-SKU:er.
   * **Generation**: information om VPN gateway generation finns i [Gateway-SKU: er](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Virtuellt nätverk**: Välj det virtuella nätverk som du vill lägga till den här gatewayen till i list rutan.
   * **Adress intervall för Gateway-under nätet**: det här fältet visas bara om ditt VNet inte har något Gateway-undernät. Om möjligt, gör du intervallet/27 eller större (/26,/25 osv.). Vi rekommenderar inte att du skapar ett intervall som är mindre än/28. Om du redan har ett Gateway-undernät kan du Visa GatewaySubnet information genom att gå till det virtuella nätverket. Visa intervallet genom att klicka på **undernät** . Om du vill ändra intervallet kan du ta bort och återskapa GatewaySubnet.

   **Offentlig IP-adress**

   Den här inställningen anger det offentliga IP-adressprefixet som associeras med VPN-gatewayen. Den offentliga IP-adressen tilldelas dynamiskt till detta objekt när en VPN-gateway skapas. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

     * **Offentlig IP-adress**: lämna **Skapa ny** är markerat.
     * **Namn på offentlig IP-adress**: Skriv ett namn för din offentliga IP-tjänstinstans i text rutan.
     * **Tilldelning**: VPN-gateway stöder endast dynamisk.
     * **Aktivera aktivt-aktivt läge**: Välj **aktivera aktivt-aktivt läge** om du skapar en aktiv-aktiv gateway-konfiguration. Annars lämnar du den här inställningen **inaktive rad**.
     * Lämna **Konfigurera BGP** som **inaktiverat**, om inte konfigurationen kräver den här inställningen. Om du behöver inställningen så är standard-ASN 65515, men detta kan ändras.
1. Välj **Granska + skapa** för att köra verifieringen.
1. När verifieringen har godkänts väljer du **skapa** för att distribuera VPN-gatewayen. Det kan ta upp till 45 minuter för en gateway att skapa och distribuera fullständigt. Du kan se distributions status på översikts sidan för din gateway.

När gatewayen är skapad, kan du se IP-adressen som har tilldelats den genom att se på det virtuella nätverket i portalen. Gatewayen visas som en ansluten enhet.
