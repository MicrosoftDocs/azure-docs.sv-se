---
title: Visa Azure Kubernetes service (AKS)-styrenhets loggar
description: Lär dig hur du aktiverar och visar loggarna för Kubernetes-kontroll planet i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 7fce3db6f3636a5ee984c8be44f877c5636f4e16
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948969"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Aktivera och granska Kubernetes Control plan-loggar i Azure Kubernetes service (AKS)

Med Azure Kubernetes service (AKS) tillhandahålls kontroll Plans komponenter som *Kube-apiserver* och *Kube-Controller Manager* som en hanterad tjänst. Du skapar och hanterar noderna som kör *kubelet* -och container Runtime och distribuerar dina program via den hanterade Kubernetes API-servern. För att felsöka ditt program och tjänster kan du behöva visa loggarna som genereras av dessa kontroll Plans komponenter. Den här artikeln visar hur du använder Azure Monitor loggar för att aktivera och skicka frågor till loggarna från Kubernetes Control plan-komponenter.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att ett befintligt AKS-kluster körs i ditt Azure-konto. Om du inte redan har ett AKS-kluster kan du skapa ett med hjälp av [Azure CLI][cli-quickstart] eller [Azure Portal][portal-quickstart]. Azure Monitor loggar fungerar med både Kubernetes RBAC, Azure RBAC och icke-RBAC-aktiverade AKS-kluster.

## <a name="enable-resource-logs"></a>Aktivera resursloggar

För att hjälpa till att samla in och granska data från flera källor, innehåller Azure Monitor loggar ett frågespråk och analys motor som ger insikter om din miljö. En arbets yta används för att sortera och analysera data och kan integreras med andra Azure-tjänster, till exempel Application Insights och Security Center. Om du vill använda en annan plattform för att analysera loggarna kan du istället välja att skicka resurs loggar till ett Azure Storage-konto eller händelsehubben. Mer information finns i [Vad är Azure Monitor loggar?][log-analytics-overview].

Azure Monitor loggar aktive ras och hanteras i Azure Portal. Om du vill aktivera logg insamling för Kubernetes Control plan-komponenter i ditt AKS-kluster öppnar du Azure Portal i en webbläsare och utför följande steg:

1. Välj resurs grupp för ditt AKS-kluster, till exempel *myResourceGroup*. Välj inte den resurs grupp som innehåller de enskilda AKS-kluster resurserna, t. ex. *MC_myResourceGroup_myAKSCluster_eastus*.

2. Välj **diagnostikinställningar** på vänster sida.

3. Välj ditt AKS-kluster, till exempel *myAKSCluster*, och välj sedan att **lägga till diagnostikinställningar**.
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Skärm bild av Azure Portal i ett webbläsarfönster som visar diagnostikinställningar, med alternativet Lägg till diagnostisk inställning ska väljas":::

4. Ange ett namn, till exempel *myAKSClusterLogs*, och välj sedan alternativet att **Skicka till Log Analytics arbets yta**.

5. Välj en befintlig arbets yta eller skapa en ny. Om du skapar en arbets yta anger du ett namn på arbets ytan, en resurs grupp och en plats.

6. I listan över tillgängliga loggar väljer du de loggar som du vill aktivera. I det här exemplet aktiverar du loggarna *Kube-audit* och *Kube-audit-admin* . Vanliga loggar omfattar *Kube-apiserver*, *Kube-Controller-Manager* och *Kube-Scheduler*. Du kan returnera och ändra de insamlade loggarna när Log Analytics arbets ytor har Aktiver ATS.

7. När du är klar väljer du **Spara** för att aktivera insamling av de valda loggarna.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Skärm bild av Azure Portal ' Lägg till diagnostikinställningar '. Målet för &quot;Skicka till Log Analytics-arbetsytan&quot; och loggarna &quot;Kube-audit&quot; och &quot;Kube-audit-admin&quot; är markerade":::

## <a name="log-categories"></a>Logg kategorier

Förutom poster som skrivs av Kubernetes har projektets gransknings loggar också poster från AKS.

Gransknings loggar registreras i tre kategorier: *Kube-audit*, *Kube-audit-admin* och *Guard*.

- Kategorin *Kube-audit* innehåller alla Gransknings logg data för varje gransknings händelse, inklusive *Hämta*, *lista*, *skapa*, *Uppdatera*, *ta bort*, *korrigera* och *publicera*.
- Kategorin *Kube-audit-admin* är en del av *Kube-granskningens* logg kategori. *Kube-audit-admin* minskar antalet loggar avsevärt genom att exkludera gransknings händelserna *Get* och *list* från loggen.
- *Skydds* kategorin är hanterad Azure AD-och Azure RBAC-granskningar. För hanterad Azure AD:-token i är användar information ut. För Azure RBAC: åtkomst granskningar in och ut.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Schemalägg en test-Pod på AKS-klustret

Skapa en del loggar genom att skapa en ny Pod i ditt AKS-kluster. Följande exempel på YAML-manifest kan användas för att skapa en grundläggande NGINX-instans. Skapa en fil med `nginx.yaml` ett valfritt redigerings program och klistra in följande innehåll:

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

Skapa Pod med kommandot [kubectl Create][kubectl-create] och ange yaml-filen, som du ser i följande exempel:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visa insamlade loggar

Det kan ta upp till 10 minuter innan diagnostikloggar är aktiverade och visas.

> [!NOTE]
> Om du behöver alla Gransknings logg data för efterlevnad eller andra syfte kan du samla in och lagra den i ett billigt lagrings utrymme, till exempel Blob Storage. Använd logg kategorin *Kube-audit-admin* för att samla in och spara en meningsfull uppsättning Gransknings logg data för övervaknings-och aviserings syfte.

I Azure Portal navigerar du till ditt AKS-kluster och väljer **loggar** på den vänstra sidan. Stäng fönstret *exempel frågor* om det visas.

På den vänstra sidan väljer du **loggar**. Om du vill visa *Kube-gransknings* loggar anger du följande fråga i text rutan:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Många loggar returneras troligt vis. Om du vill begränsa frågan till att visa loggarna om NGINX-Pod som skapades i föregående steg, lägger du till ytterligare en *WHERE* -instruktion för att söka efter *nginx* som visas i följande exempel fråga:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Om du vill visa *Kube-audit-admin-* loggar anger du följande fråga i text rutan:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

I det här exemplet visar frågan alla skapa jobb i *Kube-audit-admin*. Det finns troligen många resultat som returneras, för att begränsa frågan till att visa loggarna om de NGINX-Pod som skapades i föregående steg, Lägg till ytterligare en *WHERE* -instruktion för att söka efter *nginx* enligt följande exempel fråga.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Mer information om hur du frågar och filtrerar logg data finns i [Visa eller analysera data som samlas in med Log Analytics-loggs ökning][analyze-log-analytics].

## <a name="log-event-schema"></a>Logga händelse schema

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

## <a name="log-roles"></a>Logg roller

| Roll                     | Beskrivning |
|--------------------------|-------------|
| *aksService*             | Visnings namnet i gransknings loggen för åtgärden kontroll plan (från hcpService) |
| *masterclient*           | Visnings namnet i gransknings loggen för MasterClientCertificate, certifikatet du får från AZ AKS get-credentials |
| *nodeclient*             | Visnings namnet för ClientCertificate, som används av agent-noder |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och granskar loggarna för Kubernetes Control plan-komponenterna i ditt AKS-kluster. För att övervaka och felsöka ytterligare kan du också [Visa Kubelet-loggarna][kubelet-logs] och [Aktivera åtkomst till SSH-noden][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
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