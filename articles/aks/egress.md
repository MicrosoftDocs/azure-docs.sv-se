---
title: Använd statisk IP för utgående trafik
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en statisk offentlig IP-adress för utgående trafik i ett Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: e1f81bf4c4d35108557449a8bebd126bdf744191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592378"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Använd en statisk offentlig IP-adress för utgående trafik med en *Basic* SKU-belastningsutjämnare i Azure Kubernetes service (AKS)

Som standard tilldelas den utgående IP-adressen från ett Azure Kubernetes service-kluster (AKS) slumpmässigt. Den här konfigurationen är inte idealisk när du behöver identifiera en IP-adress för åtkomst till externa tjänster, till exempel. I stället kan du behöva tilldela en statisk IP-adress som ska läggas till i en tillåten för åtkomst till tjänsten.

Den här artikeln visar hur du skapar och använder en statisk offentlig IP-adress för användning med utgående trafik i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

I den här artikeln förutsätter vi att du använder Azure Basic-Load Balancer.  Vi rekommenderar att du använder [Azure-standard Load Balancer](../load-balancer/load-balancer-overview.md)och du kan använda mer avancerade funktioner för att [kontrol lera utgående trafik i AKS](./limit-egress-traffic.md).

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

> [!IMPORTANT]
> I den här artikeln används *Basic* SKU-belastningsutjämnare med en enda Node-pool. Den här konfigurationen är inte tillgänglig för flera resurspooler eftersom den *grundläggande* SKU-belastningsutjämnaren inte stöds med flera noder. Mer information om hur du använder *standard* -SKU: n finns i [använda en offentlig standard Load Balancer i Azure KUBERNETES service (AKS)][slb] .

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

Utgående trafik från ett AKS-kluster följer [Azure Load Balancer konventioner][outbound-connections]. Innan den första Kubernetes-tjänsten av typen `LoadBalancer` skapas, ingår inte agent-noderna i ett AKS-kluster i någon Azure Load Balancer pool. I den här konfigurationen har noderna ingen offentlig IP-adress på instans nivå. Azure översätter det utgående flödet till en offentlig käll-IP-adress som inte kan konfigureras eller deterministisk.

När en Kubernetes-tjänst av typen `LoadBalancer` har skapats läggs agent-noder till i en Azure Load Balancer-pool. Load Balancer Basic väljer en enda klient del som ska användas för utgående flöden när flera (offentliga) IP-frontend-klienter är kandidater för utgående flöden. Det här alternativet kan inte konfigureras och du bör fundera på att val av algoritm ska vara slumpmässigt. Den här offentliga IP-adressen är bara giltig för livs längd för resursen. Om du tar bort Kubernetes LoadBalancer-tjänsten raderas även den tillhör ande belastningsutjämnaren och IP-adressen. Om du vill tilldela en speciell IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

Hämta resurs gruppens namn med kommandot [AZ AKS show][az-aks-show] och Lägg till `--query nodeResourceGroup` Frågeparametern. I följande exempel hämtas resurs gruppen för AKS kluster namnet *myAKSCluster* i resurs grupps namnet *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en statisk offentlig IP-adress med kommandot [AZ Network Public IP Create][az-network-public-ip-create] . Ange resurs grupp namnet för noden som hämtades i föregående kommando och ett namn för IP-adressresurs, till exempel *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas, som du ser i följande komprimerade exempel i utdata:

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

Du kan senare hämta den offentliga IP-adressen med kommandot [AZ Network Public-IP List][az-network-public-ip-list] . Ange namnet på resurs gruppen för noden och fråga sedan efter *IP-adressen* som visas i följande exempel:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med den statiska IP-adressen

Om du vill skapa en tjänst med den statiska offentliga IP-adressen lägger du till `loadBalancerIP` egenskapen och värdet för den statiska offentliga IP-adressen i yaml-manifestet. Skapa en fil med namnet `egress-service.yaml` och kopiera i följande yaml. Ange din egen offentliga IP-adress som skapades i föregående steg.

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

Skapa tjänsten och distributionen med `kubectl apply` kommandot.

```console
kubectl apply -f egress-service.yaml
```

Den här tjänsten konfigurerar en ny klient dels-IP på Azure Load Balancer. Om du inte har några andra IP-adresser konfigurerade ska **all** utgående trafik nu använda den här adressen. När flera adresser har kon figurer ATS på Azure Load Balancer, är alla dessa offentliga IP-adresser en kandidat för utgående flöden och en väljs slumpmässigt.

## <a name="verify-egress-address"></a>Verifiera utgående adress

Du kan kontrol lera att den statiska offentliga IP-adressen används genom att använda DNS-söktjänst som `checkip.dyndns.org` .

Starta och koppla till en grundläggande *Debian* -pod:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Om du vill komma åt en webbplats inifrån behållaren använder `apt-get` du för att installera `curl` i behållaren.

```console
apt-get update && apt-get install curl -y
```

Använd nu en sväng för att komma åt *checkip.dyndns.org* -webbplatsen. Den utgående IP-adressen visas, som visas i följande exempel på utdata. Den här IP-adressen matchar den statiska offentliga IP-adress som skapats och definierats för loadBalancer-tjänsten:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Nästa steg

För att undvika att behålla flera offentliga IP-adresser på Azure Load Balancer kan du i stället använda en ingångs kontroll. Ingångs styrenheter ger ytterligare fördelar som SSL/TLS-terminering, stöd för URI-omskrivning och överordnad SSL/TLS-kryptering. Mer information finns i [skapa en grundläggande ingress-styrenhet i AKS][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
