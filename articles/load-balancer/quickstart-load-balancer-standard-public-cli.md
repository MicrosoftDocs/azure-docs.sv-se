---
title: 'Snabbstart: Skapa en offentlig lastbalanserare – Azure CLI'
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en offentlig lastbalanserare med Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9b332b18930d58ebb1d155c35a74eed69a90ce73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788789"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snabbstart: Skapa en offentlig lastbalanserare som lastbalanserar virtuella datorer med Azure CLI

Kom igång med Azure Load Balancer hjälp av Azure CLI för att skapa en offentlig lastbalanserare och tre virtuella datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabbstarten kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

* Med **namnet CreatePubLBQS-rg**. 
* På platsen **eastus.**

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-lastbalanserare rekommenderas för produktionsarbetsbelastningar. Mer information om SKU:er finns i **[Azure Load Balancer SKU:er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Standardresurser för lastbalanserare som skapats för snabbstart." border="false":::

## <a name="configure-virtual-network---standard"></a>Konfigurera virtuellt nätverk – Standard

Innan du distribuerar virtuella datorer och testar lastbalanseraren skapar du de stödande virtuella nätverksresurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [az network vnet create:](/cli/azure/network/vnet#az_network_vnet_createt)

* Med **namnet myVNet**.
* Adressprefixet **10.1.0.0/16**.
* Undernät med **namnet myBackendSubnet**.
* Undernätsprefixet **10.1.0.0/24**.
* I **resursgruppen CreatePubLBQS-rg.**
* Platsen **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Använd [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) för att skapa en offentlig IP-adress för skyddsvärden:

* Skapa en redundant offentlig IP-adress i standardzonen med namnet **myBastionIP.**
* I **CCreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Skapa ett skyddsundernät

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett skyddsundernät:

* Med **namnet AzureBastionSubnet**.
* Adressprefixet **10.1.1.0/24**.
* I det virtuella nätverket **myVNet**.
* I resursgruppen **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Skapa skyddsvärd

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en skyddsvärd:

* Med **namnet myBastionHost.**
* I **CreatePubLBQS-rg**.
* Associerad med offentlig IP **myBastionIP**.
* Associerat med det virtuella **nätverket myVNet**.
* På **platsen eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter för den Azure Bastion värden att distribueras.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standardlastbalanserare måste de virtuella datorerna i backend-adressen för ha nätverksgränssnitt som tillhör en nätverkssäkerhetsgrupp. 

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* Med **namnet myNSG.**
* I resursgruppen **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp med [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* Med **namnet myNSGRuleHTTP**.
* MyNSG i nätverkssäkerhetsgruppen som du skapade i **föregående steg.**
* I resursgruppen **CreatePubLBQS-rg**.
* Protokoll **(*)**.
* Riktning **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport **port 80**.
* Åtkomst **Tillåt**.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Skapa backend-servrar – Standard

I det här avsnittet skapar du:

* Tre nätverksgränssnitt för de virtuella datorerna.
* Tre virtuella datorer som ska användas som backend-servrar för lastbalanseraren.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverksgränssnitt för de virtuella datorerna

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create):

* Med **namnet myNicVM1,** **myNicVM2** och **myNicVM3.**
* I resursgruppen **CreatePubLBQS-rg**.
* I det virtuella nätverket **myVNet**.
* I undernätet **myBackendSubnet**.
* I nätverkssäkerhetsgruppen **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>VM1
* Med **namnet myVM1**.
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM1**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Med **namnet myVM2.**
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM2**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Med **namnet myVM3**.
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM3**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
Det kan ta några minuter för de virtuella datorerna att distribueras.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>Skapa en offentlig IP-adress – Standard

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att:

* Skapa en redundant offentlig IP-adress i standardzonen med **namnet myPublicIP.**
* I **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Så här skapar du en zonredundant offentlig IP-adress Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Skapa en standardlastbalanserare

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-adresspool på serversidan som tar emot inkommande nätverkstrafik på lastbalanseraren.
  * En BACKEND-IP-pool där frontend-poolen skickar belastningsutjämnad nätverkstrafik.
  * En hälsoavsökning som fastställer hälsotillståndet för de virtuella datorinstanserna i backend-datorn.
  * En lastbalanseringsregel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en offentlig lastbalanserare med [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Med **namnet myLoadBalancer**.
* En frontend-pool med **namnet myFrontEnd**.
* En backend-pool med **namnet myBackEndPool**.
* Associerad med den offentliga **IP-adressen myPublicIP** som du skapade i föregående steg. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla instanser av virtuella datorer för att säkerställa att de kan skicka nätverkstrafik. 

En virtuell dator med en misslyckad avsökningskontroll tas bort från lastbalanseraren. Den virtuella datorn läggs tillbaka till lastbalanseraren när felet är löst.

Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Övervakar hälsotillståndet för de virtuella datorerna.
* Med **namnet myHealthProbe.**
* **Protokoll-TCP**.
* Övervakning **av port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En lastbalanseringsregel definierar:

* IP-konfiguration på serversidan för inkommande trafik.
* Den IP-adresspool på backend-datorn som ska ta emot trafiken.
* Den nödvändiga käll- och målporten. 

Skapa en lastbalanseringsregel med [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Med **namnet myHTTPRule**
* Lyssnar på **port 80** i frontend-poolen **myFrontEnd**.
* Skicka belastningsutjämnad nätverkstrafik till backend-adresspoolen **myBackEndPool** med **port 80**. 
* Använda hälsoavsökningen **myHealthProbe**.
* **Protokoll-TCP**.
* Tidsgräns för inaktivitet på **15 minuter**.
* Aktivera TCP-återställning.


```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägga till virtuella datorer i lastbalanseringspoolens serverpool

Lägg till de virtuella datorerna i serverpoolen med [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* I backend-adresspoolen **myBackEndPool**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Skapa regelkonfiguration för utgående trafik
Regler för utgående lastbalanserare konfigurerar utgående SNAT för virtuella datorer i backend-poolen. 

Mer information om utgående anslutningar finns i [Utgående anslutningar i Azure](load-balancer-outbound-connections.md).

En offentlig IP-adress eller ett prefix kan användas för den utgående konfigurationen.

### <a name="public-ip"></a>Offentlig IP-adress

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en enskild IP-adress för den utgående anslutningen.  

* Med **namnet myPublicIPOutbound**.
* I **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Så här skapar du en zonredundant offentlig IP-adress Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Offentligt IP-prefix

Använd [az network public-ip prefix create för](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) att skapa ett offentligt IP-prefix för den utgående anslutningen.

* Med **namnet myPublicIPPrefixOutbound**.
* I **CreatePubLBQS-rg**.
* Prefixlängd på **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Så här skapar du ett zonredundant offentligt IP-prefix i Zon 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Mer information om skalning av utgående NAT och utgående anslutningar finns i [Skala utgående NAT med flera IP-adresser.](load-balancer-outbound-connections.md)

### <a name="create-outbound-frontend-ip-configuration"></a>Skapa utgående IP-konfiguration för frontend

Skapa en ny IP-konfiguration för frontend [med az network lb frontend-ip create ](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create):

Välj den offentliga IP-adressen eller det offentliga IP-prefixet baserat på beslutet i föregående steg.

#### <a name="public-ip"></a>Offentlig IP-adress

* Med **namnet myFrontEndOutbound**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associerat med den offentliga **IP-adressen myPublicIPOutbound**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Offentligt IP-prefix

* Med **namnet myFrontEndOutbound**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associerat med det offentliga IP-prefixet **myPublicIPPrefixOutbound**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Skapa en utgående pool

Skapa en ny utgående pool med [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create):

* Med **namnet myBackEndPoolOutbound**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Skapa regel för utgående trafik

Skapa en ny regel för utgående trafik för den utgående backend-poolen [med az network lb outbound-rule create](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create):

* Med **namnet myOutboundRule**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associerat med **lastbalanserare myLoadBalancer**
* Associerat med frontend **myFrontEndOutbound**.
* Protokoll **alla**.
* Tidsgräns för inaktivitet på **15**.
* **10 000** utgående portar.
* Associeras med backend-poolen **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Lägga till virtuella datorer i en utgående pool

Lägg till de virtuella datorerna i den utgående poolen [med az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):


* I backend-adresspoolen **myBackEndPoolOutbound**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-lastbalanserare rekommenderas för produktionsarbetsbelastningar. Mer information om SKU:er finns i **[Azure Load Balancer SKU:er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Resurser för grundläggande lastbalanserare som skapats i snabbstarten." border="false"::: M

## <a name="configure-virtual-network---basic"></a>Konfigurera virtuellt nätverk – Basic

Innan du distribuerar virtuella datorer och testar lastbalanseraren skapar du de virtuella nätverksresurser som är stöd.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Med **namnet myVNet**.
* Adressprefixet **10.1.0.0/16**.
* Undernät med **namnet myBackendSubnet**.
* Undernätsprefixet **10.1.0.0/24**.
* I **resursgruppen CreatePubLBQS-rg.**
* Platsen **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adress för skyddsvärden:

* Skapa en redundant offentlig IP-adress i standardzonen med namnet **myBastionIP.**
* I **CreatePubLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Skapa ett skyddsundernät

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett skyddsundernät:

* Med **namnet AzureBastionSubnet**.
* Adressprefixet **10.1.1.0/24**.
* I det virtuella nätverket **myVNet**.
* I resursgruppen **CreatePubLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Skapa skyddsvärd

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en skyddsvärd:

* Med **namnet myBastionHost**.
* I **CreatePubLBQS-rg**.
* Associerat med offentlig IP **myBastionIP**.
* Associerat med det virtuella nätverket **myVNet**.
* På **platsen eastus.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter för den Azure Bastion värden att distribueras.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standardlastbalanserare måste de virtuella datorerna i backend-adressen för ha nätverksgränssnitt som tillhör en nätverkssäkerhetsgrupp. 

Skapa en nätverkssäkerhetsgrupp med [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create):

* Med **namnet myNSG**.
* I resursgruppen **CreatePubLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp med [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

* Med **namnet myNSGRuleHTTP**.
* MyNSG i nätverkssäkerhetsgruppen som du skapade i **föregående steg.**
* I resursgruppen **CreatePubLBQS-rg**.
* Protokoll **(*)**.
* Riktning **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport **port 80**.
* Åtkomst **Tillåt**.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Skapa backend-servrar – Basic

I det här avsnittet skapar du:

* Tre nätverksgränssnitt för de virtuella datorerna.
* Tillgänglighetsuppsättning för de virtuella datorerna
* Tre virtuella datorer som ska användas som backend-servrar för lastbalanseraren.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverksgränssnitt för de virtuella datorerna

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create):


* Med **namnet myNicVM1,** **myNicVM2** och **myNicVM3.**
* I resursgruppen **CreatePubLBQS-rg**.
* I det virtuella nätverket **myVNet**.
* I undernätet **myBackendSubnet**.
* I nätverkssäkerhetsgruppen **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Skapa en tillgänglighetsuppsättning för virtuella datorer

Skapa tillgänglighetsuppsättningen med [az vm availability-set create:](/cli/azure/vm/availability-set#az_vm_availability_set_create)

* Med **namnet myAvSet**.
* I resursgruppen **CreatePubLBQS-rg**.
* Plats **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create):

### <a name="vm1"></a>VM1
* Med **namnet myVM1**.
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM1**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* Med **namnet myVM2.**
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM2**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* Med **namnet myVM3.**
* I resursgruppen **CreatePubLBQS-rg**.
* Ansluten till **nätverksgränssnittet myNicVM3**.
* Virtuell datoravbildning **win2019datacenter**.
* I **Zon 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
Det kan ta några minuter för de virtuella datorerna att distribueras.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>Skapa en offentlig IP-adress – Grundläggande

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att:

* Skapa en redundant offentlig IP-adress i standardzonen med **namnet myPublicIP.**
* I **CreatePubLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Skapa en grundläggande lastbalanserare

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-adresspool på serversidan som tar emot inkommande nätverkstrafik på lastbalanseraren.
  * En BACKEND-IP-pool där frontend-poolen skickar belastningsutjämnad nätverkstrafik.
  * En hälsoavsökning som fastställer hälsotillståndet för de virtuella datorinstanserna i backend-datorn.
  * En lastbalanseringsregel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en offentlig lastbalanserare med [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Med **namnet myLoadBalancer**.
* En frontend-pool med **namnet myFrontEnd**.
* En backend-pool med **namnet myBackEndPool**.
* Associerad med den offentliga **IP-adressen myPublicIP** som du skapade i föregående steg. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla instanser av virtuella datorer för att säkerställa att de kan skicka nätverkstrafik. 

En virtuell dator med en misslyckad avsökningskontroll tas bort från lastbalanseraren. Den virtuella datorn läggs tillbaka i lastbalanseraren när felet är löst.

Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Övervakar hälsotillståndet för de virtuella datorerna.
* Med **namnet myHealthProbe.**
* **Protokoll-TCP**.
* Övervakning **av port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En lastbalanseringsregel definierar:

* IP-konfiguration på serversidan för inkommande trafik.
* Den IP-adresspool på backend-datorn som ska ta emot trafiken.
* Den nödvändiga käll- och målporten. 

Skapa en lastbalanseringsregel med [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Med **namnet myHTTPRule**
* Lyssnar på **port 80** i frontend-poolen **myFrontEnd**.
* Skicka belastningsutjämnad nätverkstrafik till backend-adresspoolen **myBackEndPool** med **port 80**. 
* Använda hälsoavsökningen **myHealthProbe**.
* **Protokoll-TCP**.
* Tidsgräns för inaktivitet på **15 minuter**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägga till virtuella datorer i lastbalanseringspoolen

Lägg till de virtuella datorerna i serverpoolen med [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add):

* I backend-adresspoolen **myBackEndPool**.
* I resursgruppen **CreatePubLBQS-rg**.
* Associeras med **lastbalanserare myLoadBalancer**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>Installera IIS

Använd [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) för att installera IIS på de virtuella datorerna och ange datornamnet som standardwebbplats.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Hämta lastbalanserarens offentliga IP-adress med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). 

Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testa lastbalanseraren" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver den använder [du kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, lastbalanseraren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten

* Du har skapat en standardlastbalanserare eller en offentlig lastbalanserare
* Anslutna virtuella datorer. 
* Konfigurerade trafikregeln för lastbalanserare och hälsoavsökningen.
* Testade lastbalanseraren.

Om du vill veta mer Azure Load Balancer kan du fortsätta att:
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)