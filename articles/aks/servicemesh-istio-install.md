---
title: Installera Istio i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder Istio för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683836"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installera och använda Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du installerar Istio. Istio- `istioctl` klientens binärfil installeras på klient datorn och Istio-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Följande instruktioner hänvisar till Istio-versionen `1.7.3` .
>
> Istio- `1.7.x` versionerna har testats av Istio-teamet mot Kubernetes-versionen `1.16+` . Du hittar fler Istio-versioner på [GitHub-Istio-][istio-github-releases]versioner, information om var och en av versionerna på [Istio nyheter][istio-release-notes] och Kubernetes-versioner som stöds på [Istio allmänna vanliga frågor och svar][istio-faq].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hämta och installera Istio istioctl-klientens binärfil
> * Installera Istio på AKS
> * Verifiera Istio-installationen
> * Få åtkomst till tilläggen
> * Avinstallera Istio från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.16` och senare, med KUBERNETES RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart].

Se till att du har läst dokumentationen för [Istio-prestanda och skalbarhet](https://istio.io/docs/concepts/performance-and-scalability/) för att förstå de ytterligare resurs kraven för att köra Istio i ditt AKS-kluster. Kärn-och minnes kraven varierar beroende på din särskilda arbets belastning. Välj ett lämpligt antal noder och storlek för virtuella datorer för din installation.

Den här artikeln separerar installations vägledningen för Istio till flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella Istio installations [vägledningen][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>Installera Istio-operatören på AKS

Istio tillhandahåller en [operatör][istio-install-operator] för att hantera installation och uppdateringar av Istio-komponenterna i ditt AKS-kluster. Vi installerar Istio-operatören med hjälp av `istioctl` klientens binärfil.

```bash
istioctl operator init
```

Du bör se något som liknar följande utdata för att bekräfta att Istio-operatören har installerats.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio-operatorn installeras i `istio-operator` namn området. Fråga namn området.

```bash
kubectl get all -n istio-operator
```

Du bör se att följande komponenter har distribuerats.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Du kan lära dig mer om operator mönstret och hur det kan hjälpa dig att automatisera komplexa uppgifter via [Kubernetes.io][kubernetes-operator].


### <a name="install-istio-components"></a>Installera Istio-komponenter

Nu när vi har installerat Istio-operatören i vårt AKS-kluster är det dags att installera Istio-komponenterna. 

Vi använder `default` [konfigurations profilen Istio][istio-configuration-profiles] för att bygga [specifikationen Istio-operator][istio-control-plane].

Du kan köra följande `istioctl` kommando för att visa konfigurationen för `default` konfigurations profilen för Istio.

```bash
istioctl profile dump default
```

> [!NOTE]
> Istio måste för närvarande vara schemalagda att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Istio-poddar endast är schemalagda att köras på Linux-noder. Vi använder [Node-väljare][kubernetes-node-selectors] för att kontrol lera att poddar är schemalagda till rätt noder.

> [!CAUTION]
> [ISTIO cni][istio-feature-cni] Istio-funktionerna är för närvarande i [alpha][istio-feature-stages], så tänk på detta innan du aktiverar dem. 

Skapa en fil `istio.aks.yaml` med namnet med följande innehåll. Den här filen innehåller [specifikationen Istio-operatör][istio-control-plane] för att konfigurera Istio.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Skapa `istio-system` namn området och distribuera Istio-operatör specifikation till det namn området. Istio-operatorn kommer att titta efter Istio-operatörens specifikation och använder den för att installera och konfigurera Istio i AKS-klustret.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Nu har du distribuerat Istio till ditt AKS-kluster. För att säkerställa att vi har en lyckad distribution av Istio kan vi gå vidare till nästa avsnitt för att [validera Istio-installationen](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Verifiera Istio-installationen

Fråga `istio-system` namn området där Istio och tilläggs komponenterna installerades av Istio-operatorn:

```bash
kubectl get all -n istio-system
```

Du bör se följande komponenter:

- `istio*` – Istio-komponenterna
- `jaeger-*`, `tracing` och `zipkin` -spårnings tillägg
- `prometheus` – mått tillägg
- `grafana` -instrument panels tillägg för analys och övervakning
- `kiali` – service nät instrument panels tillägg

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Du kan också få mer inblick i installationen genom att titta på loggarna för Istio-operatören.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Om `istio-ingressgateway` visar en extern IP- `<pending>` adress väntar du några minuter tills en IP-adress har tilldelats av Azure-nätverk.

Alla poddar bör visa status för `Running` . Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-add-ons"></a>Åtkomst till tilläggen

Ett antal tillägg har installerats av Istio-operatorn som tillhandahåller ytterligare funktioner. Webb programmen för tilläggen visas **inte** offentligt via en extern IP-adress. 

Använd kommandot för att få åtkomst till användar gränssnitten för tillägg `istioctl dashboard` . Det här kommandot använder [kubectl Port-Forward][kubectl-port-forward] och en slumpmässig port för att skapa en säker anslutning mellan klient datorn och den relevanta Pod i ditt AKS-kluster. Sedan öppnas webb programmet för tillägg automatiskt i din standard webbläsare.

### <a name="grafana"></a>Grafana

Instrument paneler för analys och övervakning av Istio tillhandahålls av [Grafana][grafana]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Grafana-hemligheten tidigare när du uppmanas till det. Öppna Grafana-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Mått för Istio tillhandahålls av [Prometheus][prometheus]. Öppna Prometheus-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Spårning i Istio tillhandahålls av [Jaeger][jaeger]. Öppna Jaeger-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

En instrument panel för service nätets instrument panel tillhandahålls av [Kiali][kiali]. Kom ihåg att använda de autentiseringsuppgifter som du skapade via Kiali-hemligheten tidigare när du uppmanas till det. Öppna Kiali-instrumentpanelen på ett säkert sätt:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Det finns ett enkelt gränssnitt till [mottagare][envoy] -proxyservrarna. Den innehåller konfigurations information och mått för en mottagare-proxy som körs i en angiven pod. Öppna mottagare-gränssnittet på ett säkert sätt enligt följande:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Avinstallera Istio från AKS

> [!WARNING]
> Om du tar bort Istio från ett system som körs kan det leda till trafik problem mellan dina tjänster. Se till att du har gjort att systemet fortfarande fungerar korrekt utan Istio innan du fortsätter.

### <a name="remove-istio"></a>Ta bort Istio

Ta bort Istio från ditt AKS-kluster genom att ta bort `IstioOperator` resursen med namnet `istio-control-plane` som vi lade till tidigare. Istio-operatorn identifierar att Istio operatörs specifikationen har tagits bort och tar sedan bort alla tillhör ande Istio-komponenter.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Du kan köra följande för att kontrol lera när alla Istio-komponenter har tagits bort.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Ta bort Istio-operator

När Istio har avinstallerats kan du också ta bort Istio-operatorn.

```bash
istioctl operator remove
```

Ta sedan bort `istio-` namn områdena.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Nästa steg

För att utforska fler installations-och konfigurations alternativ för Istio, se följande officiella Istio-vägledning:

- [Istio – installations guider][istio-installation-guides]

Du kan också följa ytterligare scenarier som använder:

- [Exempel på Istio Bookinfo-program][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
