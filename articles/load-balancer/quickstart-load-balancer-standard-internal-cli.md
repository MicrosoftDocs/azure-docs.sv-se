---
title: 'Snabbstart: Skapa en intern lastbalanserare – Azure CLI'
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en intern lastbalanserare med hjälp av Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788843"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Snabbstart: Skapa en intern lastbalanserare med hjälp av Azure CLI

Kom igång med Azure Load Balancer hjälp av Azure CLI för att skapa en intern lastbalanserare och tre virtuella datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Den här snabbstarten kräver version 2.0.28 eller senare av Azure CLI. Om du använder en Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där du distribuerar och hanterar dina Azure-resurser.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). Ge resursgruppen namnet **CreateIntLBQS-rg** och ange platsen **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-lastbalanserare rekommenderas för produktionsarbetsbelastningar. Mer information om SKU:er finns i **[Azure Load Balancer SKU:er](skus.md)**.

I det här avsnittet skapar du en lastbalanserare som lastbalanserar virtuella datorer. När du skapar en intern lastbalanserare konfigureras ett virtuellt nätverk som lastbalanserares nätverk. Följande diagram visar de resurser som skapades i den här snabbstarten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Resurser för standardlastbalanserare som skapats för snabbstart." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

Innan du distribuerar virtuella datorer och distribuerar lastbalanserare skapar du de virtuella nätverksresurser som du stöder.

#### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Ange följande:

* Med **namnet myVNet**
* Adressprefixet **10.1.0.0/16**
* Undernät med **namnet myBackendSubnet**
* Undernätsprefixet **10.1.0.0/24**
* I **resursgruppen CreateIntLBQS-rg**
* Platsen **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adress för Azure Bastion värden. Ange följande:

* Skapa en standardzonredundant offentlig IP-adress med namnet **myBastionIP**
* I **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Skapa ett Azure Bastion undernät

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett undernät. Ange följande:

* Med **namnet AzureBastionSubnet**
* Adressprefixet **10.1.1.0/24**
* I det virtuella nätverket **myVNet**
* I resursgruppen **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Skapa en Azure Bastion-värd

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en värd. Ange följande:

* Med **namnet myBastionHost**
* I **CreateIntLBQS-rg**
* Associerad med offentlig IP **myBastionIP**
* Associerat med det virtuella nätverket **myVNet**
* På **platsen eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter för den Azure Bastion värden att distribueras.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standardlastbalanserare ser du till att dina virtuella datorer har nätverksgränssnitt som tillhör en nätverkssäkerhetsgrupp. Skapa en nätverkssäkerhetsgrupp med az [network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Ange följande:

* Med **namnet myNSG**
* I resursgruppen **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgruppen med [hjälp av az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Ange följande:

* Med **namnet myNSGRuleHTTP**
* I nätverkssäkerhetsgruppen som du skapade i föregående steg **myNSG**
* I resursgruppen **CreateIntLBQS-rg**
* Protokoll **(*)**
* Riktning **inkommande**
* Källa **(*)**
* Mål **(*)**
* Målport **port 80**
* Tillåt **åtkomst**
* Prioritet **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* Tre nätverksgränssnitt för de virtuella datorerna.
* Tre virtuella datorer som ska användas som servrar för lastbalanserare.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverksgränssnitt för de virtuella datorerna

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create). Ange följande:

* Med **namnet myNicVM1,** **myNicVM2** och **myNicVM3**
* I resursgruppen **CreateIntLBQS-rg**
* I det virtuella nätverket **myVNet**
* I undernätet **myBackendSubnet**
* I nätverkssäkerhetsgruppen **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>Skapa de virtuella datorerna

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create). Ange följande:

* Med **namnet myVM1,** **myVM2** och **myVM3**
* I resursgruppen **CreateIntLBQS-rg**
* Ansluten till **nätverksgränssnittet myNicVM1,** **myNicVM2** och **myNicVM3**
* Virtuell datoravbildning **win2019datacenter**
* I **Zon 1**, **Zon 2** och **Zon 3**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Det kan ta några minuter för de virtuella datorerna att distribueras.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

* En IP-pool som tar emot inkommande nätverkstrafik på lastbalanseraren.
* En andra IP-pool, där den första poolen skickar den belastningsutjämnade nätverkstrafiken.
* En hälsoavsökning som fastställer hälsotillståndet för de virtuella datorinstanserna.
* En lastbalanseringsregel som definierar hur trafiken distribueras till de virtuella datorerna.

#### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en offentlig lastbalanserare med [az network lb create](/cli/azure/network/lb#az_network_lb_create). Ange följande:

* Med **namnet myLoadBalancer**
* En pool med **namnet myFrontEnd**
* En pool med **namnet myBackEndPool**
* Associerat med det virtuella nätverket **myVNet**
* Associerat med undernätet **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla instanser av virtuella datorer för att säkerställa att de kan skicka nätverkstrafik. En virtuell dator med en misslyckad avsökningskontroll tas bort från lastbalanseraren. Den virtuella datorn läggs tillbaka till lastbalanseraren när felet är löst.

Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Ange följande:

* Övervakar hälsotillståndet för de virtuella datorerna
* Med **namnet myHealthProbe**
* **Protokoll-TCP**
* Övervaka **port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar:

* IP-konfigurationen för inkommande trafik.
* IP-poolen som ska ta emot trafiken.
* Käll- och målporten som krävs. 

Använd [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att skapa en regel för lastbalanseraren. Ange följande:

* Med **namnet myHTTPRule**
* Lyssna på **port 80** i poolen **myFrontEnd**
* Skicka belastningsutjämnad nätverkstrafik till **adresspoolen myBackEndPool** med **port 80** 
* Använda hälsoavsökningen **myHealthProbe**
* **Protokoll-TCP**
* Tidsgräns för inaktivitet på **15 minuter**
* Aktivera TCP-återställning

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Lägga till virtuella datorer i lastbalanseringspoolen

Lägg till de virtuella datorerna i serverpoolen med [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Ange följande:

* I **adresspoolen myBackEndPool**
* I resursgruppen **CreateIntLBQS-rg**
* Associerat med **nätverksgränssnittet myNicVM1,** **myNicVM2** och **myNicVM3**
* Associerat med **lastbalanserare myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-lastbalanserare rekommenderas för produktionsarbetsbelastningar. Mer information om SKU:er finns i **[Azure Load Balancer SKU:er](skus.md)**.

I det här avsnittet skapar du en lastbalanserare som lastbalanserar virtuella datorer. När du skapar en intern lastbalanserare konfigureras ett virtuellt nätverk som nätverk för lastbalanseraren. Följande diagram visar de resurser som skapades i den här snabbstarten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Resurser för grundläggande lastbalanserare som skapats för snabbstart." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

Innan du distribuerar virtuella datorer och distribuerar lastbalanseraren skapar du de stödande virtuella nätverksresurserna.

#### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [hjälp av az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt). Ange följande:

* Med **namnet myVNet**
* Adressprefixet **10.1.0.0/16**
* Undernät med **namnet myBackendSubnet**
* Undernätsprefixet **10.1.0.0/24**
* I **resursgruppen CreateIntLBQS-rg**
* Platsen **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Använd [az network public-ip create för](/cli/azure/network/public-ip#az_network_public_ip_create) att skapa en offentlig IP-adress för Azure Bastion värden. Ange följande:

* Skapa en standardzonredundant offentlig IP-adress med namnet **myBastionIP**
* I **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Skapa ett Azure Bastion undernät

Använd [az network vnet subnet create för](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) att skapa ett undernät. Ange följande:

* Med **namnet AzureBastionSubnet**
* Adressprefixet **10.1.1.0/24**
* I det virtuella nätverket **myVNet**
* I resursgruppen **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Skapa en Azure Bastion-värd

Använd [az network bastion create för](/cli/azure/network/bastion#az_network_bastion_create) att skapa en värd. Ange följande:

* Med **namnet myBastionHost**
* I **CreateIntLBQS-rg**
* Associerad med offentlig IP **myBastionIP**
* Associerat med det virtuella nätverket **myVNet**
* På **platsen eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter för den Azure Bastion värden att distribueras.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standardlastbalanserare ser du till att dina virtuella datorer har nätverksgränssnitt som tillhör en nätverkssäkerhetsgrupp. Skapa en nätverkssäkerhetsgrupp med az [network nsg create](/cli/azure/network/nsg#az_network_nsg_create). Ange följande:

* Med **namnet myNSG**
* I resursgruppen **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp med [hjälp av az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Ange följande:

* Namngiven **myNSGRuleHTTP**
* MyNSG i den nätverkssäkerhetsgrupp som du skapade i **föregående steg**
* I resursgruppen **CreateIntLBQS-rg**
* Protokoll **(*)**
* Riktning **inkommande**
* Källa **(*)**
* Mål **(*)**
* Målport **port 80**
* Tillåt **åtkomst**
* Prioritet **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* Tre nätverksgränssnitt för de virtuella datorerna.
* Tillgänglighetsuppsättningen för de virtuella datorerna.
* Tre virtuella datorer som ska användas som servrar för lastbalanseraren.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverksgränssnitt för de virtuella datorerna

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create). Ange följande:

* Med **namnet myNicVM1,** **myNicVM2** och **myNicVM3**
* I resursgruppen **CreateIntLBQS-rg**
* I det virtuella nätverket **myVNet**
* I undernätet **myBackendSubnet**
* I nätverkssäkerhetsgruppen **myNSG**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Skapa tillgänglighetsuppsättningen för de virtuella datorerna

Skapa tillgänglighetsuppsättningen med [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). Ange följande:

* Med **namnet myAvailabilitySet**
* I resursgruppen **CreateIntLBQS-rg**
* Plats **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Skapa de virtuella datorerna

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create). Ange följande:

* Med **namnet myVM1,** **myVM2** och **myVM3**
* I resursgruppen **CreateIntLBQS-rg**
* Ansluten till **nätverksgränssnittet myNicVM1,** **myNicVM2** och **myNicVM3**
* Virtuell datoravbildning **win2019datacenter**
* I **myAvailabilitySet**


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Det kan ta några minuter för de virtuella datorerna att distribueras.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

* En IP-pool som tar emot inkommande nätverkstrafik på lastbalanseraren.
* En andra IP-pool, där den första poolen skickar den belastningsutjämnade nätverkstrafiken.
* En hälsoavsökning som fastställer hälsotillståndet för de virtuella datorinstanserna.
* En lastbalanseringsregel som definierar hur trafiken distribueras till de virtuella datorerna.

#### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en offentlig lastbalanserare med [az network lb create](/cli/azure/network/lb#az_network_lb_create). Ange följande:

* Med **namnet myLoadBalancer**
* En pool med **namnet myFrontEnd**
* En pool med namnet **myBackEndPool**
* Associerat med det virtuella nätverket **myVNet**
* Associerat med undernätet **myBackendSubnet**

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla instanser av virtuella datorer för att säkerställa att de kan skicka nätverkstrafik. En virtuell dator med en misslyckad avsökningskontroll tas bort från lastbalanseraren. Den virtuella datorn läggs tillbaka till lastbalanseraren när felet är löst.

Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). Ange följande:

* Övervakar hälsotillståndet för de virtuella datorerna
* Med **namnet myHealthProbe**
* **Protokoll-TCP**
* Övervaka **port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar:

* IP-konfigurationen för inkommande trafik.
* IP-poolen som ska ta emot trafiken.
* Käll- och målporten som krävs. 

Använd [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att skapa en regel för lastbalanseraren. Ange följande:

* Med **namnet myHTTPRule**
* Lyssna på **port 80** i poolen **myFrontEnd**
* Skicka belastningsutjämnad nätverkstrafik till **adresspoolen myBackEndPool** med **port 80** 
* Använda hälsoavsökningen **myHealthProbe**
* **Protokoll-TCP**
* Tidsgräns för inaktivitet på **15 minuter**

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Lägga till virtuella datorer i lastbalanseringspoolen

Lägg till de virtuella datorerna i serverpoolen med [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add). Ange följande:

* I **adresspoolen myBackEndPool**
* I resursgruppen **CreateIntLBQS-rg**
* Associerat med **nätverksgränssnittet myNicVM1,** **myNicVM2** och **myNicVM3**
* Associerat med **lastbalanserare myLoadBalancer**

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Skapa nätverksgränssnittet med [az network nic create](/cli/azure/network/nic#az_network_nic_create). Ange följande:

* Med **namnet myNicTestVM**
* I resursgruppen **CreateIntLBQS-rg**
* I det virtuella nätverket **myVNet**
* I undernätet **myBackendSubnet**
* I nätverkssäkerhetsgruppen **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Skapa den virtuella datorn med [az vm create](/cli/azure/vm#az_vm_create). Ange följande:

* Med **namnet myTestVM**
* I resursgruppen **CreateIntLBQS-rg**
* Ansluten till **nätverksgränssnittet myNicTestVM**
* Avbildning av virtuell **dator Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Du kan behöva vänta några minuter tills den virtuella datorn har distribuerats.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Test

1. [Logga in](https://portal.azure.com) i Azure-portalen.

2. På sidan **Översikt** hittar du den privata IP-adressen för lastbalanseraren. I menyn till vänster väljer du Alla **tjänster Alla**  >  **resurser**  >  **myLoadBalancer**.

3. I översikten över **myLoadBalancer** kopierar du adressen bredvid Privat **IP-adress**.

4. I menyn till vänster väljer du **Alla tjänster**  >  **Alla resurser**. Välj **myTestVM** i **resursgruppen CreateIntLBQS-rg** i resurslistan.

5. På sidan **Översikt** väljer du **Anslut**  >  **Bastion**.

6. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

7. På **myTestVM** öppnar du **Internet Explorer**.

8. Ange IP-adressen från föregående steg i webbläsarens adressfält. Standardsidan för IIS-webbservern visas i webbläsaren.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Skärmbild av IP-adressen i adressfältet i webbläsaren." border="true":::
   
Om du vill se hur lastbalanserare distribuerar trafik över alla tre virtuella datorer kan du anpassa standardsidan för varje virtuell dators IIS-webbserver. Uppdatera sedan webbläsaren manuellt från klientdatorn.

## <a name="clean-up-resources"></a>Rensa resurser

När dina resurser inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, lastbalanseraren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Nästa steg

Få en översikt över Azure Load Balancer.
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)
