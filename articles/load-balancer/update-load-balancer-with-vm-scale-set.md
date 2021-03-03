---
title: Uppdatera eller ta bort en befintlig belastningsutjämnare som används av skalnings uppsättningar för virtuella datorer
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Med den här instruktions artikeln kan du komma igång med Azure Standard Load Balancer och skalnings uppsättningar för virtuella datorer.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 952889777e4236d7fa03fad5b1bdbf98499f7066
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721318"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Uppdatera eller ta bort en belastningsutjämnare som används av skalnings uppsättningar för virtuella datorer

När du arbetar med skalnings uppsättningar för virtuella datorer och en instans av Azure Load Balancer kan du:

- Lägg till, uppdatera och ta bort regler.
- Lägg till konfigurationer.
- Ta bort belastningsutjämnaren.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Konfigurera en belastningsutjämnare för skalning av skalnings uppsättningar för virtuella datorer

Se till att instansen av Azure Load Balancer har en [inkommande NAT-pool](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) och att den virtuella datorns skalnings uppsättning placeras i belastningsutjämnaren. Load Balancer skapar automatiskt nya inkommande NAT-regler i den inkommande NAT-poolen när nya virtuella dator instanser läggs till i skalnings uppsättningen för den virtuella datorn.

Så här kontrollerar du om den inkommande NAT-poolen är korrekt konfigurerad:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **alla resurser** på den vänstra menyn. Välj sedan **MyLoadBalancer** i resurs listan.
1. Under **Inställningar** väljer du **ingående NAT-regler**. Om du ser en lista över regler som har skapats för varje enskild instans i den virtuella datorns skalnings uppsättning i den högra rutan, är du redo att gå vidare för att skala upp när som helst.

## <a name="add-inbound-nat-rules"></a>Lägg till inkommande NAT-regler

Det går inte att lägga till enskilda inkommande NAT-regler. Men du kan lägga till en uppsättning inkommande NAT-regler med ett definierat frontend-port intervall och backend-port för alla instanser i den virtuella datorns skal uppsättning.

Om du vill lägga till en hel uppsättning inkommande NAT-regler för den virtuella datorns skalnings uppsättningar måste du först skapa en inkommande NAT-pool i belastningsutjämnaren. Referera sedan till den inkommande NAT-poolen från nätverks profilen för den virtuella datorns skalnings uppsättning. Ett fullständigt exempel som använder CLI visas.

Den nya inkommande NAT-poolen ska inte ha ett överlappande port intervall för front-end med befintliga inkommande NAT-pooler. Om du vill visa befintliga inkommande NAT-pooler som har kon figurer ATS använder du detta [CLI-kommando](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list):
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Uppdatera inkommande NAT-regler

Det går inte att uppdatera enskilda inkommande NAT-regler. Men du kan uppdatera en uppsättning inkommande NAT-regler med ett definierat klient dels port intervall och en backend-port för alla instanser i skalnings uppsättningen för den virtuella datorn.

Uppdatera den inkommande NAT-poolen i belastningsutjämnaren för att uppdatera en hel uppsättning inkommande NAT-regler för virtuell dators skalnings uppsättningar.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Ta bort inkommande NAT-regler

Enskilda inkommande NAT-regler kan inte tas bort, men du kan ta bort hela uppsättningen inkommande NAT-regler genom att ta bort den inkommande NAT-poolen.

Ta bort NAT-poolen genom att först ta bort den från skalnings uppsättningen. Ett fullständigt exempel som använder CLI visas här:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Lägg till flera IP-konfigurationer

Lägga till flera IP-konfigurationer:

1. Välj **alla resurser** på den vänstra menyn. Välj sedan **MyLoadBalancer** i resurs listan.
1. Under **Inställningar** väljer du **IP-konfiguration för klient delen**. Välj **Lägg till**.
1. På sidan **Lägg till IP-adress för klient** del anger du värdena och väljer **OK**.
1. Följ [steg 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) och [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) i den här självstudien om nya regler för belastnings utjämning behövs.
1. Skapa en ny uppsättning inkommande NAT-regler genom att använda de nyligen skapade frontend-IP-konfigurationerna om det behövs. Ett exempel finns i föregående avsnitt.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Flera Virtual Machine Scale Sets bakom samma Load Balancer

Skapa en inkommande NAT-pool i Load Balancer, referera till den inkommande NAT-poolen i nätverks profilen för en skalnings uppsättning för virtuella datorer och uppdatera slutligen instanserna för att ändringarna ska börja gälla. Upprepa stegen för alla Virtual Machine Scale Sets.

Se till att skapa separata inkommande NAT-pooler med icke-överlappande klient dels port intervall.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Ta bort klient delens IP-konfiguration som används av den virtuella datorns skal uppsättning

Ta bort klient delens IP-konfiguration som används av skalnings uppsättningen:

 1. Ta först bort den inkommande NAT-poolen (den uppsättning inkommande NAT-regler) som refererar till klient delens IP-konfiguration. Instruktioner för hur du tar bort de inkommande reglerna finns i föregående avsnitt.
 1. Ta bort den belastnings Utjämnings regel som refererar till klient delens IP-konfiguration.
 1. Ta bort klient delens IP-konfiguration.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Ta bort en belastningsutjämnare som används av en skalnings uppsättning för virtuell dator

Ta bort klient delens IP-konfiguration som används av skalnings uppsättningen:

 1. Ta först bort den inkommande NAT-poolen (den uppsättning inkommande NAT-regler) som refererar till klient delens IP-konfiguration. Instruktioner för hur du tar bort de inkommande reglerna finns i föregående avsnitt.
 1. Ta bort belastnings Utjämnings regeln som refererar till backend-poolen som innehåller den virtuella datorns skal uppsättning.
 1. Ta bort `loadBalancerBackendAddressPool` referensen från nätverks profilen för den virtuella datorns skal uppsättning.
 
 Ett fullständigt exempel som använder CLI visas här:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Ta slutligen bort belastnings Utjämnings resursen.
 
## <a name="next-steps"></a>Nästa steg

Läs mer om begreppen för att lära dig mer om Azure Load Balancer och skalnings uppsättningar för virtuella datorer.

> [Azure Load Balancer med skalnings uppsättningar för virtuella datorer](load-balancer-standard-virtual-machine-scale-sets.md)
