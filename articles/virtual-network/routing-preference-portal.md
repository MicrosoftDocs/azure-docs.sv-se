---
title: Konfigurera Dirigerings inställningar för en offentlig IP-adress – Azure Portal
description: Lär dig hur du skapar en offentlig IP-adress med inställningar för Internet trafik cirkulation
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
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672905"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av Azure Portal

Den här artikeln visar hur du konfigurerar [Inställningar för routning](./routing-preference-overview.md) via Internet leverantörs nätverk (**Internet** alternativ) för en offentlig IP-adress. När du har skapat den offentliga IP-adressen kan du associera den med följande Azure-resurser för inkommande och utgående trafik till Internet:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Belastningsutjämnare mot Internet
* Application Gateway
* Azure Firewall

Som standard är cirkulations inställningen för offentlig IP-adress inställd på Microsofts globala nätverk för alla Azure-tjänster och kan associeras med valfri Azure-tjänst.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Skapa en offentlig IP-adress med en cirkulations inställning
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Skapa en resurs**.
3. I rutan Sök skriver du *offentlig IP-adress*.
3. I Sök resultaten väljer du **offentlig IP-adress**. Sedan väljer du **skapa** på sidan **offentlig IP-adress** .
1. För SKU väljer du **standard**.
1. För **cirkulations inställningar** väljer du **Internet**.

      ![Skapa en offentlig IP-adress](./media/routing-preference-portal/public-ip-new.png)
1. I avsnittet **IPv4 IP-adresskonfiguration** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *RoutingPreferenceResourceGroup* och välj sedan **OK**. |
    | Location | Välj **USA, östra**.|
    | Tillgänglighetszon | Behåll standardvärdet- **Zone-redundant**. |
1. Välj **Skapa**.

    > [!NOTE]
    > Offentliga IP-adresser skapas med en IPv4- eller IPv6-adress. Men routnings inställningarna stöder för närvarande endast IPV4.

Du kan associera ovanstående skapade offentliga IP-adress med en virtuell [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator. Använd avsnittet CLI på sidan självstudie: [associera en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md#azure-cli) för att koppla den offentliga IP-adressen till den virtuella datorn. Du kan också associera den offentliga IP-adressen som skapats ovan med en [Azure Load Balancer](../load-balancer/load-balancer-overview.md), genom att tilldela den till konfiguration av belastningsutjämnarens **klient** del. Den offentliga IP-adressen fungerar som en belastningsutjämnad virtuell IP-adress (VIP).

## <a name="next-steps"></a>Nästa steg
- Läs mer om [offentlig IP-adress med inställningar för routning](routing-preference-overview.md).
- [Konfigurera cirkulations inställningar för en virtuell dator](tutorial-routing-preference-virtual-machine-portal.md).
- [Konfigurera cirkulations inställningar för en offentlig IP-adress med hjälp av PowerShell](routing-preference-powershell.md).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).