---
title: Självstudie – övervaka Apache Spark mått på program nivå med Prometheus och Grafana
description: Självstudie – lär dig hur du distribuerar en lösning för Apache Spark program mått till ett AKS-kluster (Azure Kubernetes service) och lär dig hur du integrerar Grafana-instrumentpaneler.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: aebfd4e2fc89ab597d97ef0da60bb4b265d88b96
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696114"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Självstudie: övervaka Apache Spark mått på program nivå med Prometheus och Grafana

## <a name="overview"></a>Översikt

I den här självstudien får du lära dig hur du distribuerar en lösning för Apache Spark program mått till ett AKS-kluster (Azure Kubernetes service) och lär dig hur du integrerar Grafana-instrumentpaneler.

Du kan använda den här lösningen för att samla in och fråga Apache Spark Mät data nära real tid. Med de integrerade Grafana-instrument panelerna kan du diagnostisera och övervaka ditt Apache Spark-program. Käll koden och konfigurationerna har öppnats med öppen källkod på GitHub.

## <a name="prerequisites"></a>Förutsättningar

1.  [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
2.  [Helm-3.30 +](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Eller Använd [Azure Cloud Shell](https://shell.azure.com/), som redan innehåller Azure CLI, Helm-klienten och kubectl.

## <a name="log-in-to-azure"></a>Logga in på Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Skapa en Azure Kubernetes-tjänstinstans (AKS)

Använd Azure CLI-kommandot för att skapa ett Kubernetes-kluster i din prenumeration.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Obs: det här steget kan hoppas över om du redan har ett AKS-kluster.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Skapa ett huvud namn för tjänsten och bevilja behörighet till Synapse-arbetsytan

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Resultatet bör se ut så här:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Anteckna appId, Password och tenantID.

[![skärm bilds beviljande behörighet Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Logga in på din [Azure Synapse Analytics-arbetsyta](https://web.azuresynapse.net/) som Synapse-administratör

2. I Synapse Studio väljer du **hantera > åtkomst kontroll** i rutan till vänster.

3. Klicka på knappen Lägg till längst upp till vänster för att **lägga till en roll tilldelning**

4. För omfång väljer du **arbets yta**

5. För roll väljer du **Synapse Compute operator**

6. För Välj användare, ange **<service_principal_name>** och klicka på tjänstens huvud namn

7. Klicka på **Använd** (vänta 3 minuter tills behörigheten börjar gälla.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Installera Connector, Prometheus-Server, Grafana-instrumentpanel

1. Lägg till Synapse-Charts lagrings platsen till Helm-klienten.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Installera komponenter via Helm-klienten:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Synapse-arbetsytans namn.
 - subscription_id: prenumerations-ID för Synapse-arbetsytan.
 - workspace_resource_group_name: resurs grupps namnet för Synapse-arbetsytan.
 - tenant_id: Synapse-arbetsytans klient-ID.
 - service_principal_app_id: tjänstens huvud namn (appId)
 - service_principal_password: det lösen ord för tjänstens huvud namn som du skapade.

## <a name="log-in-to-grafana"></a>Logga in på Grafana

Hämta standard lösen ordet och adressen till Grafana. Du kan ändra lösen ordet i Grafana-inställningarna.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Hämta tjänstens IP-adress, kopiera & klistra in den externa IP-adressen i webbläsaren och logga in med användar namnet "admin" och lösen ordet.

## <a name="use-grafana-dashboards"></a>Använda Grafana-instrumentpaneler

Hitta Synapse-instrumentpanelen i det övre vänstra hörnet av Grafana-sidan (start-> Synapse arbets yta/Synapse-program), försök att köra en exempel kod i Synapse Studio och vänta några sekunder på att måtten ska dra.

Du kan också använda instrument panelerna "Synapse arbets yta/arbets yta" och "Synapse-arbets yta/Spark-pooler" för att få en översikt över arbets ytan och Apache Spark pooler.

## <a name="uninstall"></a>Avinstallera

Ta bort Helm-kommandot Components by på följande sätt.

```bash
helm delete <release_name> -n <namespace>
```

Ta bort AKS-klustret.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Introduktion till komponenter

Azure Synapse Analytics innehåller ett [Helm-diagram](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) baserat på Prometheus-operatör och Synapse Prometheus-anslutning. Helm-diagrammet innehåller Prometheus-Server, Grafana-Server och Grafana-instrumentpaneler för Apache Spark på program nivå mått. Du kan använda [Prometheus](https://prometheus.io/), ett populärt övervaknings system med öppen källkod för att samla in dessa mått i nära real tid och använda [Grafana](https://github.com/grafana/grafana) för visualisering.

### <a name="synapse-prometheus-connector"></a>Synapse Prometheus-anslutning

Med Synapse Prometheus Connector kan du ansluta Azure Synapse Apache Spark pool och din Prometheus-Server. Den implementerar:

1.  Autentisering: den är AAD-baserad autentisering och kan automatiskt uppdatera AAD-token för tjänstens huvud namn för program identifiering, Mät värden och andra funktioner.
2.  Apache Spark program identifiering: när du skickar program i mål arbets ytan kan Synapse Prometheus Connector automatiskt identifiera dessa program.
3.  Apache Spark metadata för program: samlar in grundläggande programinformation och exporterar data till Prometheus.

Synapse Prometheus-anslutningen släpps som en Docker-avbildning som finns på [Microsoft container Registry](https://github.com/microsoft/containerregistry). Det är öppen källkod och finns i [Azure Synapse Spark Application-mått](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Prometheus-Server

Prometheus är en övervaknings-och aviserings verktyg med öppen källkod. Prometheus graderas från Cloud Native Computing Foundation (CNCF) och blev de facto-standarden för moln intern övervakning. Prometheus kan hjälpa oss att samla in, fråga och lagra enorma mängder tids serie data och det kan enkelt integreras med Grafana. I den här lösningen distribuerar vi Prometheus-komponenten baserat på Helm-diagrammet.

### <a name="grafana-and-dashboards"></a>Grafana och instrument paneler

Grafana är en visualiserings-och analys program med öppen källkod. Du kan fråga, visualisera, Varna på och utforska dina mått. Azure Synapse Analytics innehåller en uppsättning standard instrument paneler för Grafana för att visualisera Apache Spark mått på program nivå.

Instrument panelen "Synapse arbets yta/arbets yta" innehåller en vy på arbets ytans nivå av alla Apache Spark pooler, antal program, processor kärnor osv.

[![skärm bilds arbets yta](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Instrument panelen "Synapse-arbetsyte/Spark-pooler" innehåller måtten för Apache Spark program som körs i den valda Apache Sparks poolen under tids perioden.

[![skärm bilds panel sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Instrument panelen "Synapse arbetsyte/Spark Application" innehåller det valda Apache Spark programmet.

[![skärm bilds instrument panel program](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Mallarna ovan har öppnats med öppen källkod i [Azure Synapse Spark-programmått](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).
