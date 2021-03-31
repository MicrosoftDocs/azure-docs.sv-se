---
title: Uppgradera ett virtuellt Azure-nätverk från Basic till standard Azure Portal | Microsoft Docs
description: Du kan uppgradera din virtuella WAN-typ för att öka funktionaliteten.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91447358"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Uppgradera ett virtuellt WAN-nätverk från Basic till standard

Den här artikeln hjälper dig att uppgradera ett grundläggande WAN till ett standard WAN. En "grundläggande" WAN-typ skapar alla hubbar i den som grundläggande SKU-hubbar. I en grundläggande hubb är du begränsad till endast plats-till-plats-VPN-funktioner. En WAN-typ "standard" skapar alla hubbar i den som standard-SKU-hubbar. När du använder standard hubbar kan du aktivera ExpressRoute, användare (punkt-till-plats) VPN, en komplett näthubb och en VNet-till-VNet-överföring via Azure-hubbarna.

I följande tabell visas de konfigurationer som är tillgängliga för varje WAN-typ:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Ändra den virtuella WAN-typen

1. På sidan för ditt virtuella WAN-nätverk väljer du **konfiguration** för att öppna sidan konfiguration.

   ![Skärm bild som visar sidan "konfiguration" med text rutan information om hur du uppgraderar till ett standard-typ virtuellt WAN som är markerat längst ned.](./media/upgrade-virtual-wan/1.png)
2. För den virtuella WAN-typen väljer du **standard** i list rutan.

   ![Skärm bild som visar List rutan "virtuell WAN-typ".](./media/upgrade-virtual-wan/2.png)
3. Om du uppgraderar till ett standard virtuellt WAN-nätverk kan du inte återgå till ett grundläggande virtuellt WAN-nätverk. Välj **Bekräfta** om du vill uppgradera.

   ![Skärm bild som visar dialog rutan "uppgraderings bekräftelse".](./media/upgrade-virtual-wan/4.png)
4. När ändringen har sparats ser den virtuella WAN-sidan ut ungefär som i det här exemplet.

   ![Virtual WAN-diagram](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).