---
title: Installera konsulär i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder konsulär för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683927"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Installera och använda konsulär i Azure Kubernetes service (AKS)

[Konsulär][consul-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar tjänst identifiering, hälso kontroll, service segmentering och att det går att observera. Mer information om konsulär finns i den officiella artikeln [Vad är konsulär?][consul-docs-concepts] dokumentation.

Den här artikeln visar hur du installerar konsulär. Konsulära komponenter installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Dessa instruktioner hänvisar till konsulär version `1.6.0` och använder minst Helm version `2.14.2` .
>
> Konsulära versioner `1.6.x` kan köras mot Kubernetes-versioner `1.13+` . Du hittar ytterligare konsulära versioner på [GitHub-uppdateringar][consul-github-releases] och information om var och en av de versioner som finns i [konsulära versions anteckningar][consul-release-notes].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Installera konsulära komponenter på AKS
> * Verifiera den konsulära installationen
> * Avinstallera konsulär från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.13` och senare, med KUBERNETES RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart]. Se till att klustret har minst 3 noder i Linux-noden.

Du behöver [Helm][helm] för att följa dessa anvisningar och installera konsulär. Vi rekommenderar att du har den senaste säkra versionen som är korrekt installerad och konfigurerad i klustret. Om du behöver hjälp med att installera Helm kan du läsa mer i [installations vägledningen för AKS Helm][helm-install]. Alla konsulära poddar måste också vara schemalagda att köras på Linux-noder.

Den här artikeln separerar den konsulära installations vägledningen i flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella konsulära installations [vägledningen][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>Installera konsulära komponenter på AKS

Vi börjar med att hämta version `v0.10.0` av konsulär Helm-diagrammet. Den här versionen av diagrammet innehåller konsulär version `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Använd Helm och det nedladdade `consul-helm` diagrammet för att installera konsulära komponenter i `consul` namn området i ditt AKS-kluster. 

> [!NOTE]
> **Installationsalternativ**
> 
> Vi använder följande alternativ som en del av installationen:
> - `connectInject.enabled=true` -Aktivera att proxyservrar matas in i poddar
> - `client.enabled=true` – gör det möjligt för konsulära kunder att köras på varje nod
> - `client.grpc=true` -Aktivera gRPC-lyssnare för connectInject
> - `syncCatalog.enabled=true` -Sync-Kubernetes och konsulära tjänster
>
> **Node-väljare**
>
> Konsulär måste för närvarande vara schemalagd för att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att konsulär poddar endast är schemalagda att köras på Linux-noder. Vi använder [Node-väljare][kubernetes-node-selectors] för att kontrol lera att poddar är schemalagda till rätt noder.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul`Helm-diagrammet distribuerar ett antal objekt. Du kan se listan från utdata från `helm install` kommandot ovan. Det kan ta ungefär tre minuter att slutföra distributionen av konsulära komponenter, beroende på din kluster miljö.

Nu har du distribuerat konsulär till ditt AKS-kluster. För att säkerställa att vi har en lyckad distribution av konsulär kan vi gå vidare till nästa avsnitt för att verifiera den konsulära installationen.

## <a name="validate-the-consul-installation"></a>Verifiera den konsulära installationen

Bekräfta att resurserna har skapats. Använd [kubectl get SVC][kubectl-get] och [kubectl get Pod][kubectl-get] för att fråga efter `consul` namn området där konsulära komponenter installerades med `helm install` kommandot:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Följande exempel på utdata visar tjänsterna och poddar (schemalagda på Linux-noder) som nu ska köras:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Alla poddar bör visa status för `Running` . Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-consul-ui"></a>Åtkomst till konsulärt användar gränssnitt

Konsulärt användar gränssnitt installerades i vår installation ovan och ger UI-baserad konfiguration för konsulär. Användar gränssnittet för konsulär visas inte offentligt via en extern IP-adress. Använd kommandot [kubectl Port-Forward][kubectl-port-forward] för att komma åt användar gränssnittet i konsulär. Det här kommandot skapar en säker anslutning mellan klient datorn och relevanta Pod i ditt AKS-kluster.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Nu kan du öppna en webbläsare och peka den för `http://localhost:8080/ui` att öppna det konsulära användar gränssnittet. Du bör se följande när du öppnar användar gränssnittet:

![Konsulärt användar gränssnitt](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Avinstallera konsulär från AKS

> [!WARNING]
> Om du tar bort konsulär från ett system som körs kan trafik problem uppstå mellan dina tjänster. Se till att du har gjort att systemet fortfarande fungerar korrekt utan konsulär innan du fortsätter.

### <a name="remove-consul-components-and-namespace"></a>Ta bort konsulära komponenter och namnrymd

Om du vill ta bort konsulär från AKS-klustret använder du följande kommandon. De här `helm delete` kommandona tar bort `consul` diagrammet och tar `kubectl delete namespace` bort `consul` namn området.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Nästa steg

För att utforska fler installations-och konfigurations alternativ för konsulär, se följande officiella konsulära artiklar:

- [Konsulär-Helm installations guide][consul-install-k8]
- [Konsulär – Helm installations alternativ][consul-install-helm-options]

Du kan också följa ytterligare scenarier som använder:

- [Konsulär exempel program][consul-app-example]
- [Konsulär Kubernetes-referens arkitektur][consul-reference]
- [Konsulärt nät-gatewayer][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
