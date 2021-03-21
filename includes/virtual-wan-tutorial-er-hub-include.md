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
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73491454"
---
1. Leta upp det virtuella WAN-nätverket som du har skapat. På sidan virtuellt WAN, under avsnittet **anslutning** , väljer du **hubbar**.
2. På sidan hubbar väljer du **+ ny hubb** för att öppna sidan **Skapa virtuell hubb** .
3. Fyll i följande fält på fliken **grundläggande grunder** på sidan **Skapa virtuell hubb** :

   ![Grundläggande inställningar](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Grundläggande inställningar")

    **Projektinformation**

   * Region (tidigare kallad plats)
   * Name
   * NAV, privat adress utrymme. Det minsta adress utrymmet är/24 för att skapa en hubb, vilket innebär att allt mellan/25 och/32 genererar ett fel när det skapas.
4. Välj **fliken ExpressRoute**.

5. Fyll i följande fält på fliken **ExpressRoute** :

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Välj **Ja** om du vill skapa en **ExpressRoute** -Gateway.
   * Välj värdet för **Gateway Scale units** i list rutan.
6. Välj **Granska + skapa** för att validera.
7. Välj **skapa** för att skapa hubben. Efter 30 minuter, **Uppdatera** för att Visa hubben på sidan **hubbar** . Välj **gå till resurs** för att navigera till resursen.