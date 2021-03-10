---
title: Öppna portar till en virtuell dator med hjälp av Azure Portal
description: Lär dig hur du öppnar en port/skapar en slut punkt för din virtuella dator med hjälp av Azure Portal
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550458"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Så här öppnar du portar till en virtuell dator med Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Sök efter och Välj resurs gruppen för den virtuella datorn, Välj **Lägg till** och Sök sedan efter och välj **nätverks säkerhets grupp**.

1. Välj **Skapa**.

    Fönstret **skapa nätverks säkerhets grupp** öppnas.

    ![Skapa en nätverkssäkerhetsgrupp](./media/nsg-quickstart-portal/create-nsg.png)

1. Ange ett namn för nätverks säkerhets gruppen. 

1. Välj eller skapa en resurs grupp och välj sedan en plats.

1. Välj **skapa** för att skapa nätverks säkerhets gruppen.

## <a name="create-an-inbound-security-rule"></a>Skapa en inkommande säkerhets regel

1. Välj den nya nätverks säkerhets gruppen. 

1. Välj **inkommande säkerhets regler** på den vänstra menyn och välj sedan **Lägg till**.

    ![Växla till sidan Avancerat](./media/nsg-quickstart-portal/advanced.png)

1. På sidan **Lägg till en inkommande säkerhets regel** växlar du till **Avancerad** från **grundläggande** överst på sidan. 

1. Välj en gemensam **tjänst** i den nedrullningsbara menyn, till exempel **http**. Du kan också välja **anpassad** om du vill ange en speciell port som ska användas. 

1. Du kan också ändra **prioritet** eller **namn**. Prioriteten påverkar ordningen i vilken reglerna tillämpas: det lägre numeriska värdet, som är det tidigare regeln tillämpas.

1. Välj **Lägg till** för att skapa regeln.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Associera nätverks säkerhets gruppen med ett undernät

Det sista steget är att associera nätverkssäkerhetsgruppen med ett undernät eller ett visst nätverksgränssnitt. I det här exemplet associerar vi nätverks säkerhets gruppen med ett undernät. 

1. Välj **undernät** på den vänstra menyn och välj sedan **associera**.

1. Välj ditt virtuella nätverk och välj sedan lämpligt undernät.

    ![Associera en nätverks säkerhets grupp med virtuella nätverk](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. När du är klar väljer du **OK**.

## <a name="additional-information"></a>Ytterligare information

Du kan också [utföra stegen i den här artikeln med hjälp av Azure PowerShell](nsg-quickstart-powershell.md).

Kommandona som beskrivs i den här artikeln gör att du snabbt kan få trafik som flödar till den virtuella datorn. Nätverks säkerhets grupper ger många fantastiska funktioner och granularitet för att kontrol lera åtkomsten till dina resurser. Mer information finns i [filtrera nätverks trafik med en nätverks säkerhets grupp](../../virtual-network/tutorial-filter-network-traffic.md).

För webb program med hög tillgänglighet bör du överväga att placera dina virtuella datorer bakom en Azure Load Balancer. Belastningsutjämnaren distribuerar trafik till virtuella datorer, med en nätverks säkerhets grupp som tillhandahåller trafik filtrering. Mer information finns i [belastningsutjämna virtuella Windows-datorer i Azure för att skapa ett program med hög](tutorial-load-balancer.md)tillgänglighet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en nätverks säkerhets grupp, skapat en regel för inkommande trafik som tillåter HTTP-trafik på port 80 och sedan associerar regeln med ett undernät. 

Du hittar information om att skapa mer detaljerade miljöer i följande artiklar:
- [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Säkerhetsgrupper](../../virtual-network/network-security-groups-overview.md)
