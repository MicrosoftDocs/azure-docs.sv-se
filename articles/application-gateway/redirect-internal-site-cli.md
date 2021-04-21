---
title: Intern omdirigering med CLI
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar en programgateway som omdirigerar intern webbtrafik till lämplig pool med hjälp av Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: c4aa8927422d1f95a642e1d7c383aebc03a4930e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775789"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Skapa en programgateway med intern omdirigering med hjälp av Azure CLI

Du kan använda Azure CLI för att konfigurera [omdirigering av webbtrafik](multiple-site-overview.md) när du skapar en [programgateway.](overview.md) I den här självstudien definierar du en serverpool med hjälp av en VM-skalningsuppsättning. Sedan konfigurerar du lyssnare och regler baserat på domäner som du äger för att se till att webbtrafiken anländer till rätt pool. Den här självstudien förutsätter att du äger flera domäner och använder *exempel på www \. contoso.com* *och www \. contoso.org*.

I den här artikeln kan du se hur du:

* Konfigurera nätverket
* Skapa en programgateway
* Lägga till lyssnare och omdirigeringsregel
* Skapa en VM-skalningsuppsättning med serverpoolen
* Skapa en CNAME-post i domänen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.4 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med [az group create](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet). Du kan sedan lägga till undernätet *myBackendSubnet* som behövs av serverpoolen med [az network vnet subnet create](/cli/azure/network/vnet/subnet). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

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
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du kan använda [az network application-gateway create](/cli/azure/network/application-gateway) till att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
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


## <a name="add-listeners-and-rules"></a>Lägga till lyssnare och regler 

Du behöver en lyssnare så att programgatewayen kan dirigera trafiken till serverdelspoolen på rätt sätt. I den här självstudien skapar du två lyssnare för de två domänerna. I det här exemplet skapas lyssnare för domänerna *www \. contoso.com* *och www \. contoso.org*.

Lägg till lyssnarna för serverdelen som ska dirigera trafiken med [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Lägga till omdirigeringskonfigurationen

Lägg till omdirigeringskonfigurationen som skickar trafik *från www \. consoto.org* till lyssnaren för *www \. contoso.com* i programgatewayen med [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Lägga till routningsregler

Regler bearbetas i den ordning som de skapas och trafiken dirigeras med hjälp av den första regeln som matchar den URL som skickas till programgatewayen. Om du till exempel har en regel med en grundläggande lyssnare och en regel med en lyssnare för flera webbplatser för samma port så måste regeln med lyssnare för flera platser stå innan regeln med den grundläggande lyssnaren om regeln för flera platser ska fungera som förväntat. 

I det här exemplet skapar du två nya regler och tar bort standardregeln som skapades.  Du kan lägga till regeln med [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Skapa VM-skalningsuppsättningar

I det här exemplet skapar du en VM-skalningsuppsättning som stöder serverpoolen som du skapade. Skalningsuppsättningen som du skapar heter *myvmss* och innehåller två instanser av virtuella datorer där du installerar NGINX.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installera NGINX

Kör det här kommandot i gränssnittsfönstret:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>Skapa en CNAME-post i domänen

När du har skapat programgatewayen med dess offentliga IP-adress kan du hämta DNS-adressen och använda den till att skapa en CNAME-post i domänen. Hämta den DNS-adressen till programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Kopiera värdet *fqdn* för DNSSettings och använd det som värde för CNAME-posten du skapar. Du bör inte använda A-poster eftersom den virtuella IP-adressen kan ändras när programgatewayen startas om.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Ange domännamnet i adressfältet i webbläsaren. Till exempel http: \/ /www.contoso.com.

![Testa contoso-webbplatsen i programgatewayen](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Ändra adressen till din andra domän, till exempel http: /www.contoso.org så bör du se att trafiken har omdirigerats tillbaka till \/ lyssnaren för www \. contoso.com.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> * Konfigurera nätverket
> * Skapa en programgateway
> * Lägga till lyssnare och omdirigeringsregel
> * Skapa en VM-skalningsuppsättning med serverpoolen
> * Skapa en CNAME-post i domänen
