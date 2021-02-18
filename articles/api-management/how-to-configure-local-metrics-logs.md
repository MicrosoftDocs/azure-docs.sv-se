---
title: Konfigurera lokala mått och loggar för Azure API Management egen värd-Gateway | Microsoft Docs
description: Lär dig hur du konfigurerar lokala mått och loggar för Azure API Management egen värd-gateway på en Kubernetes Custer
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594174"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurera lokala mått och loggar för Azure API Management egen värd-Gateway

Den här artikeln innehåller information om hur du konfigurerar lokala mått och loggar för den [självbetjänings-Gateway](./self-hosted-gateway-overview.md) som distribueras i ett Kubernetes-kluster. Information om hur du konfigurerar moln mått och loggar finns i [den här artikeln](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Mått
Den egna värdbaserade gatewayen stöder [statistik](https://github.com/statsd/statsd), som har blivit ett enhetligt protokoll för insamling och insamling av mått. Det här avsnittet går igenom stegen för att distribuera statistik till Kubernetes, konfigurera gatewayen för att generera mått via statistik och använda [Prometheus](https://prometheus.io/) för att övervaka måtten. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Distribuera statistik och Prometheus till klustret

Nedan visas en exempel på YAML-konfiguration för att distribuera statistik och Prometheus till Kubernetes-klustret där en lokal gateway distribueras. Dessutom skapas en [tjänst](https://kubernetes.io/docs/concepts/services-networking/service/) för var och en. Den egen värdbaserade gatewayen kommer att publicera mått till den statistikbaserade tjänsten. Vi kommer att få åtkomst till Prometheus-instrumentpanelen via tjänsten.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Spara konfigurationerna till en fil med namnet `metrics.yaml` och Använd kommandot nedan för att distribuera allt till klustret:

```console
kubectl apply -f metrics.yaml
```

När distributionen har slutförts kör du kommandot nedan för att kontrol lera att poddar körs. Observera att ditt Pod-namn är annorlunda. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Kör kommandot nedan för att kontrol lera att tjänsterna körs. Anteckna `CLUSTER-IP` och `PORT` av tjänsten med statistik, vi behöver den senare. Du kan gå till Prometheus-instrumentpanelen med hjälp av dess `EXTERNAL-IP` och `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Konfigurera den egen värdbaserade gatewayen för att generera mått

Nu när både statistik-och Prometheus har distribuerats kan vi uppdatera konfigurationerna för den egna värdbaserade gatewayen för att börja generera mått via statistik. Funktionen kan aktive ras eller inaktive ras med `telemetry.metrics.local` nyckeln i ConfigMap för lokal gateway-distribution med ytterligare alternativ. Nedan visas en uppdelning av tillgängliga alternativ:

| Fält  | Standardvärde | Description |
| ------------- | ------------- | ------------- |
| telemetri. Metrics. local  | `none` | Aktiverar loggning via statistik. Värdet kan vara `none` , `statsd` . |
| telemetri. Metrics. local. statal. Endpoint  | saknas | Anger den statistikbaserade slut punkten. |
| telemetri. Metrics. local. statal. sampling  | saknas | Anger samplings frekvens för mått. Värdet kan vara mellan 0 och 1. t. ex., `0.5`|
| telemetri. Metrics. local. statal. tag-format  | saknas | [Etikett format](https://github.com/prometheus/statsd_exporter#tagging-extensions)för statistik för exportör. Värdet kan vara `none` , `librato` , `dogStatsD` , `influxDB` . |

Här är en exempel konfiguration:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Uppdatera YAML-filen för den egna värdbaserade Gateway-distributionen med ovanstående konfigurationer och tillämpa ändringarna med kommandot nedan: 

```console
kubectl apply -f <file-name>.yaml
 ```

Om du vill hämta de senaste konfigurations ändringarna startar du om Gateway-distributionen med kommandot nedan:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Visa måtten

Nu har vi allt distribuerat och konfigurerat, den egna värdbaserade gatewayen bör rapportera mått via statistik. Prometheus kommer att hämta måtten från statistik. Gå till Prometheus-instrumentpanelen med hjälp av `EXTERNAL-IP` och `PORT` i Prometheus-tjänsten. 

Gör vissa API-anrop via den egen värdbaserade gatewayen, om allt är korrekt konfigurerat, bör du kunna visa nedanstående mått:

| Metric  | Beskrivning |
| ------------- | ------------- |
| Begäranden  | Antal API-begäranden under perioden |
| DurationInMS | Antalet millisekunder från att gatewayen fick begäran till då svaret har skickats fullständigt |
| BackendDurationInMS | Antalet millisekunder som ägnats åt övergripande serverdels-IO (ansluta, skicka och ta emot byte)  |
| ClientDurationInMS | Antalet millisekunder som ägnats åt övergripande klient-IO (ansluta, skicka och ta emot byte)  |

## <a name="logs"></a>Loggar

Den egen värdbaserade gatewayen matar ut loggar till `stdout` och `stderr` som standard. Du kan enkelt visa loggarna med följande kommando:

```console
kubectl logs <pod-name>
```

Om din egen värdbaserade Gateway distribueras i Azure Kubernetes-tjänsten kan du aktivera [Azure Monitor för behållare](../azure-monitor/containers/container-insights-overview.md) för att samla in `stdout` och `stderr` från dina arbets belastningar och visa loggarna i Log Analytics. 

Den egna värdbaserade gatewayen stöder också ett antal protokoll `localsyslog` , inklusive, `rfc5424` och `journal` . I tabellen nedan sammanfattas alla alternativ som stöds. 

| Fält  | Standardvärde | Description |
| ------------- | ------------- | ------------- |
| telemetri. logs. STD  | `text` | Aktiverar loggning till standard strömmar. Värdet kan vara `none` , `text` , `json` |
| telemetri. logs. local  | `none` | Aktiverar lokal loggning. Värdet kan vara `none` ,,, `auto` `localsyslog` `rfc5424` , `journal`  |
| telemetri. logs. local. localsyslog. Endpoint  | saknas | Anger localsyslog-slutpunkt.  |
| telemetri. logs. local. localsyslog. Facility  | saknas | Anger localsyslog [Facility-kod](https://en.wikipedia.org/wiki/Syslog#Facility). t. ex., `7` 
| telemetri. logs. local. rfc5424. Endpoint  | saknas | Anger rfc5424-slutpunkt.  |
| telemetri. logs. local. rfc5424. Facility  | saknas | Anger anläggnings kod per [rfc5424](https://tools.ietf.org/html/rfc5424). t. ex., `7`  |
| telemetri. logs. local. journal. Endpoint  | saknas | Anger Journal slut punkt.  |

Här är en exempel konfiguration av lokal loggning:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
* Lär dig mer om att [Konfigurera och Spara loggar i molnet](how-to-configure-local-metrics-logs.md)
