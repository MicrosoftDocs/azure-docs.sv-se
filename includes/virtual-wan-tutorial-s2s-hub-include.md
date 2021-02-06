---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627734"
---
1. Leta upp det virtuella WAN-nätverket som du har skapat. På sidan virtuellt WAN, under avsnittet **anslutning** , väljer du **hubbar**.
2. På sidan **hubbar** väljer du **+ ny hubb** för att öppna sidan **Skapa virtuell hubb** .

    ![Skärm bild som visar fönstret Skapa virtuellt nav med fliken grundläggande valt.](./media/virtual-wan-tutorial-hub-include/basics.png "Grunder")
3. Fyll i följande fält på fliken **grundläggande grunder** på sidan **Skapa virtuell hubb** :

   * **Region** (tidigare kallad plats)
   * **Namn**
   * **Hubb privat adress utrymme** – det minsta adress utrymmet är/24 för att skapa en hubb. Om du använder något i intervallet från/25 till/32 skapas ett fel när det skapas. Du behöver inte uttryckligen planera under nätets adress utrymme för tjänsterna i den virtuella hubben. Eftersom Azure Virtual WAN är en hanterad tjänst skapas lämpliga undernät i den virtuella hubben för de olika gatewayerna/tjänsterna (till exempel VPN-gatewayer, ExpressRoute-gatewayer, VPN-anslutningar från plats till plats, brand vägg, Routning och så vidare).
4. Välj **Nästa: plats-till-plats**.

    ![Skärm bild som visar fönstret Skapa virtuellt nav med plats-till-plats vald.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Plats-till-plats")

5. På fliken **plats-till-plats** fyller du i följande fält:

   * Välj **Ja** om du vill skapa en plats-till-plats-VPN.
   * Det går inte att redigera fältet AS Number.
   * Välj värdet för **Gateway Scale units** i list rutan. Med skalnings enheten kan du välja det sammanställda data flödet för VPN-gatewayen som skapas i den virtuella hubben för att ansluta platser till. Om du väljer 1 skalnings enhet = 500 Mbit/s betyder det att två instanser för redundans skapas, var och en har ett maximalt data flöde på 500 Mbit/s. Om du till exempel har fem grenar, var och en med 10 Mbit/s på grenen, behöver du en agg regering på 50 Mbit/s vid Head-slutet. Du bör planera den sammanställda kapaciteten för Azure VPN-gatewayen när du har bedömt kapaciteten som krävs för att stödja antalet grenar till hubben.
6. Välj **Granska + skapa** för att validera.
7. Välj **skapa** för att skapa hubben. Efter 30 minuter, **Uppdatera** för att Visa hubben på sidan **hubbar** . Välj **gå till resurs** för att navigera till resursen.
