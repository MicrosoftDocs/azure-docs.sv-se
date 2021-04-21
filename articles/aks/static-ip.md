---
title: Använda statisk IP med lastbalanserare
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en statisk IP-adress med Azure Kubernetes Service lastbalanserare (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: bb1e5691027a4bd86b57390e12259ac165ca9ed8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769525"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använd en statisk offentlig IP-adress och DNS-etikett med Azure Kubernetes Service (AKS) lastbalanserare

Som standard är den offentliga IP-adressen som tilldelats en lastbalanseringsresurs som skapats av ett AKS-kluster endast giltig under resursens livslängd. Om du tar bort Kubernetes-tjänsten tas även den associerade lastbalanseraren och IP-adressen bort. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

Den här artikeln visar hur du skapar en statisk offentlig IP-adress och tilldelar den till din Kubernetes-tjänst.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Den här artikeln beskriver hur du använder *en STANDARD-SKU* IP med en standard-SKU-lastbalanserare.  Mer information finns i [IP-adresstyper och allokeringsmetoder i Azure.][ip-sku]

## <a name="create-a-static-ip-address"></a>Skapa en statisk IP-adress

Skapa en statisk offentlig IP-adress med [kommandot az network public ip create.][az-network-public-ip-create] Följande skapar en statisk IP-resurs med *namnet myAKSPublicIP* i *resursgruppen myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Om du använder en Basic SKU-lastbalanserare i ditt AKS-kluster använder du *Basic* som *SKU-parameter* när du definierar en offentlig IP-adress.  Endast *Basic* SKU-IP-adresser fungerar med *Basic* SKU-lastbalanserare och endast STANDARD-SKU-IP-adresser fungerar med  *standard-SKU-lastbalanserare.* 

IP-adressen visas, som du ser i följande komprimerade exempelutdata:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Du kan senare hämta den offentliga IP-adressen med [kommandot az network public-ip list.][az-network-public-ip-list] Ange namnet på nodresursgruppen och den offentliga IP-adress som du skapade och fråga efter *ipAddress* enligt följande exempel:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Skapa en tjänst med den statiska IP-adressen

Se till att klusteridentiteten som används av AKS-klustret har delegerad behörighet till den andra resursgruppen innan du skapar en tjänst. Exempel:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Om du har anpassat din utgående IP-adress kontrollerar du att klusteridentiteten har behörighet till både den utgående offentliga IP-adressen och den här inkommande offentliga IP-adressen.

Om du vill skapa *en LoadBalancer-tjänst* med den statiska offentliga IP-adressen lägger du till egenskapen och värdet för den statiska offentliga IP-adressen i `loadBalancerIP` YAML-manifestet. Skapa en fil med `load-balancer-service.yaml` namnet och kopiera följande YAML. Ange din egen offentliga IP-adress som skapades i föregående steg. I följande exempel anges även anteckningen till resursgruppen med namnet *myResourceGroup.* Ange ditt eget resursgruppsnamn.

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

Skapa tjänsten och distributionen med `kubectl apply` kommandot .

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Tillämpa en DNS-etikett på tjänsten

Om tjänsten använder en dynamisk eller statisk offentlig IP-adress kan du använda tjänstanteckningar för att `service.beta.kubernetes.io/azure-dns-label-name` ange en offentlig DNS-etikett. Då publiceras ett fullständigt domännamn för din tjänst med hjälp av Azures offentliga DNS-servrar och toppnivådomän. Kommentarsvärdet måste vara unikt på Azure-platsen, så vi rekommenderar att du använder en tillräckligt kvalificerad etikett.   

Azure lägger sedan automatiskt till ett standardundernät, till exempel (där platsen är den region som du har valt) i det namn du anger för att skapa det fullständigt `<location>.cloudapp.azure.com` kvalificerade DNS-namnet. Exempel:

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
> Information om hur du publicerar tjänsten på din egen [domän finns Azure DNS][azure-dns-zone] och projektet [external-dns.][external-dns]

## <a name="troubleshoot"></a>Felsöka

Om den statiska IP-adressen som definierats i egenskapen *loadBalancerIP* för Kubernetes-tjänstmanifestet inte finns eller inte har skapats i nodresursgruppen och inga ytterligare delegeringar har konfigurerats, går det inte att skapa lastbalanserartjänsten. Om du vill felsöka granskar du tjänstgenereringshändelserna med [kommandot kubectl describe.][kubectl-describe] Ange namnet på tjänsten som anges i YAML-manifestet, som du ser i följande exempel:

```console
kubectl describe service azure-load-balancer
```

Information om Kubernetes-tjänstresursen visas. Händelserna *i* slutet av följande exempelutdata visar att användaren som angav *IP-adressen inte hittades.* I dessa scenarier kontrollerar du att du har skapat den statiska offentliga IP-adressen i nodresursgruppen och att IP-adressen som anges i Kubernetes-tjänstmanifestet är korrekt.

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

Om du vill ha ytterligare kontroll över nätverkstrafiken till dina program kanske du i stället [vill skapa en ingress-kontrollant.][aks-ingress-basic] Du kan också [skapa en ingress-kontrollant med en statisk offentlig IP-adress.][aks-static-ingress]

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
