---
title: Självstudie – ansluta och övervaka Azure Synapse Spark-programnivå mått
description: Självstudie – lär dig hur du integrerar din befintliga lokala Prometheus-server med Azure Synapse-arbetsytan för Azure Spark-programstatistik i nära real tid med hjälp av Synapse Prometheus-anslutaren.
services: synapse-analytics
author: julieMSFT
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 956d2f8f14b224609542783a8b18d3cdee43fff9
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035633"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Självstudie: ansluta och övervaka Azure Synapse Spark-programnivå mått

## <a name="overview"></a>Översikt

I den här självstudien får du lära dig hur du integrerar din befintliga lokala Prometheus-server med Azure Synapse-arbetsytan för Azure Spark-programstatistik i nära real tid med hjälp av Synapse Prometheus-anslutaren. 

I den här kursen introduceras även API: er för Azure Synapse REST-mått. Du kan hämta Spark-programstatistik data via REST-API: er för att bygga ditt eget övervaknings-och diagnos verktyg eller integrera med övervaknings systemen.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Använd Azure Synapse Prometheus Connector för dina lokala Prometheus-servrar

[Azure Synapse Prometheus Connector](https://github.com/microsoft/azure-synapse-spark-metrics) är ett projekt med öppen källkod. Synapse Prometheus-anslutningsprogrammet använder en filbaserad tjänst identifierings metod som gör att du kan:
 - Autentisera till Synapse-arbetsyta via ett AAD-tjänstens huvud namn.
 - Hämta arbets ytans Apache Spark program lista. 
 - Hämta Spark-programmått via Prometheus-baserad konfiguration. 

### <a name="1-prerequisite"></a>1. förutsättning

Du måste ha en Prometheus-Server distribuerad på en virtuell Linux-dator.

### <a name="2-create-a-service-principal"></a>2. skapa ett huvud namn för tjänsten

Om du vill använda Azure Synapse Prometheus-anslutningen i din lokala Prometheus-Server bör du följa stegen nedan för att skapa ett huvud namn för tjänsten.

#### <a name="21-create-a-service-principal"></a>2,1 Skapa ett huvud namn för tjänsten:

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

Anteckna appId, Password och klient-ID.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 Lägg till motsvarande behörigheter till tjänstens huvud namn som skapades i ovanstående steg.

![skärm bilds beviljande behörighet Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Logga in på din [Azure Synapse Analytics-arbetsyta](https://web.azuresynapse.net/) som Synapse-administratör

2. I Synapse Studio väljer du **hantera > åtkomst kontroll** i rutan till vänster.

3. Klicka på knappen Lägg till längst upp till vänster för att **lägga till en roll tilldelning**

4. För omfång väljer du **arbets yta**

5. För roll väljer du **Synapse Compute operator**

6. För Välj användare, ange **<service_principal_name>** och klicka på tjänstens huvud namn

7. Klicka på **Använd** (vänta 3 minuter tills behörigheten börjar gälla.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Hämta Azure Synapse Prometheus-anslutningen

Använd kommandona för att installera Azure Synapse Prometheus-anslutningen.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. skapa en konfigurations fil för Azure Synapse-arbetsytor

Skapa en config. yaml-fil i mappen config och fyll i följande fält: workspace_name tenant_id service_principal_name och service_principal_password.
Du kan lägga till flera arbets ytor i yaml-konfigurationen.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. uppdatera Prometheus-konfigurationen

Lägg till följande konfigurations avsnitt i Prometheus-scrape_config och ersätt <your_workspace_name> till arbets ytans namn och <path_to_synapse_connector> till din klonade Synapse-Prometheus-mapp

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Starta anslutningen i Prometheus-serverns virtuella dator

Starta en kopplings server på den virtuella Prometheus-servern på följande sätt.

```
python main.py
```

Vänta några sekunder och anslutningen bör börja fungera. Och du kan se "Synapse-Prometheus-Connector" på sidan Prometheus Service Discovery.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Använd Azure Synapse Prometheus eller REST Metrics-API: er för att samla in mått data

### <a name="1-authentication"></a>1. autentisering
Du kan använda flödet för klientautentiseringsuppgifter för att hämta en åtkomsttoken. För att få åtkomst till Metrics-API: et ska du skaffa en Azure AD-åtkomsttoken för tjänstens huvud namn, som har rätt behörighet för att komma åt API: erna.

| Parameter     | Krävs | Beskrivning                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Sant     | Ditt Azure Service huvud namn (program) klient organisations-ID                                                          |
| grant_type    | Sant     | Anger den begärda anslags typen. I ett flöde för autentiseringsuppgifter för klientautentiseringsuppgifter måste värdet vara client_credentials. |
| client_id     | Sant     | Program-ID: t (tjänstens huvud namn) för det program som du registrerade i Azure Portal eller Azure CLI.        |
| client_secret | Sant     | Hemligheten som genereras för programmet (tjänstens huvud namn)                                                  |
| resource      | Sant     | Synapse-resurs-URI: n måste vara https://dev.azuresynapse.net                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Svar ser ut så här:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Visa en lista med program som körs i Azure dataSynapses-arbetsytan

Om du vill hämta en lista över Spark-program för en Synapse-arbetsyta kan du följa den här dokument [övervakningen – Hämta Spark jobb lista](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. samla in Spark-programmått med Prometheus eller REST-API: er


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Samla in Spark-programmått med Prometheus-API: et

Hämta de senaste måtten för det angivna Spark-programmet med Prometheus API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parameter          | Krävs | Beskrivning                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| slutpunkt           | Sant     | Slut punkten för utveckling av arbets ytor, till exempel https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Sant     | Giltig API-version för begäran. För närvarande är det 2019-11-01 – för hands version                    |
| sparkPoolName      | Sant     | Namn på Spark-poolen.                                                                   |
| sessionId          | Sant     | Identifierare för sessionen.                                                               |
| sparkApplicationId | Sant     | Spark-program-ID                                                                      |

Exempel förfrågan: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Exempel svar:

Status kod: 200-svar ser ut så här:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Samla in Spark-programmått med REST API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parameter          | Krävs | Beskrivning                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| slutpunkt           | Sant     | Slut punkten för utveckling av arbets ytor, till exempel https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Sant     | Giltig API-version för begäran. För närvarande är det 2019-11-01 – för hands version                    |
| sparkPoolName      | Sant     | Namn på Spark-poolen.                                                                   |
| sessionId          | Sant     | Identifierare för sessionen.                                                               |
| sparkApplicationId | Sant     | Spark-program-ID                                                                      |

Exempelförfrågan

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Status kod för exempel svar: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Bygg egna verktyg för diagnostik och övervakning

Prometheus-API: et och REST-API: erna ger omfattande statistik data om Spark-programmet som kör information. Du kan samla in programrelaterade mått data via API för Prometheus och REST-API: er. Och utveckla dina egna diagnos-och övervaknings verktyg som passar dina behov bättre.
