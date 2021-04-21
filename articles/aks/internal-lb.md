---
title: Skapa en intern lastbalanserare
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en intern lastbalanserare för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: cbb898d05ecc1f0796f3609adb1368c3d77de2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779749"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern lastbalanserare med Azure Kubernetes Service (AKS)

Om du vill begränsa åtkomsten till dina Azure Kubernetes Service (AKS) kan du skapa och använda en intern lastbalanserare. En intern lastbalanserare gör en Kubernetes-tjänst endast tillgänglig för program som körs i samma virtuella nätverk som Kubernetes-klustret. Den här artikeln visar hur du skapar och använder en intern lastbalanserare med Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer finns i två SKU:er – *Basic* och *Standard*. Standard-SKU:n används när du skapar ett AKS-kluster.  När du skapar en tjänst med typen LoadBalancer får du samma lastbalanseringstyp som när du etablerar klustret. Mer information finns i [SKU-jämförelse för Azure Load Balancer.][azure-lb-comparison]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Du måste också ha installerat och konfigurerat Azure CLI version 2.0.59 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

AKS-klusterklusteridentiteten behöver behörighet att hantera nätverksresurser om du använder ett befintligt undernät eller en resursgrupp. Mer information finns i Använda kubenet-nätverk med dina [egna IP-adressintervall i Azure Kubernetes Service (AKS)][use-kubenet] eller Konfigurera [Azure CNI-nätverk i Azure Kubernetes Service (AKS).][advanced-networking] Om du konfigurerar lastbalanseraren att använda en [IP-adress][different-subnet]i ett annat undernät ska du se till att AKS-klusteridentiteten även har läsbehörighet till det undernätet.

Mer information om behörigheter finns i Delegera [AKS-åtkomst till andra Azure-resurser.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

Om du vill skapa en intern lastbalanserare skapar du ett tjänstmanifest med namnet med tjänsttypen `internal-lb.yaml` *LoadBalancer* och *azure-load-balancer-internal-anteckningen* enligt följande exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Distribuera den interna lastbalanseraren med [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f internal-lb.yaml
```

En Azure-lastbalanserare skapas i nodresursgruppen och ansluts till samma virtuella nätverk som AKS-klustret.

När du visar tjänstinformationen visas IP-adressen för den interna lastbalanseraren i *kolumnen EXTERNAL-IP.* I det här sammanhanget *är Externt* i förhållande till det externa gränssnittet för lastbalanseraren, inte att den tar emot en offentlig, extern IP-adress. Det kan ta någon minut innan IP-adressen ändras från till en *\<pending\>* faktisk intern IP-adress, som du ser i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en specifik IP-adress med den interna lastbalanseraren lägger du till *egenskapen loadBalancerIP* i YAML-manifestet för lastbalanserare. I det här scenariot måste den angivna IP-adressen finnas i samma undernät som AKS-klustret och får inte redan tilldelas till en resurs. Du bör till exempel inte använda en IP-adress i intervallet som angetts för Kubernetes-undernätet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

När den har distribuerats och du visar tjänstinformationen återspeglar *IP-adressen i kolumnen EXTERNAL-IP* din angivna IP-adress:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Mer information om hur du konfigurerar lastbalanserare i ett annat undernät finns [i Ange ett annat undernät][different-subnet]

## <a name="use-private-networks"></a>Använda privata nätverk

När du skapar ditt AKS-kluster kan du ange avancerade nätverksinställningar. Med den här metoden kan du distribuera klustret till ett befintligt virtuellt Azure-nätverk och undernät. Ett scenario är att distribuera ditt AKS-kluster till ett privat nätverk som är anslutet till din lokala miljö och köra tjänster som endast är tillgängliga internt. Mer information finns i Konfigurera dina egna undernät för virtuella nätverk med [Kubenet][use-kubenet] eller [Azure CNI][advanced-networking].

Inga ändringar i föregående steg krävs för att distribuera en intern lastbalanserare i ett AKS-kluster som använder ett privat nätverk. Lastbalanseraren skapas i samma resursgrupp som ditt AKS-kluster men ansluts till ditt privata virtuella nätverk och undernät, enligt följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Du kan behöva ge klusteridentiteten för  ditt AKS-kluster rollen Nätverksdeltagare till den resursgrupp där dina virtuella Azure-nätverksresurser distribueras. Visa klusteridentiteten med [az aks show][az-aks-show], till exempel `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Om du vill skapa en rolltilldelning använder du [kommandot az role assignment][az-role-assignment-create] create.

## <a name="specify-a-different-subnet"></a>Ange ett annat undernät

Om du vill ange ett undernät för lastbalanseraren lägger du till anteckningen *azure-load-balancer-internal-subnet* i din tjänst. Det angivna undernätet måste finnas i samma virtuella nätverk som ditt AKS-kluster. När den distribueras är *lastbalanseringsadressen EXTERNAL-IP* en del av det angivna undernätet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Ta bort lastbalanseraren

När alla tjänster som använder den interna lastbalanseraren tas bort tas även själva lastbalanseraren bort.

Du kan också ta bort en tjänst direkt som med valfri Kubernetes-resurs, till exempel , som även tar bort den `kubectl delete service internal-app` underliggande Azure-lastbalanseraren.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes-tjänster i [kubernetes services-dokumentationen.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet