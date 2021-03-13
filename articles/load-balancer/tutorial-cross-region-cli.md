---
title: 'Självstudie: skapa en belastningsutjämnare för flera regioner med Azure CLI'
titleSuffix: Azure Load Balancer
description: Kom igång med den här självstudien för att distribuera en Azure Load Balancer över flera regioner med Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225191"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Självstudie: skapa en Azure Load Balancer över flera regioner med Azure CLI

En belastningsutjämnare för flera regioner säkerställer att en tjänst är tillgänglig globalt över flera Azure-regioner. Om en region Miss lyckas dirigeras trafiken till nästa närmaste felfria regionala belastnings utjämning.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en belastningsutjämnare för flera regioner.
> * Skapa en lastbalanseringsregel.
> * Skapa en backend-pool som innehåller två regionala belastningsutjämnare.
> * Testa belastningsutjämnaren.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- Två **standardiserade** SKU: er i Azure med backend-pooler distribuerade i två olika Azure-regioner.
    - Information om hur du skapar en regional standard belastningsutjämnare och virtuella datorer för backend-pooler finns i [snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure CLI](quickstart-load-balancer-standard-public-cli.md).
        - Lägg till namnet på belastningsutjämnaren och virtuella datorer i varje region med a **-R1** och **-R2**. 
- Azure CLI installerat lokalt eller Azure Cloud Shell.

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på Azure CLI:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Skapa en belastningsutjämnare för flera regioner

I det här avsnittet ska du skapa en belastningsutjämnare för flera regioner, offentlig IP-adress och belastnings Utjämnings regel.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az-group-create):

* Med namnet **myResourceGroupLB – CR**.
* På den **västra** platsen.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en belastningsutjämnare för flera regioner med [AZ Network Cross-region – lb Create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Med namnet **myLoadBalancer – CR**.
* En frontend-pool med namnet min **frontend-CR**.
* En backend-pool med namnet **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network Cross-region-lb Rule Create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Med namnet **myHTTPRule – CR**
* Lyssnar på **Port 80** i frontend-poolens klient del **-CR**.
* Skickar belastningsutjämnad nätverks trafik till backend-adresspoolen **myBackEndPool-CR** med **port 80**. 
* Protokoll- **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Skapa serverdelspool

I det här avsnittet ska du lägga till två regionala standard belastningsutjämnare till backend-poolen för belastningsutjämnaren över flera regioner.

> [!IMPORTANT]
> För att slutföra de här stegen ser du till att två regionala belastningsutjämnare med backend-pooler har distribuerats i din prenumeration.  Mer information finns i, **[snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure CLI](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>Lägg till de regionala klient-frontend-fälten i belastningsutjämnaren

I det här avsnittet ska du placera resurs-ID: n för två regionala belastnings Utjämnings-frontend i variabler.  Sedan använder du variablerna för att lägga till frontend-klienterna i backend-adresspoolen för belastningsutjämnaren mellan regioner.

Hämta resurs-ID: n med [AZ Network lb frontend-IP show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Använd [AZ Network Cross-region-lb Address-pool Address Add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) för att lägga till de klient delar som du placerade i variabler i backend-poolen för över-regionens belastningsutjämnare:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet ska du testa belastningsutjämnaren för flera regioner. Du ansluter till den offentliga IP-adressen i en webbläsare.  Du kommer att stoppa de virtuella datorerna i en av de regionala Server dels poolerna för belastnings utjämning och kontrol lera redundansväxlingen.

1. Hämta den offentliga IP-adressen för belastningsutjämnaren med [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

3. Stoppa de virtuella datorerna i backend-poolen för en av de regionala belastnings utjämningen.

4. Uppdatera webbläsaren och kontrol lera att anslutningen till den andra regionala belastningsutjämnaren är redundansväxling.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* En belastningsutjämnare för flera regioner har skapats.
* En regel för belastnings utjämning har skapats.
* Regionala belastningsutjämnare har lagts till i backend-poolen för belastningsutjämnaren i flera regioner.
* Belastnings utjämning har testats.

Gå vidare till nästa artikel om du vill lära dig hur du...
> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
