---
title: Konfigurera TCP-återställning för belastnings utjämning och tids gräns för inaktivitet
titleSuffix: Azure Load Balancer
description: I den här artikeln lär du dig hur du konfigurerar Azure Load Balancer timeout för TCP-inaktivitet och återställning.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747207"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Konfigurera TCP-återställning och tids gräns för inaktivitet för Azure Load Balancer

Azure Load Balancer har följande intervall för inaktivitet:

* 4 minuter till 100 minuter för utgående regler
* 4 minuter till 30 minuter för Load Balancer regler och inkommande NAT-regler

Som standard är den inställd på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls mellan klienten och tjänsten. 

I följande avsnitt beskrivs hur du ändrar inställningarna för inaktiv tids gräns och TCP-återställning för belastnings Utjämnings resurser.

## <a name="set-tcp-reset-and-idle-timeout"></a>Ange TCP-återställning och tids gräns för inaktivitet
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Om du vill ange tids gräns för inaktivitet och TCP-återställning för en belastningsutjämnare redigerar du den belastningsutjämnade regeln. 

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **resurs grupper** på den vänstra menyn.

3. Välj resurs grupp för belastningsutjämnaren. I det här exemplet heter resurs gruppen **myResourceGroup**.

4. Välj din lastbalanserare. I det här exemplet heter belastningsutjämnaren **myLoadBalancer**.

5. I **Inställningar** väljer du **belastnings Utjämnings regler**.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Redigera regler för belastnings utjämning." border="true":::

6. Välj din belastnings Utjämnings regel. I det här exemplet heter belastnings Utjämnings regeln **myLBrule**.

7. I belastnings Utjämnings regeln flyttar du skjutreglaget i **tids gräns för inaktivitet (minuter)** till ditt timeout-värde.  

8. Under **TCP-återställning** väljer du **aktive rad**.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Ange tids gräns för inaktivitet och TCP-återställning." border="true":::

9. Välj **Spara**.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Om du vill ange tids gräns för inaktivitet och TCP-återställning anger du värden i följande regler för belastnings utjämning med [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Ersätt följande exempel med värdena från dina resurser:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

Om du vill ange tids gräns för inaktivitet och TCP-återställning använder du följande parametrar för [AZ Network lb Rule Update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--Idle-timeout**
* **--Aktivera-TCP-reset**

Verifiera din miljö innan du börjar:

* Logga in på Azure Portal och kontrol lera att din prenumeration är aktiv genom att köra `az login` .
* Kontrol lera din version av Azure CLI i ett terminalfönster eller kommando fönster genom att köra `az --version` . Den senaste versionen finns i den senaste versions [informationen](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Om du inte har den senaste versionen uppdaterar du installationen genom att följa [installations guiden för ditt operativ system eller din plattform](/cli/azure/install-azure-cli).

Ersätt följande exempel med värdena från dina resurser:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Nästa steg

Mer information om timeout för TCP-inaktivitet och återställning finns i [load BALANCER TCP-återställning och tids gräns för inaktivitet](load-balancer-tcp-reset.md)

Mer information om hur du konfigurerar distributions läget för belastningsutjämnaren finns i [Konfigurera ett distributions läge för belastningsutjämnare](load-balancer-distribution-mode.md).
