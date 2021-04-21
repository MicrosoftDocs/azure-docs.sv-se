---
title: Använda statisk IP för utgående trafik
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en statisk offentlig IP-adress för utgående trafik i ett AKS-kluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 7cff3f5d66bb9872a0c949c6237150f8b69c9fa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773017"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Använd en statisk offentlig IP-adress för utgående trafik med en *Basic* SKU-lastbalanserare i Azure Kubernetes Service (AKS)

Som standard tilldelas den utgående IP-adressen från ett Azure Kubernetes Service-kluster (AKS) slumpmässigt. Den här konfigurationen är inte idealisk när du till exempel behöver identifiera en IP-adress för åtkomst till externa tjänster. I stället kan du behöva tilldela en statisk IP-adress som ska läggas till i en lista över tillåtna för tjänståtkomst.

Den här artikeln visar hur du skapar och använder en statisk offentlig IP-adress för användning med utgående trafik i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du använder Azure Basic Load Balancer.  Vi rekommenderar att du [använder Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md), och du kan använda mer avancerade funktioner för att styra [utgående AKS-trafik.](./limit-egress-traffic.md)

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

> [!IMPORTANT]
> I den här artikeln används *lastbalanserare* för Basic SKU med en enda nodpool. Den här konfigurationen är inte tillgänglig för flera nodpooler eftersom *Basic* SKU-lastbalanserare inte stöds med flera nodpooler. Se [Använda en offentlig Standard Load Balancer i Azure Kubernetes Service (AKS)][slb] för mer information om hur du använder standard-SKU-lastbalanserare. 

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

Utgående trafik från ett AKS-kluster följer [Azure Load Balancer konventioner][outbound-connections]. Innan den första Kubernetes-tjänsten av typen skapas är agentnoderna i ett AKS-kluster inte en del `LoadBalancer` Azure Load Balancer en pool. I den här konfigurationen har noderna ingen offentlig IP-adress på instansnivå. Azure översätter det utgående flödet till en OFFENTLIG KÄLL-IP-adress som inte kan konfigureras eller deterministisk.

När en Kubernetes-tjänst av `LoadBalancer` typen har skapats läggs agentnoder till i en Azure Load Balancer pool. Load Balancer Basic väljer en enda frontend som ska användas för utgående flöden när flera (offentliga) IP-datorer är kandidater för utgående flöden. Det här valet kan inte konfigureras och du bör betrakta urvalsalgoritmen som slumpmässig. Den här offentliga IP-adressen är endast giltig under resursens livslängd. Om du tar bort Kubernetes LoadBalancer-tjänsten tas även den associerade lastbalanseraren och IP-adressen bort. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

Hämta resursgruppens namn med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en statisk offentlig IP-adress med [kommandot az network public ip][az-network-public-ip-create] create. Ange namnet på nodens resursgrupp som du fick i föregående kommando och sedan ett namn på IP-adressresursen, till exempel *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas, som du ser i följande komprimerade exempelutdata:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Du kan senare hämta den offentliga IP-adressen med [kommandot az network public-ip list.][az-network-public-ip-list] Ange namnet på nodresursgruppen och fråga sedan efter *ipAddress* enligt följande exempel:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med den statiska IP-adressen

Om du vill skapa en tjänst med den statiska offentliga IP-adressen lägger du till egenskapen och värdet för `loadBalancerIP` den statiska offentliga IP-adressen i YAML-manifestet. Skapa en fil med `egress-service.yaml` namnet och kopiera följande YAML. Ange din egen offentliga IP-adress som skapades i föregående steg.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Skapa tjänsten och distributionen med `kubectl apply` kommandot .

```console
kubectl apply -f egress-service.yaml
```

Den här tjänsten konfigurerar en ny IP-adress för Azure Load Balancer. Om du inte har några andra IP-adresser konfigurerade ska **all utgående** trafik nu använda den här adressen. När flera adresser konfigureras på Azure Load Balancer är någon av dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.

## <a name="verify-egress-address"></a>Verifiera utgående adress

Om du vill kontrollera att den statiska offentliga IP-adressen används kan du använda DNS-söktjänsten, till exempel `checkip.dyndns.org` .

Starta och koppla till en grundläggande *Debian-podd:*

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Om du vill komma åt en webbplats inifrån containern använder `apt-get` du för att installera i `curl` containern.

```console
apt-get update && apt-get install curl -y
```

Använd curl för att komma åt *checkip.dyndns.org* webbplats. Den utgående IP-adressen visas, som du ser i följande exempelutdata. Den här IP-adressen matchar den statiska offentliga IP-adress som skapats och definierats för tjänsten loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Nästa steg

Om du vill undvika att ha flera offentliga IP-adresser Azure Load Balancer kan du i stället använda en ingress-kontrollant. Ingress-styrenheter ger ytterligare fördelar, till exempel SSL/TLS-avslutning, stöd för URI-omwrites och överordnad SSL/TLS-kryptering. Mer information finns i Skapa [en grundläggande ingress-kontrollant i AKS.][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
