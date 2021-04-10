---
title: Konfigurera Prometheus-integrering för container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera container Insights-agenten att kassera mått från Prometheus med ditt Kubernetes-kluster.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 149cdc8613d5034989c7660608a29309353cdabe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109649"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Konfigurera kasse ring av Prometheus-mått med container Insights

[Prometheus](https://prometheus.io/) är en populär lösning för övervakning av mått med öppen källkod och är en del av [molnet Native Compute Foundation](https://www.cncf.io/). Behållar insikter ger en sömlös onboarding-upplevelse för insamling av Prometheus-mått. För att använda Prometheus måste du normalt konfigurera och hantera en Prometheus-server med en butik. Genom att integrera med Azure Monitor behövs ingen Prometheus-Server. Du behöver bara exponera Prometheus-slutpunkten genom dina exportörer eller poddar (programmet) och agentens agent för behållar insikter kan kassera måtten åt dig. 

![Arkitektur för övervakning av behållare för Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Den lägsta agent version som stöds för att kassera Prometheus-mått är ciprod07092019 eller senare och agent versionen som stöds för att skriva konfigurations-och agent fel i `KubeMonAgentEvents` tabellen är ciprod10112019. För Azure Red Hat OpenShift och Red Hat OpenShift v4, agent version ciprod04162020 eller högre. 
>
>Mer information om agent versionerna och vad som ingår i varje utgåva finns i [agent viktig information](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). 
>Om du vill verifiera agent versionen går du till fliken **Node** och väljer en nod. i rutan Egenskaper noterar du värdet för egenskapen för **agentens avbildnings tag** .

Det finns stöd för att kassera Prometheus-mått med Kubernetes-kluster som finns på:

- Azure Kubernetes Service (AKS)
- Azure Stack eller lokalt
- Azure Red Hat OpenShift version 3. x
- Azure Red Hat OpenShift och Red Hat OpenShift version 4. x

### <a name="prometheus-scraping-settings"></a>Prometheus OLE-inställningar

Aktiv kasse ring av mått från Prometheus utförs från ett av två perspektiv:

* Klustrad HTTP-URL och identifiera mål från listan över slut punkter för en tjänst. Till exempel K8s-tjänster som Kube-DNS och Kube-State-Metric och Pod-anteckningar som är specifika för ett program. Mått som samlas in i den här kontexten definieras i avsnittet ConfigMap *[Prometheus data_collection_settings. Cluster]*.
* Nod-bred HTTP-URL och identifiera mål från listan över slut punkter för en tjänst. Mått som samlas in i den här kontexten definieras i avsnittet ConfigMap *[Prometheus_data_collection_settings. node]*.

| Slutpunkt | Omfång | Exempel |
|----------|-------|---------|
| Pod-anteckning | Hela klustret | anteckningar <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes-tjänst | Hela klustret | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/slut punkt | Per nod och/eller över hela klustret | `http://myurl:9101/metrics` |

När en URL anges kasserar behållar insikten bara slut punkten. När Kubernetes-tjänsten anges, matchas tjänst namnet med klustrets DNS-server för att hämta IP-adressen och sedan kasseras den lösta tjänsten.

|Omfång | Nyckel | Datatyp | Värde | Beskrivning |
|------|-----|-----------|-------|-------------|
| Hela klustret | | | | Ange en av följande tre metoder för att kassera slut punkter för mått. |
| | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (antingen IP-adress eller giltig URL-sökväg har angetts). Exempel: `urls=[$NODE_IP/metrics]`. ($NODE _IP är en angiven container Insights-parameter och kan användas i stället för nodens IP-adress. Måste vara alla versaler.) |
| | `kubernetes_services` | Sträng | Kommaavgränsad matris | En matris med Kubernetes-tjänster för att kassera mått från Kube-State-Metrics. Fullständigt kvalificerade domän namn måste användas här. Till exempel, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace.svc.cluster.local:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolesk | sant eller falskt | När du har angett `true` i inställningarna för hela klustret kommer container Insights-agenten att kassera Kubernetes-poddar över hela klustret för följande Prometheus-anteckningar:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolesk | sant eller falskt | Aktiverar kassation av pod. `monitor_kubernetes_pods` måste anges till `true` . |
| | `prometheus.io/scheme` | Sträng | http eller https | Används som standard för skrotning över HTTP. Om det behövs anger du till `https` . | 
| | `prometheus.io/path` | Sträng | Kommaavgränsad matris | Den HTTP-resurs Sök väg som måtten ska hämtas från. Om måtten Path inte är det `/metrics` definierar du den med den här anteckningen. |
| | `prometheus.io/port` | Sträng | 9102 | Ange en port att kassera från. Om porten inte har angetts är den standard 9102. |
| | `monitor_kubernetes_pods_namespaces` | Sträng | Kommaavgränsad matris | En lista över tillåtna namn områden för att kassera mått från Kubernetes poddar.<br> Till exempel `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Node-wide | `urls` | Sträng | Kommaavgränsad matris | HTTP-slutpunkt (antingen IP-adress eller giltig URL-sökväg har angetts). Exempel: `urls=[$NODE_IP/metrics]`. ($NODE _IP är en angiven container Insights-parameter och kan användas i stället för nodens IP-adress. Måste vara alla versaler.) |
| Hela noden eller hela klustret | `interval` | Sträng | 60 s | Samlings intervallets standardvärdet är en minut (60 sekunder). Du kan ändra samlingen för antingen *[prometheus_data_collection_settings. node]* och/eller *[prometheus_data_collection_settings. Cluster]* till Time units, t. ex. s, m, h. |
| Hela noden eller hela klustret | `fieldpass`<br> `fielddrop`| Sträng | Kommaavgränsad matris | Du kan ange att vissa mått ska samlas in eller inte från slut punkten genom att ange List rutan Tillåt ( `fieldpass` ) och neka ( `fielddrop` ). Du måste först ange listan över tillåtna. |

ConfigMaps är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Det går inte att ha en annan ConfigMaps över samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera ConfigMap-konfigurations filen för följande kluster:

* Azure Kubernetes Service (AKS)
* Azure Stack eller lokalt
* Azure Red Hat OpenShift version 4. x och Red Hat OpenShift version 4. x

1. [Hämta](https://aka.ms/container-azm-ms-agentconfig) mallen ConfigMap yaml File och spara den som container-AZM-MS-agentconfig. yaml.

   >[!NOTE]
   >Det här steget krävs inte när du arbetar med Azure Red Hat OpenShift eftersom ConfigMap-mallen redan finns i klustret.

2. Redigera ConfigMap yaml-filen med dina anpassningar för att kassera Prometheus-mått.

    >[!NOTE]
    >Om du redigerar ConfigMap yaml-filen för Azure Red Hat OpenShift ska du först köra kommandot `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` för att öppna filen i en text redigerare.

    >[!NOTE]
    >Följande anteckning `openshift.io/reconcile-protect: "true"` måste läggas till under metadata för *container-AZM-MS-agentconfig* ConfigMap för att förhindra avstämning. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - För att samla in Kubernetes Services Cluster-wide konfigurerar du ConfigMap-filen med hjälp av följande exempel.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Om du vill konfigurera kassation av Prometheus-mått från en viss URL i klustret konfigurerar du ConfigMap-filen med hjälp av följande exempel.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Om du vill konfigurera kasse ring av Prometheus-mått från en agents DaemonSet för varje enskild nod i klustret konfigurerar du följande i ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP är en angiven container Insights-parameter och kan användas i stället för nodens IP-adress. Det måste vara alla versaler. 

    - Utför följande steg för att konfigurera kasse ring av Prometheus-mått genom att ange en POD-anteckning:

       1. I ConfigMap anger du följande:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Ange följande konfiguration för Pod-anteckningar:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Om du vill begränsa övervakningen till vissa namn områden för poddar som har anteckningar, till exempel bara inkludera poddar som är dedikerade för produktions arbets belastningar, anger `monitor_kubernetes_pod` du till `true` i ConfigMap och lägger till namn områdes filtret `monitor_kubernetes_pods_namespaces` som anger de namn områden som ska tas med i. Till exempel `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Kör följande kubectl-kommando: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Konfigurera och distribuera ConfigMaps – Azure Red Hat OpenShift v3

Det här avsnittet innehåller krav och steg för att konfigurera konfigurations filen för ConfigMap för Azure Red Hat OpenShift v3. x-kluster.

>[!NOTE]
>För Azure Red Hat OpenShift v3. x skapas en mall ConfigMap-fil i namn området *OpenShift – Azure-Logging* . Den har inte kon figurer ATS för att aktivt kassera mått eller data insamling från agenten.

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar bekräftar du att du är medlem i rollen kund kluster administratör för ditt Azure Red Hat OpenShift-kluster för att konfigurera behållarens agent och Prometheus. Kontrol lera att du är medlem i gruppen *OSA-Customer admins* genom att köra följande kommando:

``` bash
  oc get groups
```

Utdata ser ut ungefär så här:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Om du är medlem i gruppen *OSA-Customer admins* bör du kunna lista `container-azm-ms-agentconfig` ConfigMap med följande kommando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Utdata ser ut ungefär så här:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Aktivera övervakning

Utför följande steg för att konfigurera din ConfigMap-konfigurationsfil för ditt Azure Red Hat OpenShift v3. x-kluster.

1. Redigera ConfigMap yaml-filen med dina anpassningar för att kassera Prometheus-mått. ConfigMap-mallen finns redan i Red Hat OpenShift v3-klustret. Kör kommandot `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` för att öppna filen i en text redigerare.

    >[!NOTE]
    >Följande anteckning `openshift.io/reconcile-protect: "true"` måste läggas till under metadata för *container-AZM-MS-agentconfig* ConfigMap för att förhindra avstämning. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - För att samla in Kubernetes Services Cluster-wide konfigurerar du ConfigMap-filen med hjälp av följande exempel.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Om du vill konfigurera kassation av Prometheus-mått från en viss URL i klustret konfigurerar du ConfigMap-filen med hjälp av följande exempel.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Om du vill konfigurera kasse ring av Prometheus-mått från en agents DaemonSet för varje enskild nod i klustret konfigurerar du följande i ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP är en angiven container Insights-parameter och kan användas i stället för nodens IP-adress. Det måste vara alla versaler. 

    - Utför följande steg för att konfigurera kasse ring av Prometheus-mått genom att ange en POD-anteckning:

       1. I ConfigMap anger du följande:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Ange följande konfiguration för Pod-anteckningar:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Om du vill begränsa övervakningen till vissa namn områden för poddar som har anteckningar, till exempel bara inkludera poddar som är dedikerade för produktions arbets belastningar, anger `monitor_kubernetes_pod` du till `true` i ConfigMap och lägger till namn områdes filtret `monitor_kubernetes_pods_namespaces` som anger de namn områden som ska tas med i. Till exempel `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Spara ändringarna i redigeraren.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created` .

Du kan visa den uppdaterade ConfigMap genom att köra kommandot `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Använder uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap till klustret och du vill uppdatera det med en nyare konfiguration kan du redigera ConfigMap-filen som du tidigare har använt och sedan använda samma kommandon som tidigare.

För följande Kubernetes-miljöer:

- Azure Kubernetes Service (AKS)
- Azure Stack eller lokalt
- Azure Red Hat OpenShift och Red Hat OpenShift version 4. x

Kör kommandot `kubectl apply -f <configmap_yaml_file.yaml` . 

För ett Azure Red Hat OpenShift v3. x-kluster kör du kommandot `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` för att öppna filen i standard redigeraren för att ändra och sedan spara den.

Konfigurations ändringen kan ta några minuter innan den börjar gälla, och alla omsagent-poddar i klustret kommer att startas om. Omstarten är en rullande omstart för alla omsagent-poddar, inte alla omstart på samma tidpunkt. När omstarterna är klara visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Verifiera konfigurationen

Du kan kontrol lera att konfigurationen har tillämpats på ett kluster genom att använda följande kommando för att granska loggarna från en agent pod: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>Det här kommandot gäller inte för Azure Red Hat OpenShift v3. x-kluster.
> 

Om det finns konfigurations fel från omsagent-poddar visas fel som liknar följande i utdata:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel som rör tillämpning av konfigurations ändringar är också tillgängliga för granskning. Följande alternativ är tillgängliga för att utföra ytterligare fel sökning av konfigurations ändringar och skrot av Prometheus-mått:

- Från en agents Pod loggar med samma `kubectl logs` kommando 
    >[!NOTE]
    >Det här kommandot kan inte användas för Azure Red Hat OpenShift-kluster.
    > 

- Från real tids data (för hands version). Real tids data (för hands version) loggar visar fel som liknar följande:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Från tabellen **KubeMonAgentEvents** i din Log Analytics-arbetsyta. Data skickas varje timme med *varnings* allvarlighets grad för kassations fel och *fel* allvarlighets grad för konfigurations fel. Om det inte finns några fel innehåller posten i tabellen data med allvarlighets grad *information* som inte rapporterar några fel. Egenskapen **Tags** innehåller mer information om Pod och behållar-ID: t där felet inträffade och även första förekomst, senaste förekomst och antal under den senaste timmen.

- För Azure Red Hat OpenShift v3. x och v4. x, kontrollerar du omsagent-loggarna genom att söka i **ContainerLog** -tabellen för att kontrol lera om logg insamling av OpenShift-Azure-Logging är aktiverat.

Fel förhindrar omsagent från att parsa filen, vilket gör att den startas om och använder standard konfigurationen. När du har korrigerat felen i ConfigMap på andra kluster än Azure Red Hat OpenShift v3. x sparar du yaml-filen och använder den uppdaterade ConfigMaps genom att köra kommandot: `kubectl apply -f <configmap_yaml_file.yaml` . 

För Azure Red Hat OpenShift v3. x redigerar och sparar du den uppdaterade ConfigMaps genom att köra kommandot: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Fråga Prometheus Metrics-data

Om du vill visa Prometheus-mått som har övergått av Azure Monitor och eventuella konfigurations-/kassations fel som rapporter ATS av agenten läser du [fråga Prometheus Metric data](container-insights-log-search.md#query-prometheus-metrics-data) and [query config eller kassations fel](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Visa Prometheus-mått i Grafana

Behållar insikter stöder visning av mått som lagras i Log Analytics-arbetsytan på Grafana-instrumentpaneler. Vi har angett en mall som du kan ladda ned från Grafana för [instrument panelen](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till att hjälpa dig att fråga efter ytterligare data från de övervakade klustren för att visualisera anpassade Grafana-instrumentpaneler. 

## <a name="review-prometheus-data-usage"></a>Granska Prometheus data användning

Följande fråga tillhandahålls för att identifiera inmatnings volymen för varje mått storlek i GB per dag för att förstå om den är hög.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

Resultatet kommer att visa resultat som liknar följande:

![Skärm bild visar logg frågeresultaten för data inmatnings volym](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

För att uppskatta vad varje mått storlek i GB är en månad för att förstå om den inmatade mängden data som tas emot i arbets ytan är hög, så tillhandahålls följande fråga.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Resultatet kommer att visa resultat som liknar följande:

![Logga frågeresultaten för data inmatnings volym](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Mer information om hur du övervakar data användning och analys av kostnader finns i [Hantera användning och kostnader med Azure Monitor loggar](../logs/manage-cost-storage.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att konfigurera agent samlings inställningarna för STDOUT-, stderr-och miljövariabler från container-arbetsbelastningar [här](container-insights-agent-config.md).
