---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5630eba4f1be9aead6ff1a89bc036bcc548c3646
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99540986"
---
1. Leta upp det virtuella WAN-nätverket som du har skapat. På sidan virtuellt WAN, under avsnittet **anslutning** , väljer du **hubbar**.
2. På sidan hubbar väljer du **+ ny hubb** för att öppna sidan **Skapa virtuell hubb** .

    ![Skärm bild som visar fönstret Skapa virtuellt nav med fliken grundläggande valt.](./media/virtual-wan-tutorial-hub-include/basics.png "Grunder")
3. Fyll i följande fält på fliken **grundläggande grunder** på sidan **Skapa virtuell hubb** :

    **Projektinformation**

   * Region (tidigare kallad plats)
   * Name
   * NAV, privat adress utrymme. Det minsta adress utrymmet är/24 för att skapa en hubb, vilket innebär att allt mellan/25 och/32 genererar ett fel när det skapas. Azure Virtual WAN, som är en hanterad tjänst av Microsoft, skapar lämpliga undernät i den virtuella hubben för olika gatewayer/tjänster (t. ex. VPN-gatewayer, ExpressRoute-gatewayer, användares VPN/punkt-till-plats-gatewayer, brand vägg, routning osv.). Användaren behöver inte uttryckligen planera för under nätets adress utrymme för tjänsterna i den virtuella hubben eftersom Microsoft gör detta som en del av tjänsten.
4. Välj **Nästa: plats-till-plats**.

    ![Skärm bild som visar fönstret Skapa virtuellt nav med plats-till-plats vald.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Plats-till-plats")

5. På fliken **plats-till-plats** fyller du i följande fält:

   * Välj **Ja** om du vill skapa en plats-till-plats-VPN.
   * Det går inte att redigera fältet AS Number i den virtuella hubben just nu.
   * Välj värdet för **Gateway Scale units** i list rutan. Med skalnings enheten kan du välja det sammanställda data flödet för VPN-gatewayen som skapas i den virtuella hubben för att ansluta platser till. Om du väljer 1 skalnings enhet = 500 Mbit/s betyder det att två instanser för redundans skapas, var och en har ett maximalt data flöde på 500 Mbit/s. Om du till exempel har fem grenar, var och en med 10 Mbit/s på grenen, behöver du en agg regering på 50 Mbit/s vid Head-slutet. Du bör planera den sammanställda kapaciteten för Azure VPN-gatewayen när du har bedömt kapaciteten som krävs för att stödja antalet grenar till hubben.
6. Välj **Granska + skapa** för att validera.
7. Välj **skapa** för att skapa hubben. Efter 30 minuter, **Uppdatera** för att Visa hubben på sidan **hubbar** . Välj **gå till resurs** för att navigera till resursen.
