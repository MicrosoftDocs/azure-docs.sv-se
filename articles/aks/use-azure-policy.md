---
title: Använda Azure Policy för att skydda klustret
description: Använd Azure Policy för att skydda ett Azure Kubernetes Service-kluster (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777733"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Skydda ditt kluster med Azure Policy

För att förbättra säkerheten för ditt Azure Kubernetes Service-kluster (AKS) kan du tillämpa och tillämpa inbyggda säkerhetsprinciper på klustret med hjälp av Azure Policy. [Azure Policy][azure-policy] hjälper till att genomdriva organisationsstandarder och utvärdera efterlevnad i stor skala. När du har [installerat Azure Policy-tillägget][kubernetes-policy-reference]för AKS kan du tillämpa enskilda principdefinitioner eller grupper av principdefinitioner som kallas initiativ (kallas ibland principuppsättningar) på klustret. Se [Azure Policy inbyggda definitioner för AKS för][aks-policies] en fullständig lista över AKS-princip- och initiativdefinitioner.

Den här artikeln visar hur du tillämpar principdefinitioner på klustret och kontrollerar att dessa tilldelningar tillämpas.

## <a name="prerequisites"></a>Förutsättningar

- Ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].
- Tillägget Azure Policy AKS installerat på ett AKS-kluster. Följ dessa [steg för att Azure Policy med tillägget][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Tilldela en inbyggd principdefinition eller ett initiativ

Om du vill tillämpa en principdefinition eller ett initiativ använder du Azure Portal.

1. Gå till Azure Policy i Azure Portal.
1. I den vänstra rutan på Azure Policy väljer du **Definitioner**.
1. Under **Kategorier väljer** du `Kubernetes` .
1. Välj den principdefinition eller det initiativ som du vill tillämpa. I det här exemplet väljer du `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiativet .
1. Välj **Tilldela**.
1. Ange **Omfång** till resursgruppen för AKS-klustret med Azure Policy-tillägget aktiverat.
1. Välj sidan **Parametrar** och uppdatera effekten **från** till `audit` för att blockera nya `deny` distributioner som bryter mot baslinjeinitiativ. Du kan också lägga till ytterligare namnrymder som ska undantas från utvärderingen. Behåll standardvärdena i det här exemplet.
1. Välj **Granska + skapa och** sedan **Skapa** för att skicka principtilldelningen.

## <a name="validate-a-azure-policy-is-running"></a>Verifiera att Azure Policy körs

Bekräfta att principtilldelningarna tillämpas på klustret genom att köra följande:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Det kan ta upp till [20 minuter att synkronisera principtilldelningar][azure-policy-assign-policy] i varje kluster.

Utdata bör se ut ungefär så här:

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

### <a name="validate-rejection-of-a-privileged-pod"></a>Validera avvisande av en privilegierad podd

Först testar vi vad som händer när du schemalägger en podd med säkerhetskontexten `privileged: true` . Den här säkerhetskontexten eskalerar poddens behörigheter. Initiativet nekar privilegierade poddar, så begäran nekas, vilket resulterar i att distributionen avvisas.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande YAML-manifest:

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

Skapa podden med [kommandot kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f nginx-privileged.yaml
```

Som förväntat går det inte att schemalägga podden, som du ser i följande exempelutdata:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Podden når inte schemaläggningsfasen, så det finns inga resurser att ta bort innan du går vidare.

### <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en podd utan privilegier

I föregående exempel försökte containeravbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av principinitiativ, så podden kan inte starta. Nu ska vi prova att köra samma NGINX-podd utan privilegierad åtkomst.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande YAML-manifest:

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

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Podden har schemalagts. När du kontrollerar statusen för podden med kommandot [kubectl get pods][kubectl-get] är podden *Körs:*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Det här exemplet visar baslinjeinitiativ som endast påverkar distributioner som bryter mot principer i samlingen. Tillåtna distributioner fortsätter att fungera.

Ta bort NGINX-podden utan privilegier med kommandot [kubectl delete][kubectl-delete] och ange namnet på ditt YAML-manifest:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Inaktivera en princip eller ett initiativ

Så här tar du bort baslinjeinitiativ:

1. Gå till fönstret Princip på Azure Portal.
1. Välj **Tilldelningar** i det vänstra fönstret.
1. Klicka på **knappen** ... bredvid `Kubernetes cluster pod security baseline standards for Linux-based workloads` initiativet.
1. Välj **Ta bort tilldelning.**

## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure Policy fungerar:

- [Översikt över Azure Policy][azure-policy]
- [Azure Policy initiativ och Azure Policy för AKS][aks-policies]
- Ta [bort Azure Policy-tillägget][azure-policy-addon-remove].

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
