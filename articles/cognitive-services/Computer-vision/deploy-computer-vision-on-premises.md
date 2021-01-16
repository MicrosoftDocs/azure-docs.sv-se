---
title: Använd Visuellt innehåll container med Kubernetes och Helm
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar Visuellt innehåll container med hjälp av Kubernetes och Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: b3e1bb3f418f21c75e29b5a1cad337c6f3c10145
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246646"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Använd Visuellt innehåll container med Kubernetes och Helm

Ett alternativ för att hantera dina Visuellt innehåll behållare lokalt är att använda Kubernetes och Helm. Med Kubernetes och Helm för att definiera en Visuellt innehåll behållar avbildning skapar vi ett Kubernetes-paket. Det här paketet kommer att distribueras till ett Kubernetes-kluster lokalt. Slutligen ska vi utforska hur du testar de distribuerade tjänsterna. Mer information om att köra Docker-behållare utan Kubernetes-dirigering finns i [Installera och köra visuellt innehåll behållare](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller innan du använder Visuellt innehåll behållare lokalt:

| Obligatorisk | Syfte |
|----------|---------|
| Azure-konto | Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][free-azure-account] innan du börjar. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] krävs för att hantera delade autentiseringsuppgifter från behållar registret. Kubernetes krävs också innan Helm, som är Kubernetes Package Manager. |
| Helm CLI | Installera [Helm CLI][helm-install], som används för att installera ett Helm-diagram (definition av container paket). |
| Visuellt innehåll resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure **visuellt innehåll** -resurs och den tillhör ande API-nyckeln slut punkts-URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren.<br><br>**{Api_key}**: en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}**: slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-klustret

Värddatorn förväntas ha ett tillgängligt Kubernetes-kluster. I den här självstudien om hur du [distribuerar ett Kubernetes-kluster](../../aks/tutorial-kubernetes-deploy-cluster.md) kan du få en uppfattning om hur du distribuerar ett Kubernetes-kluster till en värddator. Du hittar mer information om distributioner i Kubernetes- [dokumentationen](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurera Helm för distribution

Börja med att skapa en mapp med namnet *Read*. Klistra sedan in följande YAML-innehåll i en ny fil med namnet `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Om du vill konfigurera standardvärdena för Helm-diagrammet kopierar du och klistrar in följande YAML i en fil med namnet `values.yaml` . Ersätt `# {ENDPOINT_URI}` kommentarerna och `# {API_KEY}` med dina egna värden. Konfigurera resultExpirationPeriod, Redis och RabbitMQ om det behövs.

```yaml
# These settings are deployment specific and users can provide customizations
read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Om `billing` värdena och `apikey` inte har angetts upphör tjänsterna att gälla efter 15 minuter. Verifieringen Miss lyckas på samma sätt eftersom tjänsterna inte är tillgängliga.
> 
> - Om du distribuerar flera Läs behållare bakom en belastningsutjämnare, till exempel, under Docker Compose eller Kubernetes, måste du ha ett externt cacheminne. Eftersom bearbetnings behållaren och behållaren GET Request inte kan vara samma, lagrar ett externt cacheminne resultaten och delar över behållare. Mer information om cacheinställningar finns i [konfigurera visuellt innehåll Docker-behållare](./computer-vision-resource-container-config.md).
>

Skapa en mapp för *mallar* under *Läs* katalogen. Kopiera och klistra in följande YAML i en fil med namnet `deployment.yaml` . `deployment.yaml`Filen fungerar som en Helm-mall.

> Mallar genererar MANIFEST filer, som är YAML-formaterade resurs beskrivningar som Kubernetes kan förstå. [– Helm diagram mal len guide][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  template:
    metadata:
      labels:
        app: read-app       
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

I mappen samma *mallar* kopierar du och klistrar in följande hjälp funktioner i `helpers.tpl` . `helpers.tpl` definierar användbara funktioner som hjälper dig att skapa Helm-mallen.

> [!NOTE]
> Den här artikeln innehåller referenser till termen Slav, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
Mallen anger en belastnings Utjämnings tjänst och distributionen av din behållare/avbildning för läsning.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes-paketet (Helm-diagrammet)

*Helm-diagrammet* innehåller konfigurationen av vilka Docker-avbildningar som ska hämtas från `mcr.microsoft.com` behållar registret.

> Ett [Helm-diagram][helm-charts] är en samling filer som beskriver en relaterad uppsättning Kubernetes-resurser. Ett enkelt diagram kan användas för att distribuera något enkelt, t. ex. en memcached POD eller något komplext, t. ex. en fullständig webbapp med HTTP-servrar, databaser, cacheminnen och så vidare.

De tillhandahållna *Helm-diagrammen* hämtar Docker-avbildningarna av den visuellt innehåll tjänsten och motsvarande tjänst från `mcr.microsoft.com` behållar registret.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Installera Helm-diagrammet på Kubernetes-klustret

För att kunna installera *Helm-diagrammet* måste vi köra [`helm install`][helm-install-cmd] kommandot. Se till att köra kommandot Install från katalogen ovanför `read` mappen.

```console
helm install read ./read
```

Här är ett exempel på utdata som du kan förväntas se från en lyckad installations körning:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes-distributionen kan ta flera minuter att slutföra. För att bekräfta att både poddar och tjänsterna är korrekt distribuerade och tillgängliga, kör du följande kommando:

```console
kubectl get all
```

Du bör förvänta dig att se något som liknar följande utdata:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Distribuera flera v3-behållare i Kubernetes-klustret

Från och med v3 av behållaren kan du använda behållare parallellt på både en aktivitets-och sid nivå.

Per design har varje v3-behållare en dispatcher och en igenkännings arbetare. Dispatchern ansvarar för att dela upp en multi-Page-aktivitet till flera under aktiviteter på en sida. Igenkännings arbetaren är optimerad för att identifiera ett enda sid dokument. Om du vill få parallellitet på sidnivå distribuerar du flera v3-behållare bakom en belastningsutjämnare och låter behållarna dela en universell lagring och kö. 

> [!NOTE] 
> För närvarande stöds endast Azure Storage och Azure-kö. 

Behållaren som tar emot begäran kan dela upp aktiviteten i en enda sida under aktiviteter och lägga till dem i den universella kön. Alla igenkännings arbetare från en mindre upptagen behållare kan använda Enkels Ides under aktiviteter från kön, utföra igenkänning och överföra resultatet till lagringen. Data flödet kan förbättras upp till `n` tider, beroende på hur många behållare som distribueras.

V3-behållaren exponerar API: et för direktmigreringens avsökning under `/ContainerLiveness` sökvägen. Använd följande distributions exempel om du vill konfigurera en direktmigreringens avsökning för Kubernetes. 

Kopiera och klistra in följande YAML i en fil med namnet `deployment.yaml` . Ersätt `# {ENDPOINT_URI}` kommentarerna och `# {API_KEY}` med dina egna värden. Ersätt `# {AZURE_STORAGE_CONNECTION_STRING}` kommentaren med Azure Storage anslutnings sträng. Konfigurera `replicas` till det nummer som du vill ha, som anges `3` i följande exempel.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Kör följande kommando. 

```console
kubectl apply -f deployment.yaml
```

Nedan visas ett exempel på utdata som du kan se från en lyckad distributions körning:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

Det kan ta flera minuter att slutföra distributionen av Kubernetes. För att bekräfta att både poddar och tjänsterna är korrekt distribuerade och tillgängliga, kör du följande kommando:

```console
kubectl get all
```

Du bör se konsolens utdata som liknar följande:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du installerar program med Helm i Azure Kubernetes service (AKS) [finns här][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Cognitive Services-containrar][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
