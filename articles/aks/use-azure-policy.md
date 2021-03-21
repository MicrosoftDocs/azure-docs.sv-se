---
title: Använd Azure Policy för att skydda ditt kluster
description: Använd Azure Policy för att skydda ett AKS-kluster (Azure Kubernetes service).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193506"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Skydda ditt kluster med Azure Policy

För att förbättra säkerheten för ditt AKS-kluster (Azure Kubernetes service) kan du tillämpa och tillämpa inbyggda säkerhets principer på klustret med hjälp av Azure Policy. [Azure policy][azure-policy] hjälper till att upprätthålla organisations standarder och utvärdera kompatibiliteten i stor skala. När du har installerat [Azure policy-tillägget för AKS][kubernetes-policy-reference]kan du använda enskilda princip definitioner eller grupper av princip definitioner som kallas initiativ (kallas ibland policysets) i klustret. Se [Azure policy inbyggda definitioner för AKS][aks-policies] för en fullständig lista över AKS-principer och initiativ definitioner.

Den här artikeln visar hur du tillämpar princip definitioner på klustret och kontrollerar att tilldelningarna tillämpas.

## <a name="prerequisites"></a>Förutsättningar

- Ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].
- Azure Policy-tillägget för AKS som är installerat i ett AKS-kluster. Följ de här [stegen för att installera Azure policy-tillägget][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Tilldela en inbyggd princip definition eller ett initiativ

Använd Azure Portal för att tillämpa en princip definition eller ett initiativ.

1. Navigera till tjänsten Azure Policy i Azure Portal.
1. I den vänstra rutan på sidan Azure Policy väljer du **definitioner**.
1. Under **Kategorier** väljer du `Kubernetes` .
1. Välj den princip definition eller det initiativ som du vill använda. I det här exemplet väljer du `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiativet.
1. Välj **Tilldela**.
1. Ange **omfånget** till resurs gruppen för AKS-klustret med Azure policy-tillägget aktiverat.
1. Välj sidan **parametrar** och uppdatera **resultatet** från `audit` till `deny` för att blockera nya distributioner som bryter mot bas linje initiativ. Du kan också lägga till ytterligare namn rymder som ska undantas från utvärderingen. Behåll standardvärdena i det här exemplet.
1. Välj **Granska + skapa** och sedan **skapa** för att skicka princip tilldelningen.

## <a name="validate-a-azure-policy-is-running"></a>Verifiera att en Azure Policy körs

Bekräfta att princip tilldelningarna tillämpas på klustret genom att köra följande:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Princip tilldelningar kan ta [upp till 20 minuter innan de synkroniseras][azure-policy-assign-policy] till varje kluster.

Utdata bör likna följande:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Verifiera avvisande av en privilegie rad Pod

Vi börjar med att testa vad som händer när du schemalägger en POD med säkerhets kontexten för `privileged: true` . Den här säkerhets kontexten eskalerar Pod privilegier. Initiativet tillåter inte privilegie rad poddar, så begäran kommer att nekas, vilket leder till att distributionen avvisas.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f nginx-privileged.yaml
```

Som förväntat kan Pod inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

### <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en ej privilegie rad Pod

I föregående exempel försökte behållar avbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av princip initiativet, så Pod kan inte starta. Nu ska vi prova att köra samma NGINX-Pod utan privilegie rad åtkomst.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod har schemalagts. När du kontrollerar status för Pod med kommandot [kubectl get poddar][kubectl-get] *körs* pod:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

I det här exemplet visas ett bas linje initiativ som endast påverkar distributioner som bryter mot principer i samlingen. Tillåtna distributioner fortsätter att fungera.

Ta bort NGINX Pod med kommandot [kubectl Delete][kubectl-delete] och ange namnet på din yaml-manifest:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Inaktivera en princip eller ett initiativ

Så här tar du bort bas linje initiativ:

1. Navigera till princip fönstret på Azure Portal.
1. Välj **tilldelningar** i det vänstra fönstret.
1. Klicka på knappen **...** bredvid `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiativet.
1. Välj **ta bort tilldelning**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure Policy fungerar:

- [Översikt över Azure Policy][azure-policy]
- [Azure Policy initiativ och principer för AKS][aks-policies]
- Ta bort [Azure policy-tillägget][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
