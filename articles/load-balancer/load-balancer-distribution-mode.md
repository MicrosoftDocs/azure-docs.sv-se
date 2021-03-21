---
title: Konfigurera Azure Load Balancer distributions läge
titleSuffix: Azure Load Balancer
description: I den här artikeln ska du komma igång med att konfigurera distributions läget för Azure Load Balancer att stödja Källans IP-tillhörighet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739924"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurera distributions läget för Azure Load Balancer

Azure Load Balancer stöder två distributions lägen för att distribuera trafik till dina program:

* Hash-baserad
* Källans IP-tillhörighet

I den här artikeln får du lära dig hur du konfigurerar distributions läget för Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Konfigurera distributions läge

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Du kan ändra konfigurationen för distributions läget genom att ändra belastnings Utjämnings regeln i portalen.

1. Logga in på Azure Portal och leta upp resurs gruppen som innehåller den belastningsutjämnare som du vill ändra genom att klicka på **resurs grupper**.
2. I översikts fönstret belastningsutjämnare väljer du regler för **belastnings utjämning** under **Inställningar**.
3. På skärmen belastnings Utjämnings regler väljer du den belastnings Utjämnings regel som du vill ändra distributions läget.
4. Under regeln ändras distributions läget genom att du ändrar List rutan för **sessionens persistens** . 

Följande alternativ är tillgängliga: 

* **Ingen (Hash-baserad)** – anger att efterföljande begär Anden från samma klient kan hanteras av alla virtuella datorer.
* **Klientens IP-adress (IP-tupel för källa)** – anger att efterföljande förfrågningar från samma klient-IP-adress ska hanteras av samma virtuella dator.
* **Klientens IP och protokoll (Källans IP-tillhörighet)** -anger att efterföljande förfrågningar från samma klient-IP-adress och protokoll kombination ska hanteras av samma virtuella dator.

5. Välj distributions läge och välj sedan **Spara**.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Ändra persistence för en belastnings Utjämnings regel." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd PowerShell för att ändra distributions inställningarna för belastningsutjämnare för en befintlig belastnings Utjämnings regel. Följande kommando uppdaterar distributions läget: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Ange värdet för `LoadDistribution` elementet för den typ av belastnings utjämning som krävs. 

* Ange **SourceIP** för belastnings utjämning för två tupleer (käll-IP och mål-IP). 

* Ange **SourceIPProtocol** för tre tupler (käll-IP, mål-IP och protokoll typ) belastnings utjämning. 

* Ange **standard** för belastnings utjämning för fem tupler.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Använd Azure CLI för att ändra distributions inställningarna för belastningsutjämnare för en befintlig belastnings Utjämnings regel.  Följande kommando uppdaterar distributions läget:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Ange värdet `--load-distribution` för den typ av belastnings utjämning som krävs.

* Ange **SourceIP** för belastnings utjämning för två tupleer (käll-IP och mål-IP). 

* Ange **SourceIPProtocol** för tre tupler (käll-IP, mål-IP och protokoll typ) belastnings utjämning. 

* Ange **standard** för belastnings utjämning för fem tupler.

Mer information om kommandot som används i den här artikeln finns i [AZ Network lb Rule Update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Load Balancer](load-balancer-overview.md)
* [Kom igång med att konfigurera en Internet-riktad belastningsutjämnare](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)