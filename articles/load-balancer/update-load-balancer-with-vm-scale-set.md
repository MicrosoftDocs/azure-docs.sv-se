---
title: Uppdatera eller ta bort befintliga Azure Load Balancer-objekt som används av VM-skalningsuppsättningar
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Med den här instruktions artikeln kan du komma igång med Azure Standard Load Balancer och Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: d5614490bfd2cfb67b6b7afd7b7b8643bbf754bd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790097"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Uppdatera/ta bort Azure Load Balancer som används av Virtual Machine Scale Sets

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Konfigurera Azure Load Balancer för att skala ut Virtual Machine Scale Sets
  * Kontrol lera att Load Balancer har en [inkommande NAT-pool](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) och att den virtuella datorns skalnings uppsättning placeras i Server delen för Load Balancer. Azure Load Balancer skapar automatiskt nya inkommande NAT-regler i den inkommande NAT-poolen när nya virtuella dator instanser läggs till i skalnings uppsättningen för den virtuella datorn. 
  * För att kontrol lera om den inkommande NAT-poolen är korrekt konfigurerad, 
  1. Logga in på Azure Portal på https://portal.azure.com.
  
  1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
  
  1. Under **Inställningar** väljer du **ingående NAT-regler**.
Om du ser i den högra rutan visas en lista över regler som har skapats för varje enskild instans i den virtuella datorns skalnings uppsättning.

## <a name="how-to-add-inbound-nat-rules"></a>Hur lägger du till inkommande NAT-regler? 
  * Det går inte att lägga till en enskild inkommande NAT-regel. Du kan dock lägga till en uppsättning inkommande NAT-regler med ett definierat port intervall för klient delen och Server dels porten för alla instanser i skalnings uppsättningen för den virtuella datorn.
  * Om du vill lägga till en hel uppsättning inkommande NAT-regler för Virtual Machine Scale Sets måste du först skapa en inkommande NAT-pool i Load Balancer och sedan referera till den inkommande NAT-poolen från nätverks profilen för den virtuella datorns skalnings uppsättning. Ett fullständigt exempel som använder CLI visas nedan.
  * Den nya inkommande NAT-poolen ska inte ha överlappande port intervall för klient delen med befintliga inkommande NAT-pooler. Du kan använda detta [CLI-kommando](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) om du vill visa befintliga inkommande NAT-pooler
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
## <a name="how-to-update-inbound-nat-rules"></a>Hur uppdaterar jag inkommande NAT-regler? 
  * Det går inte att uppdatera en enskild inkommande NAT-regel. Du kan dock uppdatera en uppsättning inkommande NAT-regler med ett definierat port intervall för klient delen och backend-porten för alla instanser i skalnings uppsättningen för den virtuella datorn.
  * För att kunna uppdatera en hel uppsättning inkommande NAT-regler för Virtual Machine Scale Sets måste du uppdatera den inkommande NAT-poolen i Load Balancer. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Hur tar du bort inkommande NAT-regler? 
* Det går inte att ta bort en enskild inkommande NAT-regel. Du kan dock ta bort hela uppsättningen inkommande NAT-regler.
* För att ta bort hela uppsättningen inkommande NAT-regler som används av skalnings uppsättningen måste du först ta bort NAT-poolen från skalnings uppsättningen. Ett fullständigt exempel som använder CLI visas nedan:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Så här lägger du till flera IP-konfigurationer:
1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **IP-konfigurationer för klient** del och väljer sedan **Lägg till**.
   
1. På sidan **Lägg till klient delens IP-adress** skriver du in värdena och väljer **OK**

1. Följ [steg 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) och [steg 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) i den här självstudien om nya regler för belastnings utjämning behövs

1. Skapa en ny uppsättning inkommande NAT-regler med de nyligen skapade IP-konfigurationerna för klient delen vid behov. Du hittar exempel här i avsnittet [föregående].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Ta bort klient delens IP-konfiguration som används av den virtuella datorns skal uppsättning: 
 1. Om du vill ta bort klient delens IP-konfiguration som används av skalnings uppsättningen måste du först ta bort den inkommande NAT-poolen (uppsättning inkommande NAT-regler) som refererar till IP-konfigurationen för klient delen. Instruktioner för hur du tar bort reglerna för inkommande trafik finns i föregående avsnitt.
 1. Ta bort belastnings Utjämnings regeln som refererar till klient delens IP-konfiguration. 
 1. Ta bort klient delens IP-konfiguration.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Ta bort Azure Load Balancer som används av den virtuella datorns skal uppsättning: 
 1. Om du vill ta bort klient delens IP-konfiguration som används av skalnings uppsättningen måste du först ta bort den inkommande NAT-poolen (uppsättning inkommande NAT-regler) som refererar till IP-konfigurationen för klient delen. Instruktioner för hur du tar bort reglerna för inkommande trafik finns i föregående avsnitt.
 
 1. Ta bort belastnings Utjämnings regeln som refererar till backend-poolen som innehåller skalnings uppsättningen för den virtuella datorn.
 
 1. Ta bort loadBalancerBackendAddressPool-referensen från nätverks profilen för den virtuella datorns skal uppsättning. Ett fullständigt exempel som använder CLI visas nedan:
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
Ta slutligen bort Load Balancer resursen.
 
## <a name="next-steps"></a>Nästa steg

Läs mer om begreppen för att lära dig mer om Azure Load Balancer och skalnings uppsättningen för virtuella datorer.

> [Azure Load Balancer med skalnings uppsättningar för virtuella Azure-datorer](load-balancer-standard-virtual-machine-scale-sets.md)