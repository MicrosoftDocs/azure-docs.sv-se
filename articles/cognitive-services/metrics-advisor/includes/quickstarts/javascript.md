---
title: Snabb start för Metrics Advisor JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 668255486b1c53c062907aba9a679cf7a84bc3ca
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948367"
---
[Referens dokumentation](/java/api/overview/azure/ai-metricsadvisor-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/ai-metrics-advisor)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har en Azure-prenumeration kan du <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" skapa en Metrics Advisor-resurs "  target="_blank"> som skapar en Metric Advisor-resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att distribuera din Metric Advisor-instans.  
* Din egen SQL-databas med Time Series-data.
  
> [!TIP]
> * Du hittar exempel på JavaScript Metrics Advisor på [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples).
> * Det kan ta 10 till 30 minuter för din Metric Advisor-resurs att distribuera en tjänst instans som du kan använda. Klicka på **gå till resurs** när den har distribuerats. Efter distributionen kan du börja använda Metric Advisor-instansen med både webb portalen och REST API. 
> * Du hittar URL: en för REST API i Azure Portal i avsnittet **Översikt** i din resurs. Resultatet ser ut så här:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `@azure/ai-metrics-advisor` NPM-paketet:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

Appens `package.json` fil kommer att uppdateras med beroenden.

Skapa en fil med namnet `index.js` och importera följande bibliotek:

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript), som innehåller kod exemplen i den här snabb starten.

Skapa variabler för resursens Azure-slutpunkt och nyckel. 

> [!IMPORTANT]
> Gå till Azure-portalen. Om den mått Advisor-resurs som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar prenumerations nycklar och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. <br><br>För att hämta din API-nyckel måste du gå till [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Välj lämplig: **katalog**, **prenumeration** och **arbets yta** för resursen och välj **Kom igång**. Du kommer sedan att kunna hämta dina API-nycklar från [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i JavaScript-SDK: n för Metrics Advisor.

|Name|Beskrivning|
|---|---|
| MetricsAdvisorClient | **Används för**: <br> – Visar incidenter <br> – Visar rotor saken till incidenter <br> -Hämtar ursprungliga tids serie data och tids serie data som har berikats av tjänsten. <br> – Visa aviseringar <br> – Lägga till feedback för att finjustera din modell |
| MetricsAdvisorAdministrationClient | **Gör att du kan:** <br> – Hantera datafeeds <br> -Skapa, konfigurera, Hämta, lista och ta bort avvikelser för varningar <br> -Hantera hookar  |
| DataFeed | **Vilka mått rådgivare inhämtar från data källan. A `DataFeed` innehåller rader med:** <br> – Tidsstämplar <br> -Noll eller flera dimensioner <br> -En eller flera mått  |
| DataFeedMetric | A `DataFeedMetric` är ett mätbart mått som används för att övervaka och utvärdera status för en enskild affärs process. Det kan vara en kombination av flera tids serie värden indelade i dimensioner. Till exempel kan ett webb hälso mått innehålla dimensioner för antal användare och en-US-marknad. |

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med klient biblioteket Metrics Advisor för Java Script:

* [Autentisera klienten](#authenticate-the-client)
* [Lägg till en datafeed](#add-a-data-feed)
* [Kontrol lera inmatnings status](#check-ingestion-status)
* [Konfigurera avvikelse identifiering](#configure-anomaly-detection)
* [Skapa en Hook](#create-a-hook)
* [Skapa en aviserings konfiguration](#create-an-alert-configuration)
* [Fråga aviseringen](#query-the-alert)

## <a name="authenticate-the-client"></a>Autentisera klienten

När du har två nycklar och slut punkts adress kan du använda klassen MetricsAdvisorKeyCredential för att autentisera klienterna på följande sätt:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Lägg till en datafeed

Mått Advisor stöder anslutning av olika typer av data källor. Här är ett exempel på att mata in data från SQL Server.

Ersätt `sql_server_connection_string` med din egen SQL Server-anslutningssträng och Ersätt `query` med en fråga som returnerar dina data med en enda tidsstämpel. Du måste också justera `metric` `dimension` värdena och utifrån dina anpassade data.

> [!IMPORTANT]
> Frågan ska returnera högst en post för varje dimensions kombination vid varje tidsstämpel. Och alla poster som returneras av frågan måste ha samma tidsstämplar. Metrics Advisor kör den här frågan för varje tidsstämpel för att mata in dina data. Mer information och exempel finns i [avsnittet Vanliga frågor och svar om frågor](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>Kontrol lera inmatnings status

När vi har startat data inmatningen kan vi kontrol lera inmatnings statusen.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurera avvikelse identifiering

Vi behöver en konfiguration för avvikelse identifiering för att avgöra om en punkt i tids serien är en avvikelse. Även om en standard identifierings konfiguration automatiskt tillämpas på varje mått, kan du finjustera identifierings lägena som används på dina data genom att skapa en anpassad konfiguration för avvikelse identifiering.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>Skapa en Hook

Vi använder hookar för att prenumerera på aviseringar i real tid. I det här exemplet skapar vi en webhook för tjänsten Metrics Advisor som skickar aviseringen till.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Skapa en aviserings konfiguration

I det här exemplet visas hur du konfigurerar villkor som en avisering måste utlösas och vilka hookar som ska användas som mål för en avisering.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>Fråga aviseringen

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```
