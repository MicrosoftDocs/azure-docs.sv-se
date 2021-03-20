---
title: Värd för flera webbplatser med CLI
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar en programgateway som är värd för flera webbplatser i Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 350962aed89d04c5508e7b2c50e8a838cd5a7174
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566154"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Skapa en programgateway som är värd för flera webbplatser med hjälp av Azure CLI

Du kan använda Azure CLI till att [konfigurera ett värdskap för flera webbplatser](multiple-site-overview.md) när du skapar en [programgateway](overview.md). I den här artikeln definierar du backend-adresspooler med Virtual Machines Scale Sets. Du konfigurerar sedan lyssnare och regler baserat på de domäner du äger för att kontrollera att webbtrafiken anländer till rätt servrar i poolerna. Den här artikeln förutsätter att du äger flera domäner och använder exempel på *www- \. contoso.com* och *www- \. fabrikam.com*.

I den här artikeln kan du se hur du:

* Konfigurera nätverket
* Skapa en programgateway
* Skapa lyssnare för serverdelen
* Skapa routningsregler
* Skapa VM-skalningsuppsättningar med serverdelspoolerna
* Skapa en CNAME-post i domänen

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="Application Gateway för flera platser":::

Om du vill kan du slutföra den här proceduren med hjälp av [Azure PowerShell](tutorial-multiple-sites-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.4 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med [az group create](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Skapa det virtuella nätverket och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet). Du kan sedan lägga till undernätet som servrarna i serverdelen behöver med [az network vnet subnet create](/cli/azure/network/vnet/subnet). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Du kan använda [az network application-gateway create](/cli/azure/network/application-gateway#az-network-application-gateway-create) till att skapa programgatewayen. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Det kan ta flera minuter att skapa programgatewayen. När programgatewayen har skapats ser du de här nya funktionerna:

- *appGatewayBackendPool* – en programgateway måste ha minst en serverdelsadresspool.
- *appGatewayBackendHttpSettings* – anger att port 80 och ett HTTP-protokoll används för kommunikation.
- *appGatewayHttpListener* – standardlyssnaren som är associerad med *appGatewayBackendPool*.
- *appGatewayFrontendIP* – tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* – standardroutningsregel som är associerad med *appGatewayHttpListener*.

### <a name="add-the-backend-pools"></a>Lägga till serverdelspoolerna

Lägg till de backend-pooler som behövs för att innehålla backend-servrarna med [AZ Network Application-Gateway Address-pool Create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create)
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Lägga till lyssnare

Lägg till lyssnare som behövs för att dirigera trafik med [AZ Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create).

>[!NOTE]
> Med Application Gateway eller WAF v2 SKU kan du också konfigurera upp till 5 värdnamn per lyssnare och du kan använda jokertecken i värd namnet. Mer information finns i [namn på jokertecken i lyssnaren](multiple-site-overview.md#wildcard-host-names-in-listener-preview) .
>Om du vill använda flera värdnamn och jokertecken i en lyssnare med hjälp av Azure CLI måste du använda `--host-names` i stället för `--host-name` . Med värd namn kan du nämna upp till fem värdnamn som blankstegsavgränsad värden. Till exempel `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Lägga till routningsregler

Regler bearbetas i den ordning som de visas. Trafiken dirigeras med den första regeln som matchar oavsett specificitet. Om du till exempel har en regel med en grundläggande lyssnare och en regel med en lyssnare för flera webbplatser för samma port så måste regeln med lyssnare för flera platser stå innan regeln med den grundläggande lyssnaren om regeln för flera platser ska fungera som förväntat. 

I det här exemplet skapar du två nya regler och tar bort standard regeln som skapades när du distribuerade Application Gateway. Du kan lägga till regeln med [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du tre VM-skalningsuppsättningar för de tre serverdelspoolerna i programgatewayen. Skalningsuppsättningarna du skapar har namnen *myvmss1*, *myvmss2* och *myvmss3*. Varje skalningsuppsättning innehåller två virtuella datorinstanser där du installerar IIS.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installera NGINX

```azurecli-interactive
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Hämta den DNS-adressen till programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopiera värdet *fqdn* för DNSSettings och använd det som värde för CNAME-posten du skapar. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Användning av A-poster rekommenderas inte eftersom VIP kan ändras när Application Gateway startar om.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http: \/ /www.contoso.com.

![Testa contoso-webbplatsen i programgatewayen](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Om du ändrar adressen till din andra domän bör du se något som liknar följande exempel:

![Testa fabrikam-webbplatsen i programgatewayen](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Nästa steg

[Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-cli.md)
