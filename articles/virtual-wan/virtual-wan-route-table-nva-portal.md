---
title: 'Virtuellt WAN: Skapa virtuell hubb väg tabell till NVA: Azure Portal'
description: Väg tabell för virtuell WAN-hubb som styr trafik till en virtuell nätverks enhet med hjälp av portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 409858b2a9005592d895f6044d028221769144cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058518"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Skapa en virtuell WAN Hub-routningstabell för NVA: Azure Portal

Den här artikeln visar hur du styr trafik från en gren (lokal plats) som är ansluten till den virtuella WAN-hubben till ett eker-virtuellt nätverk (VNet) via en virtuell nätverks installation (NVA).

![Virtual WAN-diagram](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har uppfyllt följande kriterier:

*  Du har en virtuell nätverks installation (NVA). En virtuell nätverks installation är ett tredjepartsprogram som du väljer som vanligt vis tillhandahålls från Azure Marketplace i ett virtuellt nätverk.

    * En privat IP-adress måste tilldelas till NVA-nätverks gränssnittet.

    * NVA har inte distribuerats i den virtuella hubben. Den måste distribueras i ett separat virtuellt nätverk.

    *  Det virtuella NVA-nätverket kan ha ett eller flera virtuella nätverk som är anslutna till det. I den här artikeln hänvisar vi till det virtuella NVA-nätverket som ett ' indirekt eker VNet '. Dessa virtuella nätverk kan anslutas till NVA VNet med hjälp av VNet-peering. Länkarna för VNet-peering visas med svarta pilar i bilden ovan mellan VNet 1, VNet 2 och NVA VNet.
*  Du har skapat två virtuella nätverk. De kommer att användas som ekrar virtuella nätverk.

    * Adress utrymmen för VNet-ekrar: VNet1:10.0.2.0/24 och VNet2:10.0.3.0/24. Om du behöver information om hur du skapar ett virtuellt nätverk kan du läsa [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md).

    * Se till att det inte finns några virtuella nätverksgateway i någon av virtuella nätverk.

    * Virtuella nätverk kräver inte ett Gateway-undernät.

## <a name="1-sign-in"></a><a name="signin"></a>1. Logga in

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. skapa ett virtuellt WAN

Skapa ett virtuellt WAN-nätverk. Använd följande exempel värden:

* **Virtuellt WAN-namn:** myVirtualWAN
* **Resurs grupp:** testRG
* **Plats:** Västra USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. skapa en hubb

Skapa hubben. Använd följande exempel värden:

* **Plats:** Västra USA
* **Namn:** westushub
* **Hubb privat adress utrymme:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. skapa och Använd en hubb väg tabell

Uppdatera hubben med en hubb väg tabell. Använd följande exempel värden:

* **Eker VNet-adress utrymmen:** (VNet1 och VNet2) 10.0.2.0/24 och 10.0.3.0/24
* **DMZ NVA Network Interface Private IP Address:** 10.0.4.5

1. Navigera till ditt virtuella WAN-nätverk.
2. Klicka på den hubb som du vill skapa en routningstabell för.
3. Klicka på **...** och klicka sedan på **Redigera virtuell hubb**.
4. På sidan **Redigera virtuellt nav** bläddrar du nedåt och markerar kryss rutan **Använd tabell för routning**.
5. Lägg till adress utrymmena i kolumnen **om målets prefix är** . I kolumnen **Skicka till nästa hopp** lägger du till DMZ NVA Network Interface Private IP Address.

   > [!NOTE]
   > DMZ NVA-nätverket är tillämpligt på den lokala hubben.
   
6. Klicka på **Bekräfta** om du vill uppdatera Hub-resursen med väg tabells inställningarna.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. skapa VNet-anslutningarna

Skapa en virtuell nätverks anslutning från varje indirekt eker VNet (VNet1 och VNet2) till hubben. De här virtuella nätverks anslutningarna illustreras med de blå pilarna i bilden ovan. Skapa sedan en VNet-anslutning från NVA VNet till hubben (svart pil i bilden).

 I det här steget kan du använda följande värden:

| Virtuellt nätverksnamn| Anslutningsnamn|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Upprepa följande steg för varje virtuellt nätverk som du vill ansluta.

1. På sidan för det virtuella WAN-nätverket klickar du på **Virtuella nätverksanslutningar**.
2. På sidan för virtuell nätverksanslutning klickar du på **+Lägg till anslutning**.
3. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** för att skapa anslutningen.

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
