---
title: Visa Azure Kubernetes Service (AKS)-kontrollantloggar
description: Lär dig hur du aktiverar och visar loggarna för Kubernetes-kontrollplanet i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767299"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Aktivera och granska Kubernetes-kontrollplansloggar i Azure Kubernetes Service (AKS)

Med Azure Kubernetes Service (AKS) tillhandahålls kontrollplanskomponenter som *kube-apiserver* och *kube-controller-manager* som en hanterad tjänst. Du skapar och hanterar noderna som kör *kubelet* och containerkörningen och distribuerar dina program via den hanterade Kubernetes API-servern. För att felsöka ditt program och dina tjänster kan du behöva visa loggarna som genererats av dessa kontrollplanskomponenter. Den här artikeln visar hur du använder Azure Monitor för att aktivera och fråga loggarna från Kubernetes-kontrollplanskomponenterna.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver ett befintligt AKS-kluster som körs i ditt Azure-konto. Om du inte redan har ett AKS-kluster skapar du ett med hjälp av [Azure CLI][cli-quickstart] eller [Azure Portal][portal-quickstart]. Azure Monitor loggar fungerar med både Kubernetes RBAC, Azure RBAC och icke-RBAC-aktiverade AKS-kluster.

## <a name="enable-resource-logs"></a>Aktivera resursloggar

För att samla in och granska data från flera källor Azure Monitor-loggarna ett frågespråk och en analysmotor som ger insikter till din miljö. En arbetsyta används för att samla in och analysera data och kan integreras med andra Azure-tjänster som Application Insights och Security Center. Om du vill använda en annan plattform för att analysera loggarna kan du i stället välja att skicka resursloggar till ett Azure Storage-konto eller en händelsehubb. Mer information finns i Vad [är Azure Monitor loggar?][log-analytics-overview].

Azure Monitor aktiveras och hanteras i Azure Portal. Om du vill aktivera logginsamling för Kubernetes-kontrollplankomponenterna i ditt AKS-kluster öppnar du Azure Portal i en webbläsare och utför följande steg:

1. Välj resursgruppen för ditt AKS-kluster, till exempel *myResourceGroup.* Välj inte den resursgrupp som innehåller dina enskilda AKS-klusterresurser, till exempel *MC_myResourceGroup_myAKSCluster_eastus*.

2. Välj Diagnostikinställningar till **vänster.**

3. Välj ditt AKS-kluster, till exempel *myAKSCluster, och* välj sedan Lägg **till diagnostikinställning**.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Skärmbild av Azure Portal i ett webbläsarfönster som visar diagnostikinställningar som anger att &quot;Lägg till diagnostikinställning&quot; ska väljas":::

4. Ange ett namn, till exempel *myAKSClusterLogs,* och välj sedan alternativet Skicka **till Log Analytics-arbetsytan**.

5. Välj en befintlig arbetsyta eller skapa en ny. Om du skapar en arbetsyta anger du ett arbetsytenamn, en resursgrupp och en plats.

6. I listan över tillgängliga loggar väljer du de loggar som du vill aktivera. I det här exemplet aktiverar du *loggarna kube-audit* *och kube-audit-admin.* Vanliga loggar är *kube-apiserver,* *kube-controller-manager* och *kube-scheduler*. Du kan returnera och ändra de insamlade loggarna när Log Analytics-arbetsytor har aktiverats.

7. När du är klar väljer **du Spara** för att aktivera insamling av de valda loggarna.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Skärmbild av Azure Portal skärmen Lägg till diagnostikinställning. Målet &quot;Skicka till Log Analytics-arbetsytan&quot; och loggarna &quot;kube-audit&quot; och &quot;kube-audit-admin&quot; har valts":::

## <a name="log-categories"></a>Loggkategorier

Förutom poster som skrivits av Kubernetes har projektets granskningsloggar även poster från AKS.

Granskningsloggar registreras i tre kategorier: *kube-audit,* *kube-audit-admin* och *guard*.

- *Kube-audit-kategorin* innehåller alla granskningsloggdata för varje granskningshändelse, inklusive *get*, *list*, *create*, *update*, *delete*, *patch* och *post*.
- Kategorin *kube-audit-admin* är en delmängd av *kategorin kube-audit* log. *kube-audit-admin* minskar antalet loggar avsevärt genom att exkludera *granskningshändelserna hämta* *och* lista från loggen.
- *Säkerhetskategorin* är hanterade Azure AD- och Azure RBAC-granskningar. För hanterad Azure AD: token in, user info out. För Azure RBAC: åtkomstgranskningar in och ut.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Schemalägg en testpodd i AKS-klustret

Skapa en ny podd i ditt AKS-kluster för att generera vissa loggar. Följande YAML-exempelmanifest kan användas för att skapa en grundläggande NGINX-instans. Skapa en fil med `nginx.yaml` namnet i valfri redigerare och klistra in följande innehåll:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Skapa podden med [kommandot kubectl create][kubectl-create] och ange YAML-filen enligt följande exempel:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visa insamlade loggar

Det kan ta upp till 10 minuter innan diagnostikloggarna aktiveras och visas.

> [!NOTE]
> Om du behöver alla granskningsloggdata för efterlevnad eller andra ändamål samlar du in och lagrar dem i prisvärd lagring, till exempel bloblagring. Använd *loggkategorin kube-audit-admin* för att samla in och spara en meningsfull uppsättning granskningsloggdata för övervakning och avisering.

I Azure Portal navigerar du till ditt AKS-kluster och **väljer** Loggar till vänster. Stäng fönstret *Exempelfrågor* om det visas.

Välj Loggar till **vänster.** Om du vill *visa kube-audit-loggarna* anger du följande fråga i textrutan:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Många loggar returneras troligen. Om du vill begränsa frågan och visa loggarna om NGINX-podden som skapades i föregående steg lägger du till ytterligare en *where-instruktion* för att söka efter *nginx* enligt följande exempelfråga:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Om du vill *visa loggarna kube-audit-admin* anger du följande fråga i textrutan:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

I det här exemplet visar frågan alla jobb som skapas i *kube-audit-admin*. Förmodligen returneras många resultat för att begränsa frågan och visa loggarna om NGINX-podden som skapades i föregående steg. Lägg till ytterligare en where-instruktion för att söka efter *nginx* enligt följande exempelfråga. 

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Mer information om hur du frågar efter och filtrerar dina loggdata finns i Visa eller analysera data som [samlas in med loggsökning i Log Analytics.][analyze-log-analytics]

## <a name="log-event-schema"></a>Schema för logghändelse

AKS loggar följande händelser:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Pulsslag][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Loggroller

| Roll                     | Beskrivning |
|--------------------------|-------------|
| *aksService*             | Visningsnamnet i granskningsloggen för kontrollplansåtgärden (från hcpService) |
| *masterclient*           | Visningsnamnet i granskningsloggen för MasterClientCertificate, certifikatet du får från az aks get-credentials |
| *nodeclient*             | Visningsnamnet för ClientCertificate, som används av agentnoder |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och granskar loggarna för Kubernetes-kontrollplanskomponenterna i ditt AKS-kluster. Om du vill övervaka och felsöka ytterligare kan du även visa [Kubelet-loggarna och][kubelet-logs] aktivera [SSH-nodåtkomst.][aks-ssh]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf