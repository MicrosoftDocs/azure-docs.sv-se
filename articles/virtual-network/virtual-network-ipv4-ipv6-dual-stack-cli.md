---
title: Distribuera IPv6-program med dubbla stackar – Basic Load Balancer – CLI
titlesuffix: Azure Virtual Network
description: Lär dig hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) med Basic Load Balancer med Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: e6205b4c5428f03459bc75c6fbb2a40458f0f898
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773215"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Distribuera ett IPv6-program med dubbla stackar med Basic Load Balancer – CLI

Den här artikeln visar hur du distribuerar ett program med dubbla stackar (IPv4 + IPv6) med Basic Load Balancer med Azure CLI som innehåller ett virtuellt nätverk med dubbla stackar med ett undernät med dubbla stackar, en Basic Load Balancer med dubbla (IPv4 + IPv6) frontend-konfigurationer, virtuella datorer med nätverkskort som har en dubbel IP-konfiguration, dubbla regler för nätverkssäkerhetsgrupp och dubbla offentliga IP-adresser.

Om du vill distribuera ett program med dubbla stackar (IPV4 + IPv6) med Standard Load Balancer kan du gå till Distribuera ett [dubbelstackprogram för IPv6](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)med Standard Load Balancer med Azure CLI .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.49 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa ditt virtuella nätverk med dubbla stackar måste du skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med *namnet DsResourceGroup01* på *platsen eastus:*

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Skapa offentliga IP-adresser för IPv4 och IPv6 för lastbalanserare
För att få åtkomst till dina IPv4- och IPv6-slutpunkter på Internet behöver du offentliga IP-adresser för IPv4 och IPv6 för lastbalanseraren. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas en offentlig IP-adress för IPv4 och IPv6 med *dsPublicIP_v4* *och dsPublicIP_v6* *i resursgruppen DsResourceGroup01:*

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Skapa offentliga IP-adresser för virtuella datorer

För att fjärransluta till dina virtuella datorer på Internet behöver du offentliga IP-adresser för IPv4 för de virtuella datorerna. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet konfigurerar du dubbla IP-adresser för frontend (IPv4 och IPv6) och backend-adresspoolen för lastbalanseraren och skapar sedan en Grundläggande Load Balancer.

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa Basic Load Balancer med [az network lb create](/cli/azure/network/lb) med namnet **dsLB** som innehåller en frontend-pool med **namnet dsLbFrontEnd_v4**, en backend-pool med namnet **dsLbBackEndPool_v4** som är associerad med den offentliga IP-adress för IPv4 **dsPublicIP_v4** som du skapade i föregående steg. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Skapa IPv6-frontend

Skapa en IP-adress för IPV6-frontend [med az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create). I följande exempel skapas en IP-konfiguration på *serversidan med namnet dsLbFrontEnd_v6* bifogar dsPublicIP_v6 adressen: 

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Konfigurera IPv6-backend-adresspool

Skapa en IPv6-backend-adresspool med [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create). I följande exempel skapas en serveradresspool med *namnet dsLbBackEndPool_v6*  att inkludera virtuella datorer med IPv6 NIC-konfigurationer:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. 

Använd [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att skapa en regel för lastbalanseraren. I följande exempel skapas lastbalanseringsregler med *dsLBrule_v4* *och dsLBrule_v6* och trafiken balanseras på *TCP-port* *80* till IP-konfigurationerna för IPv4 och IPv6 på frontend:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du distribuerar vissa virtuella datorer måste du skapa stödresurser – tillgänglighetsuppsättning, nätverkssäkerhetsgrupp, virtuellt nätverk och virtuella nätverkskort. 
### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning
Du kan förbättra tillgängligheten för din app genom att placera dina virtuella datorer i en tillgänglighetsuppsättning.

Skapa en tillgänglighetsuppsättning med [az vm availability-set create](/cli/azure/vm/availability-set). I följande exempel skapas en tillgänglighetsuppsättning med *namnet dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp för reglerna som styr inkommande och utgående kommunikation i ditt virtuella nätverk.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Skapa en regel för nätverkssäkerhetsgrupp för inkommande och utgående anslutningar

Skapa en regel för nätverkssäkerhetsgruppen som tillåter RDP-anslutningar via port 3389, Internetanslutning via port 80 och för utgående anslutningar med [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). I följande exempel skapas ett virtuellt nätverk med *namnet dsVNET* med *undernät dsSubNET_v4* och *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Skapa nätverkskort

Skapa virtuella nätverkskort för varje virtuell dator med [az network nic create](/cli/azure/network/nic#az_network_nic_create). I följande exempel skapas ett virtuellt nätverkskort för varje virtuell dator. Varje nätverkskort har två IP-konfigurationer (1 IPv4-konfiguration, 1 IPv6-konfiguration). Du skapar IPV6-konfigurationen med [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create).

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns. 

Skapa den virtuella *datorn dsVM0* på följande sätt:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Skapa den virtuella *datorn dsVM1* på följande sätt:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visa virtuella IPv6-nätverk med dubbla stackar i Azure Portal
Du kan visa det virtuella nätverket med dubbla IPv6-stackar Azure Portal följande:
1. I portalens sökfält anger du *dsVnet*.
2. När **myVirtualNetwork** visas i sökresultatet väljer du det. Då startas översiktssidan **för** det virtuella nätverket med dubbla stackar med *namnet dsVnet*. Det virtuella nätverket med dubbla stackar visar de två nätverkskorten med både IPv4- och IPv6-konfigurationer som finns i undernätet med dubbla stackar med *namnet dsSubnet*.

  ![Virtuellt IPv6-nätverk med dubbla staplar i Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en Grundläggande Load Balancer med en dubbel IP-konfiguration för serversidan (IPv4 och IPv6). Du har också skapat två virtuella datorer som innehåller nätverkskort med dubbla IP-konfigurationer (IPV4 + IPv6) som har lagts till i lastbalanseringspoolens serverpool. Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md)
