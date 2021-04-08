---
title: Så här konfigurerar du routning för en virtuell hubb
titleSuffix: Azure Virtual WAN
description: Den här artikeln beskriver hur du konfigurerar routning av virtuell hubb
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313696"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Så här konfigurerar du routning för en virtuell hubb

En virtuell hubb kan innehålla flera gatewayer som en plats-till-plats-VPN-gateway, ExpressRoute-Gateway, punkt-till-plats-gateway och Azure-brandvägg. Routningsfunktioner i den virtuella hubben tillhandahålls av en router som hanterar all routning, inklusive transit routning, mellan de gatewayer som använder Border Gateway Protocol (BGP). Den här routern tillhandahåller även överförings anslutning mellan virtuella nätverk som ansluter till en virtuell hubb och har stöd för upp till ett sammanställt data flöde på 50 Gbit/s. Dessa routningsfunktioner gäller för virtuella standard WAN-kunder.

Mer information finns i [om routning av virtuell hubb](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Skapa en routningstabell

1. I Azure Portal navigerar du till den virtuella hubben.
2. Under **anslutning** väljer du **routning**. På sidan routning ser du **standard** -och **inga** vägvals tabeller.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Sidan routning":::
3. Välj **+ skapa routningstabell** för att öppna sidan **skapa** routningstabell.
4. Fyll i följande fält på fliken **grundläggande** på sidan för att skapa routningstabellen.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Fliken Grundläggande":::

   * **Namn**
   * **Vägar**
   * **Vägnamn**
   * **Måltyp**
   * **Måltema**: du kan aggregera prefix. Exempel: VNet 1:10.1.0.0/24 och VNet 2:10.1.1.0/24 kan aggregeras som 10.1.0.0/16. **Förgrenings** vägar gäller för alla anslutna VPN-platser, ExpressRoute-kretsar och VPN-anslutningar för användare.
   * **Nästa hopp**: en lista över virtuella nätverks anslutningar eller Azure-brandvägg.

     Om du väljer en virtuell nätverks anslutning visas **Konfigurera statiska vägar**. Detta är en valfri konfigurations inställning. Mer information finns i [Konfigurera statiska vägar](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Nästa hopp":::

5. Välj fliken **Etiketter** för att konfigurera etikett namn. Etiketter ger en mekanism för att gruppera väg tabeller logiskt.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Konfigurera etikett namn":::

6. Välj fliken **associationer** för att associera anslutningarna till routningstabellen.
Du kan se **grenar**, **virtuella nätverk** och de **aktuella inställningarna** för anslutningarna.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Kopplings anslutningar till routningstabellen":::

7. Välj fliken **spridningar** för att sprida vägar från anslutningar till routningstabellen.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Sprid vägar":::

8. Skapa routningstabellen genom att välja **skapa** .

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Redigera en routningstabell

Leta upp den virtuella hubbens routningstabell i Azure Portal. Välj routningstabellen för att redigera all information.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Ta bort en routningstabell

Leta upp den virtuella hubbens routningstabell i Azure Portal. Du kan inte ta bort en standard-eller none route-tabell. Du kan dock ta bort alla anpassade väg tabeller. Klicka på **"..."** och välj sedan **ta bort**.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>Så här visar du effektiva vägar

Leta upp den virtuella hubbens routningstabell i Azure Portal. Klicka på **"..."** och välj **effektiva vägar** för att visa de vägar som har lärts av den valda routningstabellen. Spridda vägar från anslutningen till routningstabellen fylls i automatiskt i **effektiva vägar** i routningstabellen. Mer information finns i [om effektiva vägar](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Visa effektiva vägar" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>Konfigurera konfigurering av routning för en virtuell nätverks anslutning

1. I Azure Portal navigerar du till ditt virtuella WAN och väljer **Virtual Network anslutningar** under **anslutning**.
1. Välj **+ Lägg till anslutning**.
1. Välj det virtuella nätverket i list rutan.
1. Konfigurera Dirigerings konfigurationen så att den kopplas till en routningstabell. För **koppla** routningstabell väljer du routningstabellen i list rutan.
1. Konfigurera konfigurering av routning för att sprida till en eller flera vägvals tabeller. För **spridning till** routningstabell väljer du i list rutan.
1. För **statiska vägar** konfigurerar du statiska vägar för virtuell nätverks installation (om tillämpligt). Virtual WAN stöder en enda nästa hopp-IP för en statisk väg i en virtuell nätverks anslutning. Om du till exempel har en separat virtuell installation för ingångs-och utgående trafikflöden, är det bäst att ha de virtuella enheterna i separata virtuella nätverk och koppla virtuella nätverk till den virtuella hubben.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Konfigurera konfiguration av Routning" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Nästa steg

Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
