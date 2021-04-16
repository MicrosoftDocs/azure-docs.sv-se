---
title: Uppdatera eller ta bort en befintlig lastbalanserare som används av VM-skalningsuppsättningar
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Med den här artikeln kan du komma igång med Azure Standard Load Balancer och VM-skalningsuppsättningar.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 2079eeb97ac935ba24c6ff0616a58fbb28a962f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480094"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Uppdatera eller ta bort en lastbalanserare som används av VM-skalningsuppsättningar

När du arbetar med VM-skalningsuppsättningar och en instans av Azure Load Balancer kan du:

- Lägg till, uppdatera och ta bort regler.
- Lägg till konfigurationer.
- Ta bort lastbalanseraren.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Konfigurera en lastbalanserare för att skala ut VM-skalningsuppsättningar

Kontrollera att instansen av Azure Load Balancer har en [inkommande NAT-pool](/cli/azure/network/lb/inbound-nat-pool) konfigurerad och att VM-skalningsuppsättningen läggs i lastbalanseringspoolens serverpool. Load Balancer automatiskt nya inkommande NAT-regler i den inkommande NAT-poolen när nya instanser av virtuella datorer läggs till i VM-skalningsuppsättningen.

Så här kontrollerar du om den inkommande NAT-poolen är korrekt konfigurerad:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj Alla resurser på den **vänstra menyn.** Välj sedan **MyLoadBalancer** i resurslistan.
1. Under **Inställningar** väljer du **Inkommande NAT-regler.** Om du ser en lista över regler som skapats för varje enskild instans i VM-skalningsuppsättningen är du redo att skala upp när som helst i den högra rutan.

## <a name="add-inbound-nat-rules"></a>Lägga till ingående NAT-regler

Det går inte att lägga till enskilda nat-regler för inkommande trafik. Men du kan lägga till en uppsättning inkommande NAT-regler med definierat portintervall för frontend och serverport för alla instanser i VM-skalningsuppsättningen.

Om du vill lägga till en hel uppsättning inkommande NAT-regler för VM-skalningsuppsättningar skapar du först en inkommande NAT-pool i lastbalanseraren. Referera sedan till den inkommande NAT-poolen från nätverksprofilen för VM-skalningsuppsättningen. Ett fullständigt exempel med CLI visas.

Den nya inkommande NAT-poolen får inte ha ett överlappande portintervall på frontend-sidan med befintliga inkommande NAT-pooler. Om du vill visa befintliga inkommande NAT-pooler som har ställts in använder du [följande CLI-kommando:](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list)
  
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Uppdatera regler för inkommande NAT

Enskilda inkommande NAT-regler kan inte uppdateras. Men du kan uppdatera en uppsättning inkommande NAT-regler med ett definierat portintervall för frontend och en serverport för alla instanser i VM-skalningsuppsättningen.

Om du vill uppdatera en hel uppsättning inkommande NAT-regler för VM-skalningsuppsättningar uppdaterar du den inkommande NAT-poolen i lastbalanseraren.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Ta bort ingående NAT-regler

Enskilda inkommande NAT-regler kan inte tas bort, men du kan ta bort hela uppsättningen med ingående NAT-regler genom att ta bort den inkommande NAT-poolen.

Om du vill ta bort NAT-poolen tar du först bort den från skalningsuppsättningen. Ett fullständigt exempel med CLI visas här:

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
       -â€“lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Lägga till flera IP-konfigurationer

Så här lägger du till flera IP-konfigurationer:

1. På den vänstra menyn väljer du **Alla resurser**. Välj sedan **MyLoadBalancer** i resurslistan.
1. Under **Inställningar** väljer du **IP-konfiguration för frontend.** Välj **Lägg till**.
1. På sidan **Lägg till IP-adress för frontend** anger du värdena och väljer **OK.**
1. Följ [steg 5 och](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) steg [6 i den](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) här självstudien om nya regler för belastningsutjämning behövs.
1. Skapa en ny uppsättning inkommande NAT-regler med hjälp av de nyligen skapade IP-konfigurationerna för frontend-IP om det behövs. Ett exempel finns i föregående avsnitt.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Flera Virtual Machine Scale Sets bakom en enda Load Balancer

Skapa en inkommande NAT-pool i Load Balancer, referera till den inkommande NAT-poolen i nätverksprofilen för en VM-skalningsuppsättning och uppdatera slutligen instanserna för att ändringarna ska gälla. Upprepa stegen för alla Virtual Machine Scale Sets.

Se till att skapa separata inkommande NAT-pooler med portintervall som inte överlappar frontend.
  
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
          -â€“instance-ids *
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
          -â€“instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Ta bort frontend-IP-konfigurationen som används av VM-skalningsuppsättningen

Ta bort frontend-IP-konfigurationen som används av skalningsuppsättningen:

 1. Ta först bort den inkommande NAT-poolen (uppsättningen med inkommande NAT-regler) som refererar till FRONTEND-IP-konfigurationen. Anvisningar om hur du tar bort regler för inkommande trafik finns i föregående avsnitt.
 1. Ta bort belastningsutjämningsregeln som refererar till FRONTEND-IP-konfigurationen.
 1. Ta bort frontend-IP-konfigurationen.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Ta bort en lastbalanserare som används av en VM-skalningsuppsättning

Ta bort frontend-IP-konfigurationen som används av skalningsuppsättningen:

 1. Ta först bort den inkommande NAT-poolen (uppsättningen med inkommande NAT-regler) som refererar till FRONTEND-IP-konfigurationen. Anvisningar om hur du tar bort regler för inkommande trafik finns i föregående avsnitt.
 1. Ta bort belastningsutjämningsregeln som refererar till serverpoolen som innehåller VM-skalningsuppsättningen.
 1. Ta bort `loadBalancerBackendAddressPool` referensen från nätverksprofilen för VM-skalningsuppsättningen.
 
 Ett fullständigt exempel med CLI visas här:

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
Ta slutligen bort lastbalanseringsresursen.
 
## <a name="next-steps"></a>Nästa steg

Mer information om Azure Load Balancer och VM-skalningsuppsättningar finns i begreppen.

> [Azure Load Balancer med VM-skalningsuppsättningar](load-balancer-standard-virtual-machine-scale-sets.md)
