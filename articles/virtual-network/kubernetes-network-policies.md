---
title: Nätverks principer för Azure Kubernetes | Microsoft Docs
description: Lär dig mer om Kubernetes-nätverks principer för att skydda ditt Kubernetes-kluster.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b7c683edd15ab05e9efc239ffe07759078754607
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222657"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Översikt över Azure Kubernetes Network policies

Nätverks principer ger Micro-segmentering för poddar precis som nätverks säkerhets grupper (NSG: er) tillhandahåller mikrosegmentering för virtuella datorer. Azure Network Policy Manager (även kallat Azure NPM)-implementering stöder standard principen för nätverks principer för Kubernetes. Du kan använda etiketter för att välja en grupp med poddar och definiera en lista med ingångs-och utgångs regler för att filtrera trafik till och från dessa poddar. Läs mer om principerna för Kubernetes-nätverk i [Kubernetes-dokumentationen](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Översikt över Kubernetes-nätverks principer](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM-implementeringen fungerar tillsammans med Azure-CNI som tillhandahåller VNet-integrering för behållare. NPM stöds bara på Linux idag. Implementeringen upprätthåller trafik filtrering genom att konfigurera Tillåt och neka IP-regler i Linux-program varan iptables baserat på de definierade principerna. Dessa regler grupperas tillsammans med Linux-IPSets.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Planera säkerhet för ditt Kubernetes-kluster
När du implementerar säkerheten för klustret ska du använda nätverks säkerhets grupper (NSG: er) för att filtrera trafik som går in och lämnar ditt kluster under nät (Nord-syd-trafik). Använd Azure-NPM för trafik mellan poddar i klustret (öst-väst-trafik).

## <a name="using-azure-npm"></a>Använda Azure-NPM
Azure NPM kan användas på följande sätt för att tillhandahålla Micro-segmentering för poddar.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM finns inbyggt i AKS och kan aktive ras när klustret skapas. Läs mer om det i [skydda trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AKS-motor
AKS-Engine är ett verktyg som genererar en Azure Resource Manager-mall för distributionen av ett Kubernetes-kluster i Azure. Klusterkonfigurationen anges i en JSON-fil som överförs till verktyget när mallen skapas. Läs mer om hela listan med klusterinställningar som stöds och beskrivningar av dessa i Microsoft Azure Container Service Engine - Cluster Definition.

Om du vill aktivera principer för kluster som distribueras med ACS-Engine anger du värdet för inställningen networkPolicy i kluster definitions filen som ska vara "Azure".

#### <a name="example-configuration"></a>Exempelkonfiguration

Konfigurationen för följande JSON-exempel skapar ett nytt virtuellt nätverk och undernät och distribuerar ett Kubernetes-kluster i det med Azure CNI. Vi rekommenderar att du använder anteckningar för att redigera JSON-filen. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Gör det själv (gör det själv) Kubernetes-kluster i Azure
 För gör det själv-kluster installerar du först CNI-plugin-programmet och aktiverar det på varje virtuell dator i ett kluster. Detaljerade anvisningar finns i avsnittet om att [distribuera plugin-programmet för ett Kubernetes-kluster som du distribuerar själv](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

När klustret har distribuerats kör du följande `kubectl` kommando för att ladda ned och använda Azure NPM *daemon-uppsättningen* i klustret.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Lösningen är också öppen källa och koden är tillgänglig på [Azure Container Networking-lagringsplatsen](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Övervaka och visualisera nätverkskonfigurationer med Azure NPM
Azure NPM innehåller informativa Prometheus-mått som gör att du kan övervaka och bättre förstå dina konfigurationer. Den innehåller inbyggda visualiseringar i antingen Azure Portal-eller Grafana Labs. Du kan börja samla in dessa mått med antingen Azure Monitor eller en Prometheus-Server.

### <a name="benefits-of-azure-npm-metrics"></a>Fördelar med Azure NPM-mått
Användare har tidigare bara kunnat lära sig om sin nätverks konfiguration med kommandot `iptables -L` som körs inuti en klusternod, vilket ger en utförlig och svår förståelse för utdata. NPM-mått ger följande fördelar som rör nätverks principer, program varan iptables-regler och IPSets.
- Ger inblick i relationen mellan den tre dimensionen och en tids dimension för att felsöka en konfiguration.
- Antal poster i alla IPSets och varje IPSet.
- Åtgången tid för att tillämpa en princip med granularitet på IPTable/IPSet-nivån.
 
### <a name="supported-metrics"></a>Mått som stöds
Följande är en lista över mått som stöds:

|Måttnamn |Description  |Prometheus Metric-typ  |Etiketter  |
|---------|---------|---------|---------|
|`npm_num_policies`     |Antal nätverks principer          |Mätare         |-         |
|`npm_num_iptables_rules`     | antal program varan iptables-regler     | Mätare        |-         |         
|`npm_num_ipsets`     |antal IPSets         |Mätare            |-         |
|`npm_num_ipset_entries`     |antal IP-adresser i alla IPSets         |Mätare         |-         |
|`npm_add_policy_exec_time`     |körning för att lägga till en nätverks princip         |Sammanfattning         |quantile (0,5, 0,9 eller 0,99)         |
|`npm_add_iptables_rule_exec_time`     |körning för att lägga till en program varan iptables-regel         |Sammanfattning         |quantile (0,5, 0,9 eller 0,99)         |
|`npm_add_ipset_exec_time`     |körning för att lägga till en IPSet         |Sammanfattning         |quantile (0,5, 0,9 eller 0,99)         |
|`npm_ipset_counts` erfar     |antal poster inom varje enskild IPSet         |GaugeVec         |Ange namn & hash         |

Med de olika quantile-nivåerna i "exec_time"-mått kan du skilja mellan de allmänna och värsta fall scenarierna.

Det finns också ett mått för "exec_time_count" och "exec_time_sum" för varje "exec_time" sammanfattnings mått.

Måtten kan kasseras genom Azure Monitor för behållare eller via Prometheus.

### <a name="setup-for-azure-monitor"></a>Installations program för Azure Monitor
Det första steget är att aktivera Azure Monitor för behållare för ditt Kubernetes-kluster. Du hittar stegen i [Azure Monitor for containers Overview](../azure-monitor/insights/container-insights-overview.md). När du har Azure Monitor för behållare aktiverade konfigurerar du [Azure Monitor for containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig) för att aktivera NPM-integrering och insamling av Prometheus NPM-mått. Azure Monitor for containers ConfigMap har ett ```integrations``` avsnitt med inställningar för att samla in NPM-mått. De här inställningarna är inaktiverade som standard i ConfigMap. När du aktiverar den grundläggande inställningen ```collect_basic_metrics = true``` samlas Basic NPM-mått in. Om du aktiverar avancerad inställning ```collect_advanced_metrics = true``` samlas avancerade mått utöver de grundläggande måtten. 

När du har redigerat ConfigMap sparar du det lokalt och tillämpar ConfigMap på klustret på följande sätt.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Nedan visas ett kodfragment från [Azure Monitor för containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig), som visar NPM-integrering som är aktive rad med avancerad mått samling.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Avancerade mått är valfria och när du aktiverar dem aktive ras automatiskt grundläggande mått samling. Avancerade mått omfattar för närvarande endast `npm_ipset_counts`

Läs mer om [Azure Monitor för inställningar för behållare samling i config Map](../azure-monitor/insights/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Visualiserings alternativ för Azure Monitor
När NPM mått samling har Aktiver ATS kan du visa måtten i Azure Portal med hjälp av container Insights eller i Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Visa i Azure Portal under insikter för klustret
Öppna Azure-portalen. När du är i klustrets insikter går du till "arbets böcker" och öppnar "Network Policy Manager (NPM)-konfiguration".

Förutom att Visa arbets boken (bilder nedan), kan du också direkt fråga Prometheus-måtten i "logs" i avsnittet insikter. Den här frågan returnerar till exempel alla mått som samlas in.
| där TimeGenerated > sedan (5H) | där namnet innehåller "npm_"

Du kan också fråga Log Analytics direkt för måtten. Lär dig mer om det med [komma igång med Log Analytics frågor](../azure-monitor/insights/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Visa på Grafana-instrumentpanelen
Konfigurera din Grafana-Server och konfigurera en Log Analytics data källa enligt beskrivningen [här](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Importera sedan [Grafana-instrumentpanelen med en Log Analytics Server](https://grafana.com/grafana/dashboards/10956) del till dina Grafana-labb.

Instrument panelen innehåller visuella objekt som liknar Azure-arbetsboken. Du kan lägga till paneler i diagrammet & visualisera NPM mått från InsightsMetrics-tabellen.

### <a name="setup-for-prometheus-server"></a>Installations program för Prometheus-Server
Vissa användare kan välja att samla in mått med en Prometheus-server i stället för att Azure Monitor för behållare. Du behöver bara lägga till två jobb till din kassations konfiguration för att samla in NPM-mått.

Om du vill installera en enkel Prometheus-Server lägger du till den här Helm-lagrings platsen på klustret
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
Lägg sedan till en server
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
där `prometheus-server-scrape-config.yaml` består av
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Du kan också ersätta `azure-npm-node-metrics` jobbet med innehållet nedan eller infoga det i ett befintligt jobb för Kubernetes poddar:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Visualiserings alternativ för Prometheus
När du använder en Prometheus-Server stöds endast Grafana-instrumentpanelen. 

Om du inte redan har gjort det konfigurerar du Grafana-servern och konfigurerar en Prometheus-datakälla. Importera sedan vår [Grafana-instrumentpanel med en Prometheus-backend](https://grafana.com/grafana/dashboards/13000) till Grafana Labs.

Visualiseringarna för den här instrument panelen är identiska med instrument panelen med en behållar insikter/Log Analytics Server del.

### <a name="sample-dashboards"></a>Exempelinstrumentpaneler
Här följer några exempel på instrument paneler för NPM-mått i container Insights (CI) och Grafana

#### <a name="ci-summary-counts"></a>Antal CI-sammanfattningar
![Antal sammanfattningar av Azure-arbetsböcker](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Antal CI med tiden
[![Antal Azure-arbetsböcker över tid](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet-poster
[![Azure-bokens IPSet-poster](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI runtime-Quantiles
![Quantiles för Azures arbets boks körning](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Sammanfattnings antal för Grafana-instrumentpanelen
![Sammanfattnings antal för Grafana-instrumentpanelen](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana instrument panel räknar över tid
[![Grafana instrument panel räknar över tid](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana-instrumentpanel IPSet poster
[![Grafana-instrumentpanel IPSet poster](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana Dashboard runtime-Quantiles
[![Grafana Dashboard runtime-quantiles](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Kubernetes-tjänsten](../aks/intro-kubernetes.md).
-  Lär dig mer om [behållar nätverk](container-networking-overview.md).
- [Distribuera plugin-programmet](deploy-container-networking.md) för Kubernetes-kluster eller Docker-behållare.

