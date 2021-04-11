---
title: 'Snabb start: skapa en offentlig belastningsutjämnare – Azure CLI'
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
ms.openlocfilehash: 1956d51cbcf962f7a73e970ca2cec858d9ec6579
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056532"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snabbstart: Skapa en offentlig lastbalanserare som lastbalanserar virtuella datorer med Azure CLI

Kom igång med Azure Load Balancer med hjälp av Azure CLI och skapa en offentlig belastningsutjämnare och tre virtuella datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az-group-create):

* Med namnet **CreatePubLBQS-RG**. 
* På den **östra** platsen.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Standard belastnings Utjämnings resurser har skapats för snabb start." border="false":::

## <a name="configure-virtual-network---standard"></a>Konfigurera virtuellt nätverk – standard

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-createt):

* Med namnet **myVNet**.
* Adressprefix för **10.1.0.0/16**.
* Undernät med namnet **myBackendSubnet**.
* Undernätsprefixet för **10.1.0.0/24**.
* I resurs gruppen **CreatePubLBQS-RG** .
* Plats för **öster**.

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

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för skydds-värden:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myBastionIP**.
* I **CCreatePubLBQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Skapa ett skydds-undernät

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett skydds-undernät:

* Med namnet **AzureBastionSubnet**.
* Adressprefix för **10.1.1.0/24**.
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreatePubLBQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Skapa skydds-värd

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en skydds-värd:

* Med namnet **myBastionHost**.
* I **CreatePubLBQS-RG**.
* Associerat med offentliga IP- **myBastionIP**.
* Associerad med **myVNet** för virtuellt nätverk.
* På **östra** platsen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt** åtkomst.
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

## <a name="create-backend-servers---standard"></a>Skapa backend-servrar – standard

I det här avsnittet skapar du:

* Tre nätverks gränssnitt för de virtuella datorerna.
* Tre virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create):

* Med namnet **myNicVM1**, **myNicVM2** och **myNicVM3**.
* I resurs gruppen **CreatePubLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

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

Skapa de virtuella datorerna med [AZ VM Create](/cli/azure/vm#az-vm-create):

### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 1**.

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
* Med namnet **myVM2**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 2**.

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
* Med namnet **myVM3**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM3**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 3**.

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
Det kan ta några minuter innan de virtuella datorerna distribueras.

## <a name="create-a-public-ip-address---standard"></a>Skapa en offentlig IP-adress – standard

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myPublicIP**.
* I **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Så här skapar du en zonindelade redundant offentlig IP-adress i Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Kopplad till den offentliga IP- **myPublicIP** som du skapade i föregående steg. 

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

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.
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
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägg till virtuella datorer i backend-poolen för belastnings utjämning

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

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

## <a name="create-outbound-rule-configuration"></a>Skapa utgående regel konfiguration
Utgående regler för belastningsutjämnare konfigurerar utgående SNAT för virtuella datorer i backend-poolen. 

Mer information om utgående anslutningar finns i [utgående anslutningar i Azure](load-balancer-outbound-connections.md).

En offentlig IP-adress eller ett prefix kan användas för den utgående konfigurationen.

### <a name="public-ip"></a>Offentlig IP-adress

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en enskild IP-adress för utgående anslutningar.  

* Med namnet **myPublicIPOutbound**.
* I **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Så här skapar du en zonindelade redundant offentlig IP-adress i Zon 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Offentligt IP-prefix

Använd [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) för att skapa ett offentligt IP-prefix för den utgående anslutningen.

* Med namnet **myPublicIPPrefixOutbound**.
* I **CreatePubLBQS-RG**.
* Prefixlängden på **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Så här skapar du ett zonindelade redundant offentlig IP-prefix i Zon 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Mer information om skalning av utgående NAT och utgående anslutningar finns i [skala utgående NAT med flera IP-adresser](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Skapa utgående IP-konfiguration för klient delen

Skapa en ny IP-konfiguration för klient delen med [AZ Network lb frontend-IP Create ](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create):

Välj kommandona offentlig IP-adress eller offentlig IP-prefix baserat på beslut i föregående steg.

#### <a name="public-ip"></a>Offentlig IP-adress

* Med namnet **myFrontEndOutbound**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Associerat med offentliga IP- **myPublicIPOutbound**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Offentligt IP-prefix

* Med namnet **myFrontEndOutbound**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Associerat med det offentliga IP-prefixet **myPublicIPPrefixOutbound**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Skapa utgående pool

Skapa en ny utgående pool med [AZ Network lb Address-pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create):

* Med namnet **myBackEndPoolOutbound**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Skapa utgående regel

Skapa en ny utgående regel för den utgående backend-poolen med [AZ Network lb utgående-Rule Create](/cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-create):

* Med namnet **myOutboundRule**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Kopplad till belastningsutjämnare **myLoadBalancer**
* Associerad med frontend- **myFrontEndOutbound**.
* Protokoll **alla**.
* Tids gräns för inaktivitet på **15**.
* **10000** utgående portar.
* Kopplad till **myBackEndPoolOutbound** i Server delen.

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
### <a name="add-virtual-machines-to-outbound-pool"></a>Lägg till virtuella datorer i utgående pool

Lägg till de virtuella datorerna i den utgående poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):


* I backend-adresspoolen **myBackEndPoolOutbound**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

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
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Grundläggande belastnings Utjämnings resurser skapade i snabb starten." border="false"::: avstånd

## <a name="configure-virtual-network---basic"></a>Konfigurera virtuellt nätverk – grundläggande

Innan du distribuerar virtuella datorer och testar belastningsutjämnaren, skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create):

* Med namnet **myVNet**.
* Adressprefix för **10.1.0.0/16**.
* Undernät med namnet **myBackendSubnet**.
* Undernätsprefixet för **10.1.0.0/24**.
* I resurs gruppen **CreatePubLBQS-RG** .
* Plats för **öster**.

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

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) för att skapa en offentlig IP-adress för skydds-värden:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myBastionIP**.
* I **CreatePubLBQS-RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Skapa ett skydds-undernät

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) för att skapa ett skydds-undernät:

* Med namnet **AzureBastionSubnet**.
* Adressprefix för **10.1.1.0/24**.
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreatePubLBQS-RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Skapa skydds-värd

Använd [AZ Network skydds Create](/cli/azure/network/bastion#az-network-bastion-create) för att skapa en skydds-värd:

* Med namnet **myBastionHost**.
* I **CreatePubLBQS-RG**.
* Associerat med offentliga IP- **myBastionIP**.
* Associerad med **myVNet** för virtuellt nätverk.
* På **östra** platsen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **CreatePubLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt** åtkomst.
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

* Tre nätverks gränssnitt för de virtuella datorerna.
* Tillgänglighets uppsättning för de virtuella datorerna
* Tre virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa tre nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create):


* Med namnet **myNicVM1**, **myNicVM2** och **myNicVM3**.
* I resurs gruppen **CreatePubLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

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
### <a name="create-availability-set-for-virtual-machines"></a>Skapa tillgänglighets uppsättning för virtuella datorer

Skapa tillgänglighets uppsättningen med [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create):

* Med namnet **myAvSet**.
* I resurs gruppen **CreatePubLBQS-RG**.
* **Östra** platser.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [AZ VM Create](/cli/azure/vm#az-vm-create):

### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 1**.

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
* Med namnet **myVM2**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 2**.

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
* Med namnet **myVM3**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM3**.
* **Win2019datacenter** för avbildning av virtuell dator.
* I **zon 3**.

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
Det kan ta några minuter innan de virtuella datorerna distribueras.

## <a name="create-a-public-ip-address---basic"></a>Skapa en offentlig IP-adress – grundläggande

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. 

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) to:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myPublicIP**.
* I **CreatePubLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Skapa Basic Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Kopplad till den offentliga IP- **myPublicIP** som du skapade i föregående steg. 

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

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.

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

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägg till virtuella datorer i backend-poolen för belastnings utjämning

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add):

* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreatePubLBQS-RG**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

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
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Hämta lastbalanserarens offentliga IP-adress med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

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

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten

* Du har skapat en standard-eller offentlig belastningsutjämnare
* Anslutna virtuella datorer. 
* Konfigurerat trafik regel för belastnings utjämning och hälso avsökning.
* Belastnings utjämning har testats.

Om du vill veta mer om Azure Load Balancer fortsätter du till:
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)