---
title: 'Konfigurera anpassad IPsec-princip för Azure Virtual WAN: Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar anpassad IPsec-princip för Azure Virtual WAN med portalen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851189"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurera en anpassad IPsec-princip för virtuellt WAN-nätverk med hjälp av portalen

Du kan konfigurera en anpassad IPsec-princip för en virtuell WAN-anslutning i Azure Portal. Anpassade principer är användbara när du vill att både sidor (lokal och Azure VPN-gateway) ska använda samma inställningar för IKE fas 1 och IKE fas 2.

## <a name="working-with-custom-policies"></a>Arbeta med anpassade principer

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurera en princip

1. **Leta upp den virtuella hubben**. Öppna en webbläsare, navigera till [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) och logga in med ditt Azure-konto. Navigera till din virtuella WAN-resurs och leta upp den virtuella hubb som VPN-platsen är ansluten till.
2. **Välj VPN-platsen**. På sidan hubb klickar du på **VPN (plats till plats)** och väljer den VPN-plats som du vill konfigurera en anpassad IPsec-princip för.

   ![välj](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Redigera VPN-anslutningen**. Från **snabb menyn** **...** väljer du **Redigera VPN-anslutning**.

   ![redigera](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Konfigurera inställningarna**. På sidan **Redigera VPN-anslutning** ändrar du IPSec-inställningen från standard till anpassad och anpassar IPSec-principen. Spara inställningarna genom att välja **Spara** .

   ![Konfigurera och spara](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).