---
title: 'Snabb start: skapa en intern belastningsutjämnare – Azure CLI'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en intern belastningsutjämnare med hjälp av Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 10ac477bed97d2a48344aa8ef9b570d2b6203345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101702631"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Snabb start: skapa en intern belastningsutjämnare med hjälp av Azure CLI

Kom igång med Azure Load Balancer med hjälp av Azure CLI för att skapa en intern belastningsutjämnare och tre virtuella datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

>[!NOTE]
>Azure Load Balancer standard är det rekommenderade alternativet för produktions arbets belastningar. Den här artikeln innehåller information om Azure Load Balancer standard, samt Azure Load Balancer Basic. Mer information om SKU: er finns i [Azure Load Balancer SKU: er](skus.md).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resurs grupp är en logisk behållare där du distribuerar och hanterar dina Azure-resurser.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). Ge resurs gruppen namnet **CreateIntLBQS-RG** och ange platsen som **öst**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer standard

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. När du skapar en intern belastningsutjämnare konfigureras ett virtuellt nätverk som nätverk för belastningsutjämnaren. Följande diagram visar de resurser som skapats i den här snabb starten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Standard belastnings Utjämnings resurser har skapats för snabb start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

Innan du distribuerar virtuella datorer och distribuerar belastningsutjämnaren skapar du de stödda virtuella nätverks resurserna.

#### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create). Ange följande:

* Med namnet **myVNet**
* Adressprefix för **10.1.0.0/16**
* Undernät med namnet **myBackendSubnet**
* Undernätsprefixet för **10.1.0.0/24**
* I resurs gruppen **CreateIntLBQS-RG**
* Plats för **öster**

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

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för Azure skydds-värden. Ange följande:

* Skapa en standard zon med redundant offentlig IP-adress med namnet **myBastionIP**
* I **CreateIntLBQS-RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Skapa ett Azure skydds-undernät

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett undernät. Ange följande:

* Med namnet **AzureBastionSubnet**
* Adressprefix för **10.1.1.0/24**
* I virtuellt nätverk **myVNet**
* I resurs gruppen **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Skapa en Azure Bastion-värd

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en värd. Ange följande:

* Med namnet **myBastionHost**
* I **CreateIntLBQS-RG**
* Associerat med offentliga IP- **myBastionIP**
* Associerad med **myVNet** för virtuellt nätverk
* På **östra** platsen

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare kontrollerar du att de virtuella datorerna har nätverks gränssnitt som tillhör en nätverks säkerhets grupp. Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create). Ange följande:

* Med namnet **myNSG**
* I resurs gruppen **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp genom att använda [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Ange följande:

* Med namnet **myNSGRuleHTTP**
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**
* I resurs gruppen **CreateIntLBQS-RG**
* Protokoll **(*)**
* Riktningen **inkommande**
* Källa **(*)**
* Mål **(*)**
* Målport **port 80**
* **Tillåt** åtkomst
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

* Tre nätverks gränssnitt för de virtuella datorerna.
* Tre virtuella datorer som ska användas som servrar för belastningsutjämnaren.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Ange följande:

* Med namnet **myNicVM1**, **myNicVM2** och **myNicVM3**
* I resurs gruppen **CreateIntLBQS-RG**
* I virtuellt nätverk **myVNet**
* I undernät **myBackendSubnet**
* I nätverks säkerhets gruppen **myNSG**

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

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az-vm-create). Ange följande:

* Med namnet **myVM1**, **myVM2** och **myVM3**
* I resurs gruppen **CreateIntLBQS-RG**
* Ansluten till nätverks gränssnittet **myNicVM1**, **myNicVM2** och **myNicVM3**
* **Win2019datacenter** för avbildning av virtuell dator
* I **zon 1**, **zon 2** och **zon 3**

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

Det kan ta några minuter innan de virtuella datorerna distribueras.

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

* En IP-pool som tar emot inkommande nätverks trafik i belastningsutjämnaren.
* En andra IP-pool, där den första poolen skickar den belastningsutjämnade nätverks trafiken.
* En hälso avsökning som avgör hälso tillståndet för VM-instanserna.
* En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

#### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Ange följande:

* Med namnet **myLoadBalancer**
* En pool med namnet ' **frontend** '
* En pool med namnet **myBackEndPool**
* Associerat med det virtuella nätverket **myVNet**
* Associerad med under nätet **myBackendSubnet**

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

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Ange följande:

* Övervakar hälso tillståndet för de virtuella datorerna
* Med namnet **myHealthProbe**
* Protokoll- **TCP**
* Övervaknings **Port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastnings Utjämnings regel definierar:

* IP-konfigurationen för inkommande trafik.
* IP-poolen som ska ta emot trafiken.
* Käll-och mål port som krävs. 

Använd [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) för att skapa en regel för lastbalanseraren. Ange följande:

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i poolen för- **frontend**
* Skicka belastningsutjämnad nätverks trafik till adresspoolen **myBackEndPool** genom att använda **port 80** 
* Använda **myHealthProbe** för hälso avsökning
* Protokoll- **TCP**
* Tids gräns för inaktivitet i **15 minuter**
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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Lägga till virtuella datorer i belastningsutjämnaren

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Ange följande:

* I **myBackEndPool** för adresspool
* I resurs gruppen **CreateIntLBQS-RG**
* Associerad med nätverks gränssnittet **myNicVM1**, **myNicVM2** och **myNicVM3**
* Kopplad till belastningsutjämnare **myLoadBalancer**

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

## <a name="azure-load-balancer-basic"></a>Azure Load Balancer Basic

I det här avsnittet skapar du en belastningsutjämnare som laddar upp virtuella datorer. När du skapar en intern belastningsutjämnare konfigureras ett virtuellt nätverk som nätverk för belastningsutjämnaren. Följande diagram visar de resurser som skapats i den här snabb starten:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Grundläggande belastnings Utjämnings resurser har skapats för snabb start." border="false":::

### <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

Innan du distribuerar virtuella datorer och distribuerar belastningsutjämnaren skapar du de stödda virtuella nätverks resurserna.

#### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-createt). Ange följande:

* Med namnet **myVNet**
* Adressprefix för **10.1.0.0/16**
* Undernät med namnet **myBackendSubnet**
* Undernätsprefixet för **10.1.0.0/24**
* I resurs gruppen **CreateIntLBQS-RG**
* Plats för **öster**

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

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för Azure skydds-värden. Ange följande:

* Skapa en standard zon med redundant offentlig IP-adress med namnet **myBastionIP**
* I **CreateIntLBQS-RG**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Skapa ett Azure skydds-undernät

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett undernät. Ange följande:

* Med namnet **AzureBastionSubnet**
* Adressprefix för **10.1.1.0/24**
* I virtuellt nätverk **myVNet**
* I resurs gruppen **CreateIntLBQS-RG**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Skapa en Azure Bastion-värd

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en värd. Ange följande:

* Med namnet **myBastionHost**
* I **CreateIntLBQS-RG**
* Associerat med offentliga IP- **myBastionIP**
* Associerad med **myVNet** för virtuellt nätverk
* På **östra** platsen

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare kontrollerar du att de virtuella datorerna har nätverks gränssnitt som tillhör en nätverks säkerhets grupp. Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create). Ange följande:

* Med namnet **myNSG**
* I resurs gruppen **CreateIntLBQS-RG**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp genom att använda [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Ange följande:

* Med namnet **myNSGRuleHTTP**
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**
* I resurs gruppen **CreateIntLBQS-RG**
* Protokoll **(*)**
* Riktningen **inkommande**
* Källa **(*)**
* Mål **(*)**
* Målport **port 80**
* **Tillåt** åtkomst
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

* Tre nätverks gränssnitt för de virtuella datorerna.
* Tillgänglighets uppsättningen för de virtuella datorerna.
* Tre virtuella datorer som ska användas som servrar för belastningsutjämnaren.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Ange följande:

* Med namnet **myNicVM1**, **myNicVM2** och **myNicVM3**
* I resurs gruppen **CreateIntLBQS-RG**
* I virtuellt nätverk **myVNet**
* I undernät **myBackendSubnet**
* I nätverks säkerhets gruppen **myNSG**

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Skapa tillgänglighets uppsättning för de virtuella datorerna

Skapa tillgänglighets uppsättningen med [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create). Ange följande:

* Med namnet **myAvailabilitySet**
* I resurs gruppen **CreateIntLBQS-RG**
* **Östra** platser

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Skapa de virtuella datorerna

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az-vm-create). Ange följande:

* Med namnet **myVM1**, **myVM2** och **myVM3**
* I resurs gruppen **CreateIntLBQS-RG**
* Ansluten till nätverks gränssnittet **myNicVM1**, **myNicVM2** och **myNicVM3**
* **Win2019datacenter** för avbildning av virtuell dator
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
Det kan ta några minuter innan de virtuella datorerna distribueras.

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

* En IP-pool som tar emot inkommande nätverks trafik i belastningsutjämnaren.
* En andra IP-pool, där den första poolen skickar den belastningsutjämnade nätverks trafiken.
* En hälso avsökning som avgör hälso tillståndet för VM-instanserna.
* En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

#### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Ange följande:

* Med namnet **myLoadBalancer**
* En pool med namnet ' **frontend** '
* En pool med namnet **myBackEndPool**
* Associerat med det virtuella nätverket **myVNet**
* Associerad med under nätet **myBackendSubnet**

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

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Ange följande:

* Övervakar hälso tillståndet för de virtuella datorerna
* Med namnet **myHealthProbe**
* Protokoll- **TCP**
* Övervaknings **Port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En belastnings Utjämnings regel definierar:

* IP-konfigurationen för inkommande trafik.
* IP-poolen som ska ta emot trafiken.
* Käll-och mål port som krävs. 

Använd [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) för att skapa en regel för lastbalanseraren. Ange följande:

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i poolen för- **frontend**
* Skicka belastningsutjämnad nätverks trafik till adresspoolen **myBackEndPool** genom att använda **port 80** 
* Använda **myHealthProbe** för hälso avsökning
* Protokoll- **TCP**
* Tids gräns för inaktivitet i **15 minuter**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Lägga till virtuella datorer i belastningsutjämnaren

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Ange följande:

* I **myBackEndPool** för adresspool
* I resurs gruppen **CreateIntLBQS-RG**
* Associerad med nätverks gränssnittet **myNicVM1**, **myNicVM2** och **myNicVM3**
* Kopplad till belastningsutjämnare **myLoadBalancer**

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

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Skapa nätverks gränssnittet med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). Ange följande:

* Med namnet **myNicTestVM**
* I resurs gruppen **CreateIntLBQS-RG**
* I virtuellt nätverk **myVNet**
* I undernät **myBackendSubnet**
* I nätverks säkerhets gruppen **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create). Ange följande:

* Med namnet **myTestVM**
* I resurs gruppen **CreateIntLBQS-RG**
* Ansluten till nätverks gränssnittet **myNicTestVM**
* **Win2019Datacenter** för avbildning av virtuell dator

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Du kan behöva vänta några minuter på att den virtuella datorn ska distribueras.

## <a name="install-iis"></a>Installera IIS

Använd [AZ VM Extension](/cli/azure/vm/extension#az_vm_extension_set) för att installera IIS på de virtuella datorerna och ange standard webbplatsen som dator namn.

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

2. På sidan **Översikt** letar du reda på den privata IP-adressen för belastningsutjämnaren. I menyn till vänster väljer du **alla tjänster**  >  **alla resurser**  >  **myLoadBalancer**.

3. I översikten över **myLoadBalancer** kopierar du adressen bredvid **privat IP-adress**.

4. I menyn till vänster väljer du **alla tjänster**  >  **alla resurser**. Välj **myTestVM** i resurs gruppen **CreateIntLBQS-RG** i listan resurser.

5. På sidan **Översikt** väljer du **Anslut**  >  **skydds**.

6. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange IP-adressen från föregående steg i adress fältet i webbläsaren. Standard sidan för IIS-webbservern visas i webbläsaren.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Skärm bild av IP-adressen i webbläsarens Adress fält." border="true":::
   
Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du anpassa standard sidan för varje virtuell dators IIS-webbserver. Uppdatera sedan webbläsaren manuellt från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När resurserna inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Nästa steg

Få en översikt över Azure Load Balancer.
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)
