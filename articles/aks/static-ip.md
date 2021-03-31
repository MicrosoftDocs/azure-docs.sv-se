---
title: Använd statisk IP med Load Balancer
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en statisk IP-adress med AKS-belastningsutjämnaren (Azure Kubernetes service).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 102df48ca22fb996e0f4d9c402b8ce8f0fa80f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102509480"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använd en statisk offentlig IP-adress och DNS-etikett med belastningsutjämnaren för Azure Kubernetes service (AKS)

Som standard är den offentliga IP-adress som tilldelats till en belastnings Utjämnings resurs som skapats av ett AKS-kluster bara giltig för livs längd för resursen. Om du tar bort Kubernetes-tjänsten raderas även den tillhör ande belastningsutjämnaren och IP-adress. Om du vill tilldela en speciell IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

Den här artikeln visar hur du skapar en statisk offentlig IP-adress och tilldelar den till din Kubernetes-tjänst.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Den här artikeln beskriver hur du använder en *standard* -SKU-IP med en *standard* -SKU-belastningsutjämnare. Mer information finns i [IP-diagramtyper och autentiseringsmetoder i Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Skapa en statisk IP-adress

Skapa en statisk offentlig IP-adress med kommandot [AZ Network Public IP Create][az-network-public-ip-create] . Följande skapar en statisk IP-resurs med namnet *myAKSPublicIP* i resurs gruppen *myResourceGroup* :

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Om du använder en *Basic* SKU-BELASTNINGSUTJÄMNARE i AKS-klustret använder du *Basic* för *SKU* -parametern när du definierar en offentlig IP-adress. Endast *grundläggande* SKU-adresser fungerar med *Basic* SKU-belastningsutjämnaren och endast *standard* -SKU: er fungerar med *standard* -SKU: er. 

IP-adressen visas, som du ser i följande komprimerade exempel i utdata:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Du kan senare hämta den offentliga IP-adressen med kommandot [AZ Network Public-IP List][az-network-public-ip-list] . Ange namnet på den resurs grupp för noden och den offentliga IP-adress som du skapade och fråga efter *IP* -adressen som visas i följande exempel:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Skapa en tjänst med hjälp av den statiska IP-adressen

Innan du skapar en tjänst kontrollerar du att kluster identiteten som används av AKS-klustret har delegerade behörigheter till den andra resurs gruppen. Exempel:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Om du har anpassat den utgående IP-adressen kontrollerar du att din kluster identitet har behörighet till både den utgående offentliga IP-adressen och den inkommande offentliga IP-adressen.

Om du vill skapa en *Loadbalancer* -tjänst med den statiska offentliga IP-adressen lägger du till `loadBalancerIP` egenskapen och värdet för den statiska offentliga IP-adressen i yaml-manifestet. Skapa en fil med namnet `load-balancer-service.yaml` och kopiera i följande yaml. Ange din egen offentliga IP-adress som skapades i föregående steg. I följande exempel anges även anteckningen till resurs gruppen med namnet *myResourceGroup*. Ange ett eget namn på din resurs grupp.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Skapa tjänsten och distributionen med `kubectl apply` kommandot.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Använda en DNS-etikett för tjänsten

Om tjänsten använder en dynamisk eller statisk offentlig IP-adress kan du använda tjänst anteckningen `service.beta.kubernetes.io/azure-dns-label-name` för att ange en offentlig DNS-etikett. Detta publicerar ett fullständigt kvalificerat domän namn för tjänsten med Azures offentliga DNS-servrar och toppnivå domänen. Anteckning svärdet måste vara unikt inom Azure-platsen, så vi rekommenderar att du använder en tillräckligt kvalificerad etikett.   

Azure lägger sedan automatiskt till ett standard under nät, till exempel `<location>.cloudapp.azure.com` (där platsen är den region som du har valt) till det namn som du anger för att skapa det fullständigt kvalificerade DNS-namnet. Exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Information om hur du publicerar tjänsten på din domän finns i [Azure DNS][azure-dns-zone] och det [externa-DNS-][external-dns] projektet.

## <a name="troubleshoot"></a>Felsöka

Om den statiska IP-adressen som definierats i *loadBalancerIP* -egenskapen för Kubernetes-tjänst manifestet inte finns eller inte har skapats i resurs gruppen för noden och inga ytterligare delegeringar har kon figurer ATS, Miss lyckas skapandet av tjänsten för belastningsutjämnare. Du kan felsöka genom att granska tjänstens skapande händelser med kommandot [kubectl beskriver][kubectl-describe] . Ange namnet på tjänsten enligt vad som anges i YAML-manifestet, som du ser i följande exempel:

```console
kubectl describe service azure-load-balancer
```

Information om Kubernetes-Tjänsteresursen visas. *Händelserna* i slutet av följande exempel på utdata visar att det *inte gick att hitta ANVÄNDAREns angivna IP-adress*. I dessa scenarier kontrollerar du att du har skapat den statiska offentliga IP-adressen i resurs gruppen för noden och att den IP-adress som anges i Kubernetes-tjänstens manifest är korrekt.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Nästa steg

Om du vill ha ytterligare kontroll över nätverks trafiken till dina program kan du i stället [skapa en][aks-ingress-basic]ingångs kontroll. Du kan också [skapa en ingångs kontroll enhet med en statisk offentlig IP-adress][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
