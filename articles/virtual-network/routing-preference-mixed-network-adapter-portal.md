---
title: Konfigurera inställnings alternativ för båda routningen för en virtuell dator – Azure Portal
description: Lär dig hur du aktiverar båda alternativen för routning av inställningar
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680151"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Konfigurera inställnings alternativ för båda routningen för en virtuell dator

Den här artikeln visar hur du konfigurerar [Inställningar för routning](./routing-preference-overview.md) (Internet och Microsoft globalt nätverk) för en virtuell dator (VM). Detta uppnås med hjälp av två virtuella nätverks gränssnitt, ett nätverks gränssnitt med en offentlig IP-adress via det globala nätverket i Microsoft och det andra med en offentlig IP-dirigerad via ett Internet leverantörs nätverk.

## <a name="prerequisites"></a>Förutsättningar

Skapa en virtuell dator med en offentlig IP-adress genom att följa anvisningarna i [skapa en virtuell dator med en statisk offentlig IP-adress med](./virtual-network-deploy-static-pip-arm-portal.md) hjälp av Azure Portal med hjälp av Azure Portal. Standard alternativet för routning för den offentliga IP-adressen är via **Microsoft globalt nätverk**. När du har skapat en virtuell dator med en offentlig IP-adress, lägger du till en andra offentlig IP-adress till den virtuella datorn som dirigerar trafik via Internet leverantörs nätverk med dess Dirigerings inställningar som är konfigurerade som **Internet**

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Skapa en offentlig IP-adress med ett inställnings alternativ för routning på Internet
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**. 
3. I rutan Sök skriver du *offentlig IP-adress*.
4. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa** på sidan **offentlig IP-adress** .
5. I **Inställningar för routning** väljer du **Internet**.

      ![Skapa en offentlig IP-adress](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Offentliga IP-adresser skapas med en IPv4- eller IPv6-adress. Men routnings inställningarna stöder för närvarande endast IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Skapa ett nätverks gränssnitt och associera den offentliga IP-adressen

1. Skapa ett [nätverks gränssnitt](./routing-preference-overview.md) med hjälp av Azure Portal med standardinställningar. 
1. [Associera den offentliga IP-adressen med det nätverks gränssnitt som](./associate-public-ip-address-vm.md) skapades i föregående avsnitt. Det kan ta några sekunder innan en IP-adress visas. Visa den offentliga IP-adress som tilldelats IP-konfigurationen, som visas:

    ![Associera den offentliga IP-adressen](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Bifoga nätverks gränssnitt till den virtuella datorn

1. [Bifoga nätverks gränssnittet som skapades i föregående avsnitt till den virtuella datorn](./virtual-network-network-interface-vm.md).
2. Nu kan du Visa två virtuella nätverks gränssnitt som är kopplade till den virtuella datorn, ett med en offentlig IP-adress som dirigeras via Microsoft globalt nätverk och den andra dirigeras via med ett ISP-nätverk som visas:  ![ koppla ett nätverks gränssnitt till en virtuell dator](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [offentlig IP-adress med inställningar för routning](routing-preference-overview.md).
- [Konfigurera cirkulations inställningar för en virtuell dator](tutorial-routing-preference-virtual-machine-portal.md).
- [Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av PowerShell](routing-preference-powershell.md).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).