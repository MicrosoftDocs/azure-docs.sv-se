---
title: Konfigurera statisk utgående IP
description: Konfigurera Azure Firewall och användardefinierade vägar för Azure Container Instances arbetsbelastningar som använder brandväggens offentliga IP-adress för in- och utgående trafik
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 1cd0ff48da58706a1be59caf4b9d5974dc5f552a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790823"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Konfigurera en enskild offentlig IP-adress för utgående och inkommande trafik till en containergrupp

Genom att konfigurera [en containergrupp](container-instances-container-groups.md) med en extern IP-adress kan externa klienter använda IP-adressen för att få åtkomst till en container i gruppen. En webbläsare kan till exempel komma åt en webbapp som körs i en container. För närvarande använder dock en containergrupp en annan IP-adress för utgående trafik. Den här utgående IP-adressen exponeras inte programmatiskt, vilket gör övervakning av containergrupper och konfiguration av klientbrandväggsregler mer komplexa.

Den här artikeln innehåller steg för att konfigurera en containergrupp i ett [virtuellt nätverk](container-instances-virtual-network-concepts.md) som är integrerat [med Azure Firewall](../firewall/overview.md). Genom att konfigurera en användardefinierad väg till containergruppen och brandväggsreglerna kan du dirigera och identifiera trafik till och från containergruppen. In- och utgående containergrupp använder brandväggens offentliga IP-adress. En enda utgående IP-adress kan användas av flera containergrupper som distribueras i det virtuella nätverkets undernät som delegerats till Azure Container Instances.

I den här artikeln använder du Azure CLI för att skapa resurser för det här scenariot:

* Containergrupper som distribuerats i ett delegerat [undernät i det virtuella nätverket](container-instances-vnet.md) 
* En Azure-brandvägg som distribuerats i nätverket med en statisk offentlig IP-adress
* En användardefinierad väg i containergruppernas undernät
* En NAT-regel för brandväggsingress och en programregel för utgående

Sedan validerar du in- och utgående från exempelcontainergrupper genom brandväggen.

## <a name="deploy-aci-in-a-virtual-network"></a>Distribuera ACI i ett virtuellt nätverk

I ett typiskt fall kanske du redan har ett virtuellt Azure-nätverk där du kan distribuera en containergrupp. I exempelsyfte skapar följande kommandon ett virtuellt nätverk och undernät när containergruppen skapas. Undernätet delegeras till Azure Container Instances. 

Containergruppen kör en liten webbapp från `aci-helloworld` avbildningen. Som du ser i andra artiklar i dokumentationen paketerar den här avbildningen en liten webbapp skriven i Node.js som fungerar som en statisk HTML-sida.

Om du behöver en skapar du först en Azure-resursgrupp med [kommandot az group][az-group-create] create. Exempel:

```azurecli
az group create --name myResourceGroup --location eastus
```

För att förenkla följande kommandoexempel använder du en miljövariabel för resursgruppens namn:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Skapa containergruppen med [kommandot az container][az-container-create] create:

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
> Justera värdet för för `--subnet address-prefix` det IP-adressutrymme som du behöver i undernätet. Det minsta undernät som stöds är /29, som tillhandahåller åtta IP-adresser. Vissa IP-adresser är reserverade för användning av Azure.

För användning i ett senare steg hämtar du den privata IP-adressen för containergruppen genom att köra kommandot [az container show][az-container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Distribuera Azure Firewall i nätverket

I följande avsnitt använder du Azure CLI för att distribuera en Azure-brandvägg i det virtuella nätverket. Bakgrundsinformation finns i [Självstudie: Distribuera och konfigurera Azure Firewall med hjälp av Azure Portal](../firewall/deploy-cli.md).

Använd först az [network vnet subnet create för att][az-network-vnet-subnet-create] lägga till ett undernät med namnet AzureFirewallSubnet för brandväggen. AzureFirewallSubnet är det *obligatoriska* namnet på det här undernätet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Använd följande [Azure CLI-kommandon](../firewall/deploy-cli.md) för att skapa en brandvägg i undernätet.

Om det inte redan är installerat lägger du till brandväggstillägget i Azure CLI med [kommandot az extension add:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

Skapa brandväggsresurserna:

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

Uppdatera brandväggskonfigurationen med [kommandot az network firewall update:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Hämta brandväggens privata IP-adress med kommandot [az network firewall ip-config][az-network-firewall-ip-config-list] list. Den här privata IP-adressen används i ett senare kommando.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Hämta brandväggens offentliga IP-adress med kommandot [az network public-ip show.][az-network-public-ip-show] Den här offentliga IP-adressen används i ett senare kommando.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definiera användardefinierad väg i ACI-undernät

Definiera en användardefinierad väg i ACI-undernätet för att omdirigera trafik till Azure-brandväggen. Mer information finns i [Dirigera nätverkstrafik](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Skapa routningstabell

Kör först följande [az network route-table create-kommando][az-network-route-table-create] för att skapa vägtabellen. Skapa vägtabellen i samma region som det virtuella nätverket.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Skapa väg

Kör [az network-route-table route create][az-network-route-table-route-create] för att skapa en väg i vägtabellen. Om du vill dirigera trafik till brandväggen anger du nästa hopptyp till och skickar brandväggens privata `VirtualAppliance` IP-adress som nästa hopp-adress.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Associera vägtabell med ACI-undernät

Kör kommandot [az network vnet subnet update för][az-network-vnet-subnet-update] att associera vägtabellen med det undernät som delegerats till Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Konfigurera regler i brandväggen

Som standard Azure Firewall (block) inkommande och utgående trafik. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Konfigurera NAT-regel i brandvägg till ACI-undernät

Skapa en [NAT-regel](../firewall/rule-processing.md) i brandväggen för att översätta och filtrera inkommande Internettrafik till programcontainern som du startade tidigare i nätverket. Mer information finns i [Filtrera inkommande Internettrafik med Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)

Skapa en NAT-regel och samling med hjälp av [kommandot az network firewall nat-rule create:][az-network-firewall-nat-rule-create]

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

Lägg till NAT-regler efter behov för att filtrera trafik till andra IP-adresser i undernätet. Andra containergrupper i undernätet kan till exempel exponera IP-adresser för inkommande trafik, eller så kan andra interna IP-adresser tilldelas till containergruppen efter en omstart.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Skapa en regel för utgående program i brandväggen

Kör följande kommando [az network firewall application-rule create][az-network-firewall-application-rule-create] för att skapa en regel för utgående trafik i brandväggen. Den här exempelregeln tillåter åtkomst från det undernät som delegerats Azure Container Instances till FQDN. `checkip.dyndns.org` HTTP-åtkomst till platsen används i ett senare steg för att bekräfta den utgående IP-adressen från Azure Container Instances.

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

## <a name="test-container-group-access-through-the-firewall"></a>Testa åtkomsten till containergruppen via brandväggen

Följande avsnitt kontrollerar att undernätet som delegerats till Azure Container Instances är korrekt konfigurerat bakom Azure-brandväggen. Föregående steg dirigerade både inkommande trafik till undernätet och utgående trafik från undernätet genom brandväggen.

### <a name="test-ingress-to-a-container-group"></a>Testa ingress till en containergrupp

Testa inkommande åtkomst till *den appcontainer som* körs i det virtuella nätverket genom att bläddra till brandväggens offentliga IP-adress. Tidigare lagrade du den offentliga IP-adressen i variabeln $FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Utdata liknar följande:

```console
52.142.18.133
```

Om NAT-regeln i brandväggen är korrekt konfigurerad ser du följande när du anger brandväggens offentliga IP-adress i webbläsaren:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Bläddra till brandväggens offentliga IP-adress":::

### <a name="test-egress-from-a-container-group"></a>Testa utgående från en containergrupp


Distribuera följande exempelcontainer till det virtuella nätverket. När den körs skickar den en enda HTTP-begäran till `http://checkip.dyndns.org` , som visar avsändarens IP-adress (den utgående IP-adressen). Om programregeln i brandväggen är korrekt konfigurerad returneras brandväggens offentliga IP-adress.

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

Visa containerloggarna för att bekräfta att IP-adressen är samma som brandväggens offentliga IP-adress.

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

I den här artikeln ställer du in containergrupper i ett virtuellt nätverk bakom en Azure-brandvägg. Du konfigurerade en användardefinierad väg och NAT och programregler i brandväggen. Med den här konfigurationen kan du konfigurera en enda statisk IP-adress för in- och utgående från Azure Container Instances.

Mer information om hur du hanterar trafik och skyddar Azure-resurser finns i Azure Firewall dokumentationen. [](../firewall/index.yml)



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create
