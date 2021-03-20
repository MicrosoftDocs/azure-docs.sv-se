---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328460"
---
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I **Sök efter resurser, tjänst och dokument (G +/)** skriver du *virtuellt nätverk*.

   ![Hitta Virtual Network resurs Sidan](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Hitta resurs sidan för virtuella nätverk")
1. Välj **Virtual Network** från **Marketplace** -resultatet.

   ![Välj virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Hitta resurs sidan för virtuella nätverk")
1. På sidan **Virtual Network** väljer du **skapa**.

   ![Sidan virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Välj Skapa")
1. När du har valt **skapa** öppnas sidan **Skapa virtuellt nätverk** .
1. På fliken **grundläggande** konfigurerar du **projekt information** och inställningar för **instans information** VNet.

   ![Fliken grunder](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Fliken Grundläggande") När du fyller i fälten visas en grön bock markering när tecknen du anger i fältet verifieras. Vissa värden är automatiskt ifyllda, och du kan ersätta med dina egna värden:

   - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.
   - **Resurs grupp**: Välj en befintlig resurs grupp eller klicka på **Skapa ny** för att skapa en ny. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Namn**: Ange namnet på det virtuella nätverket.
   - **Region**: Välj platsen för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att vara.

1. Konfigurera värdena på fliken **IP-adresser** . Värdena som visas i exemplen nedan är i demonstrations syfte. Justera värdena enligt de inställningar som du behöver.

   ![Fliken IP-adresser](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Fliken IP-adresser")  
   - **IPv4-adress utrymme**: som standard skapas ett adress utrymme automatiskt. Du kan klicka på adress utrymmet för att justera det så att det motsvarar dina egna värden. Du kan också lägga till ytterligare adress utrymmen.
   - **Undernät**: om du använder standard adress utrymmet skapas ett standard under nät automatiskt. Om du ändrar adress utrymmet måste du lägga till ett undernät. Välj **+ Lägg till undernät** för att öppna fönstret **Lägg till undernät** . Konfigurera följande inställningar och välj sedan **Lägg** till för att lägga till värdena:
      - **Under näts namn**: i det här exemplet heter vi under nätet "FrontEnd".
      - **Adress intervall för under nätet**: adress intervallet för det här under nätet.

1. Lämna standardvärdena på fliken **säkerhet** .

   - **DDoS-skydd**: grundläggande
   - **Brand vägg**: inaktive rad
1. Välj **Granska + skapa** för att verifiera inställningarna för det virtuella nätverket.
1. När inställningarna har verifierats väljer du **skapa**.
