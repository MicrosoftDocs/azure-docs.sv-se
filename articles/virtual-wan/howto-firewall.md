---
title: Installera Azure-brandväggen i en virtuell WAN-hubb
titleSuffix: Azure Virtual WAN
description: Steg för att konfigurera Azure-brandväggen i en virtuell WAN-hubb
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983665"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Konfigurera Azure-brandväggen i en virtuell WAN-hubb

En **skyddad hubb** är en virtuell Azure-hubb med Azure-brandvägg. Den här artikeln vägleder dig genom stegen för att konvertera en virtuell WAN-hubb till en säker hubb genom att installera Azure-brandväggen direkt från Azures virtuella WAN-Portal sidor.

## <a name="before-you-begin"></a>Innan du börjar

Stegen i den här artikeln förutsätter att du redan har distribuerat ett virtuellt WAN med ett eller flera hubbar.

Om du vill skapa ett nytt virtuellt WAN-nätverk och en ny hubb använder du stegen i följande artiklar:

* [Skapa ett virtuellt WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Skapa en hubb](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Visa virtuella hubbar

**Översikts** sidan för ditt virtuella WAN-nätverk visar en lista över virtuella hubbar och skyddade nav. Följande bild visar ett virtuellt WAN utan skyddade nav.

[![Skärm bild som visar översikts sidan för ett virtuellt WAN med en lista över virtuella nav.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Konvertera till skyddad hubb

1. På sidan **Översikt** för ditt virtuella WAN väljer du den hubb som du vill konvertera till ett skyddat nav. På sidan Virtual Hub visas två alternativ för att distribuera Azure-brandväggen i den här hubben. Välj något av alternativen.

   [![Skärm bild som visar översikts sidan för ditt virtuella WAN-nätverk där du kan välja antingen konvertera till säker hubb eller Azure-brandvägg.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. När du har valt ett av alternativen visas sidan **konvertera till säkert nav** . Välj en hubb som ska konverteras och välj sedan **Nästa: Azure-brandväggen** längst ned på sidan.

   [![Välj hubb](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. När du har slutfört arbets flödet väljer du **Bekräfta**.

   [![Skärm bild som visar fönstret konvertera till säkert hubb med bekräfta valt.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. När hubben har konverterats till en skyddad hubb kan du Visa den på **översikts** sidan för Virtual WAN.

   [![Visa skyddad hubb](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Visa nav resurser

På sidan Virtual WAN- **Översikt** väljer du den skyddade hubben. På sidan hubb kan du Visa alla virtuella nav resurser, inklusive Azure-brandväggen.

[![Visa nav resurser](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Om du vill visa inställningarna för Azure-brandväggen från den skyddade hubben väljer du **skyddade inställningar för virtuella nav** under **säkerhet**.
[![Visa Hubbs inställningar](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Konfigurera ytterligare inställningar

Om du vill konfigurera ytterligare inställningar för Azure-brandväggen för det virtuella navet väljer du länken till **Azure Firewall Manager**. Information om brand Väggs principer finns i [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![ytterligare inställningar](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Om du vill gå tillbaka till **sidan hubb kan** du gå tillbaka genom att klicka på banan, som du ser i pilen i följande figur.

[![gå tillbaka till översikt](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
