---
title: 'Självstudie: Skapa en lastbalanserare mellan regioner med Azure CLI'
titleSuffix: Azure Load Balancer
description: Kom igång med den här självstudiekursen om hur du distribuerar en Azure Load Balancer med hjälp av Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791921"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Självstudie: Skapa en Azure Load Balancer med Azure CLI

En lastbalanserare mellan regioner säkerställer att en tjänst är tillgänglig globalt i flera Azure-regioner. Om en region misslyckas dirigeras trafiken till nästa närmaste felfria regionala lastbalanserare.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa lastbalanserare mellan regioner.
> * Skapa en lastbalanseringsregel.
> * Skapa en backend-pool som innehåller två regionala lastbalanserare.
> * Testa lastbalanseraren.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- Två  Standard-SKU:er för Azure Load Balancers med backend-pooler distribuerade i två olika Azure-regioner.
    - Information om hur du skapar en regional standardlastbalanserare och virtuella datorer för serverpooler finns i Snabbstart: Skapa en offentlig [lastbalanserare](quickstart-load-balancer-standard-public-cli.md)för att belastningsutjämna virtuella datorer med Azure CLI.
        - Lägg till namnet på lastbalanserarna och de virtuella datorerna i varje region med **-R1** och **-R2.** 
- Azure CLI installerat lokalt eller Azure Cloud Shell.

Om du väljer att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Logga in på Azure CLI:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Skapa lastbalanserare mellan regioner

I det här avsnittet skapar du en lastbalanserare mellan regioner, en offentlig IP-adress och en lastbalanseringsregel.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

* Med **namnet myResourceGroupLB-CR.**
* På platsen **usa, västra.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en lastbalanserare mellan regioner med [az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* Med **namnet myLoadBalancer-CR**.
* En frontend-pool med **namnet myFrontEnd-CR**.
* En backend-pool med **namnet myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En lastbalanseringsregel definierar:

* Frontend-IP-konfiguration för inkommande trafik.
* Backend-IP-poolen som ska ta emot trafiken.
* Käll- och målporten som krävs. 

Skapa en lastbalanseringsregel [med az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Med **namnet myHTTPRule-CR**
* Lyssnar på **port 80** i frontend-poolen **myFrontEnd-CR**.
* Skicka belastningsutjämnad nätverkstrafik till backend-adresspoolen **myBackEndPool-CR** med **port 80**. 
* Protokoll **TCP**.

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

I det här avsnittet lägger du till två regionala standardlastbalanserare i backend-poolen för lastbalanserare mellan regioner.

> [!IMPORTANT]
> För att slutföra de här stegen ser du till att två regionala lastbalanserare med backend-pooler har distribuerats i din prenumeration.  Mer information finns i **[Snabbstart: Skapa en offentlig lastbalanserare för att lastbalansera virtuella datorer med Azure CLI.](quickstart-load-balancer-standard-public-cli.md)**

### <a name="add-the-regional-frontends-to-load-balancer"></a>Lägga till regionala frontends i lastbalanserare

I det här avsnittet placerar du resurs-ID:erna för två regionala lastbalanserare i variabler.  Sedan använder du variablerna för att lägga till frontends i backend-adresspoolen för lastbalanseraren mellan regioner.

Hämta resurs-IP-adresserna [med az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Använd [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) för att lägga till de frontends som du placerade i variabler i backend-poolen för lastbalanseraren mellan regioner:

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

I det här avsnittet testar du lastbalanseraren mellan regioner. Du ansluter till den offentliga IP-adressen i en webbläsare.  Du stoppar de virtuella datorerna i någon av de regionala serverpoolerna för lastbalanserare och observerar redundansen.

1. Hämta den offentliga IP-adressen för lastbalanseraren med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

3. Stoppa de virtuella datorerna i serverpoolen för en av de regionala lastbalanserarna.

4. Uppdatera webbläsaren och observera redundans för anslutningen till den andra regionala lastbalanseraren.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs använder du [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, lastbalanseraren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* Skapat en lastbalanserare mellan regioner.
* Skapat en belastningsutjämningsregel.
* Regionala lastbalanserare har lagts till i backend-poolen för lastbalanseraren mellan regioner.
* Testade lastbalanseraren.

Gå vidare till nästa artikel om du vill lära dig hur du...
> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
