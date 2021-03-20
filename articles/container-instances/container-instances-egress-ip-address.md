---
title: Konfigurera statisk utgående IP
description: Konfigurera Azure-brandväggen och användardefinierade vägar för Azure Container Instances arbets belastningar som använder brand väggens offentliga IP-adress för ingående och utgående
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89566576"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Konfigurera en enskild offentlig IP-adress för utgående och inkommande trafik till en behållar grupp

Genom att konfigurera en [behållar grupp](container-instances-container-groups.md) med en extern IP-adress kan externa klienter använda IP-adressen för att få åtkomst till en behållare i gruppen. En webbläsare kan till exempel komma åt en webbapp som körs i en behållare. Men för närvarande använder en behållar grupp en annan IP-adress för utgående trafik. Den här utgående IP-adressen exponeras inte program mässigt, vilket gör det lättare att övervaka och konfigurera klient brand Väggs reglerna.

Den här artikeln innehåller steg för att konfigurera en behållar grupp i ett [virtuellt nätverk](container-instances-virtual-network-concepts.md) som är integrerat med [Azure-brandväggen](../firewall/overview.md). Genom att konfigurera en användardefinierad väg till behållar gruppen och brand Väggs reglerna kan du dirigera och identifiera trafik till och från behållar gruppen. Container grupp ingångs-och utgångs användning använder brand väggens offentliga IP-adress. En enda utgående IP-adress kan användas av flera behållar grupper som distribueras i det virtuella nätverkets undernät delegerade till Azure Container Instances.

I den här artikeln använder du Azure CLI för att skapa resurser för det här scenariot:

* Behållar grupper som har distribuerats i ett delegerat undernät [i det virtuella nätverket](container-instances-vnet.md) 
* En Azure-brandvägg distribuerad i nätverket med en statisk offentlig IP-adress
* En användardefinierad väg i under nätet för behållar grupper
* En NAT-regel för brand Väggs ingångar och en program regel för utgående

Sedan validerar du ingångs-och utgångs exempel för container grupper genom brand väggen.

## <a name="deploy-aci-in-a-virtual-network"></a>Distribuera ACI i ett virtuellt nätverk

I ett typiskt fall kanske du redan har ett virtuellt Azure-nätverk där du kan distribuera en behållar grupp. I demonstrations syfte skapar följande kommandon ett virtuellt nätverk och undernät när behållar gruppen skapas. Under nätet delegeras till Azure Container Instances. 

Behållar gruppen kör en liten webbapp från `aci-helloworld` avbildningen. Som du ser i andra artiklar i dokumentationen packar den här avbildningen en liten webbapp som skrivits i Node.js som hanterar en statisk HTML-sida.

Om du behöver ett måste du först skapa en Azure-resurs grupp med kommandot [AZ Group Create][az-group-create] . Exempel:

```azurecli
az group create --name myResourceGroup --location eastus
```

För att förenkla följande kommando exempel använder du en miljö variabel för resurs gruppens namn:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Skapa behållar gruppen med kommandot [AZ container Create][az-container-create] :

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Justera värdet för `--subnet address-prefix` det IP-adressutrymme du behöver i ditt undernät. Det minsta under nätet som stöds är/29, vilket ger åtta IP-adresser. Vissa IP-adresser är reserverade för användning av Azure.

För användning i ett senare steg hämtar du den privata IP-adressen för behållar gruppen genom att köra kommandot [AZ container show] [AZ-container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Distribuera Azure-brandväggen i nätverket

I följande avsnitt använder du Azure CLI för att distribuera en Azure-brandvägg i det virtuella nätverket. I bakgrunden, se [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](../firewall/deploy-cli.md).

Använd först [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] för att lägga till ett undernät med namnet AzureFirewallSubnet för brand väggen. AzureFirewallSubnet är namnet på det här under nätet som *krävs* .

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Använd följande [Azure CLI-kommandon](../firewall/deploy-cli.md) för att skapa en brand vägg i under nätet.

Om du inte redan har installerat lägger du till brand Väggs tillägget i Azure CLI med hjälp av kommandot [AZ-tillägg Lägg till][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

Skapa brand Väggs resurser:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Uppdatera brand Väggs konfigurationen med hjälp av kommandot [AZ Network Firewall Update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Hämta brand väggens privata IP-adress med hjälp av kommandot [AZ Network Firewall IP-config List][az-network-firewall-ip-config-list] . Den här privata IP-adressen används i ett senare kommando.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Hämta brand väggens offentliga IP-adress med kommandot [AZ Network Public-IP show][az-network-public-ip-show] . Den här offentliga IP-adressen används i ett senare kommando.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definiera användardefinierad väg i ACI-undernätet

Definiera en Använd-definierad väg i ACI-undernätet för att vidarebefordra trafik till Azure-brandväggen. Mer information finns i [dirigera nätverks trafik](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Skapa routningstabell

Kör först följande [AZ-kommando för nätverks väg-Table Create][az-network-route-table-create] för att skapa routningstabellen. Skapa routningstabellen i samma region som det virtuella nätverket.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Skapa väg

Kör [AZ Network-Route-Table Route Create][az-network-route-table-route-create] för att skapa en väg i routningstabellen. Om du vill dirigera trafik till brand väggen anger du nästa hopp typ till `VirtualAppliance` och skickar brand väggens privata IP-adress som nästa hopp adress.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Koppla routningstabellen till ACI-undernät

Kör [uppdaterings kommandot AZ Network VNet Subnet][az-network-vnet-subnet-update] för att associera routningstabellen med under nätet delegerat till Azure Container instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Konfigurera regler för brand vägg

Som standard nekar Azure-brandväggen (blockerar) inkommande och utgående trafik. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Konfigurera NAT-regel för brand vägg till ACI-undernät

Skapa en [NAT-regel](../firewall/rule-processing.md) i brand väggen för att översätta och filtrera inkommande Internet trafik till den program behållare som du startade tidigare i nätverket. Mer information finns i [filtrera inkommande Internet trafik med Azure FIREWALL DNAt](../firewall/tutorial-firewall-dnat.md)

Skapa en NAT-regel och-samling med hjälp av kommandot [AZ Network Firewall NAT-Rule Create][az-network-firewall-nat-rule-create] :

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Lägg till NAT-regler efter behov för att filtrera trafik till andra IP-adresser i under nätet. Till exempel kan andra behållar grupper i under nätet exponera IP-adresser för inkommande trafik, eller så kan andra interna IP-adresser tilldelas till behållar gruppen efter en omstart.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Skapa utgående program regel i brand väggen

Kör följande [AZ Network Firewall Application-Rule Create-][az-network-firewall-application-rule-create] kommando för att skapa en utgående regel i brand väggen. Med den här exempel regeln kan åtkomst från under nätet delegeras till att Azure Container Instances till det fullständiga domän namnet `checkip.dyndns.org` . HTTP-åtkomst till platsen används i ett senare steg för att bekräfta den utgående IP-adressen från Azure Container Instances.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Testa åtkomst till behållar grupp via brand väggen

Följande avsnitt kontrollerar att under nätet som delegerats till Azure Container Instances har kon figurer ATS korrekt bakom Azure-brandväggen. Föregående steg dirigerade både inkommande trafik till under nätet och utgående trafik från under nätet genom brand väggen.

### <a name="test-ingress-to-a-container-group"></a>Testa ingress till en behållar grupp

Testa inkommande åtkomst till *AppContainer* som körs i det virtuella nätverket genom att bläddra till brand väggens offentliga IP-adress. Tidigare sparade du den offentliga IP-adressen i variabeln $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Utdata liknar följande:

```console
52.142.18.133
```

Om NAT-regeln i brand väggen är korrekt konfigurerad visas följande när du anger brand väggens offentliga IP-adress i webbläsaren:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Bläddra till brand väggens offentliga IP-adress":::

### <a name="test-egress-from-a-container-group"></a>Testa utgående från en behållar grupp


Distribuera följande exempel behållare till det virtuella nätverket. När den körs skickar den en enda HTTP-begäran till `http://checkip.dyndns.org` , som visar avsändarens IP-adress (den utgående IP-adressen). Om program regeln i brand väggen har kon figurer ATS korrekt returneras brand väggens offentliga IP-adress.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Visa behållar loggarna för att bekräfta att IP-adressen är samma som den offentliga IP-adressen för brand väggen.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

Utdata liknar följande:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapar du behållar grupper i ett virtuellt nätverk bakom en Azure-brandvägg. Du har konfigurerat en användardefinierad väg och NAT-och program regler i brand väggen. Genom att använda den här konfigurationen ställer du in en statisk IP-adress för ingående och utgående från Azure Container Instances.

Mer information om hur du hanterar trafik och skyddar Azure-resurser finns i dokumentationen för [Azure-brandväggen](../firewall/index.yml) .



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






