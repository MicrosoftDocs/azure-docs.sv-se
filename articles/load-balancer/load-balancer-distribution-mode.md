---
title: Konfigurera Azure Load Balancer distributionsläge
titleSuffix: Azure Load Balancer
description: I den här artikeln kommer du igång med att konfigurera distributionsläget för Azure Load Balancer att stödja källans IP-tillhörighet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 0c6b845a8176054dc5ec6cfc239e609f568c925d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483647"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurera distributionsläget för Azure Load Balancer

Azure Load Balancer två distributionslägen för att distribuera trafik till dina program:

* Hash-baserad
* Källans IP-tillhörighet

I den här artikeln får du lära dig hur du konfigurerar distributionsläget för Azure Load Balancer.


## <a name="configure-distribution-mode"></a>Konfigurera distributionsläge

---

# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

Du kan ändra distributionslägets konfiguration genom att ändra belastningsutjämningsregeln i portalen.

1. Logga in på Azure Portal och leta upp resursgruppen som innehåller den lastbalanserare som du vill ändra genom att klicka på **Resursgrupper.**
2. På översiktsskärmen för lastbalanserare väljer du **Belastningsutjämningsregler** under **Inställningar.**
3. På skärmen belastningsutjämningsregler väljer du den belastningsutjämningsregel som du vill ändra distributionsläget för.
4. Under regeln ändras distributionsläget genom att ändra **listrutan** Sessionspersistence. 

Följande alternativ är tillgängliga: 

* **Ingen (hashbaserad)** – Anger att efterföljande begäranden från samma klient kan hanteras av valfri virtuell dator.
* **Klient-IP (käll-IP-tillhörighet** med två tupplar) – Anger att efterföljande begäranden från samma klient-IP-adress ska hanteras av samma virtuella dator.
* **Klient-IP och protokoll (käll-IP-tillhörighet** med tre tupplar) – Anger att efterföljande begäranden från samma klient-IP-adress och protokollkombination ska hanteras av samma virtuella dator.

5. Välj distributionsläge och välj sedan **Spara.**

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="Ändra sessionspersens på lastbalanseringsregeln." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd PowerShell för att ändra distributionsinställningarna för lastbalanserare på en befintlig belastningsutjämningsregel. Följande kommando uppdaterar distributionsläget: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

Ange värdet för elementet för `LoadDistribution` den typ av belastningsutjämning som krävs. 

* Ange **SourceIP för** belastningsutjämning med två tuppeln (käll-IP och mål-IP). 

* Ange **SourceIPProtocol för** belastningsutjämning med tre tuppeln (käll-IP, mål-IP och protokolltyp). 

* Ange **Standard för** standardbeteendet för belastningsutjämning med fem tuppeln.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Använd Azure CLI för att ändra distributionsinställningarna för lastbalanserare i en befintlig belastningsutjämningsregel.  Följande kommando uppdaterar distributionsläget:

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
Ange värdet för för `--load-distribution` den typ av belastningsutjämning som krävs.

* Ange **SourceIP för** belastningsutjämning med två tuppeln (käll-IP och mål-IP). 

* Ange **SourceIPProtocol för** belastningsutjämning med tre tuppeln (käll-IP, mål-IP och protokolltyp). 

* Ange **Standard för** standardbeteendet för belastningsutjämning med fem tuppeln.

Mer information om kommandot som används i den här artikeln finns i [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)

---

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Load Balancer](load-balancer-overview.md)
* [Kom igång med att konfigurera en Internetuppriktad lastbalanserare](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)
