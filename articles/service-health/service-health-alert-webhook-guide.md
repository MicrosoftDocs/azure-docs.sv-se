---
title: Skicka Azure Service Health-meddelanden via Webhooks
description: Skicka personligt anpassade meddelanden om service Health-händelser till ditt befintliga problem hanterings system.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: a70c4fcd01a1a95c598d980004ee60292a6cf24b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594617"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Använd en webhook för att konfigurera hälso aviseringar för problem hanterings system

Den här artikeln visar hur du konfigurerar Azure Service Health aviseringar för att skicka data via webhookar till ditt befintliga meddelande system.

Du kan konfigurera Service Health aviseringar så att du får meddelanden om SMS eller e-post när en Azure-tjänst incident påverkar dig.

Men du kanske redan har ett befintligt externt meddelande system på plats som du föredrar att använda. Den här artikeln beskriver de viktigaste delarna av webhook-nyttolasten. Det beskriver hur du skapar anpassade aviseringar som meddelar dig när relevanta tjänst problem inträffar.

Om du vill använda en förkonfigurerad integrering, se:
* [Konfigurera aviseringar med ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurera aviseringar med PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurera aviseringar med OpsGenie](service-health-alert-webhook-opsgenie.md)

**Titta på en introduktions video:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Konfigurera ett anpassat meddelande med hjälp av Service Health webhook-nyttolasten
Om du vill konfigurera en egen anpassad webhook-integrering måste du parsa JSON-nyttolasten som skickas via Service Health-avisering.

Se [ett exempel](../azure-monitor/alerts/activity-log-alerts-webhook.md) på `ServiceHealth` webhook-nyttolast.

Du kan bekräfta att det är en tjänst hälso avisering genom att titta på `context.eventSource == "ServiceHealth"` . Följande egenskaper är mest relevanta:
- **data. context. activityLog. status**
- **data. context. activityLog. level**
- **data. context. activityLog. subscriptionId**
- **data. context. activityLog. Properties. title**
- **data. context. activityLog. Properties. impactStartTime**
- **data. context. activityLog. Properties. kommunikation**
- **data. context. activityLog. Properties. impactedServices**
- **data. context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Skapa en länk till Service Health instrument panel för en incident
Du kan skapa en direkt länk till din Service Health-instrumentpanel på en stationär eller mobil enhet genom att skapa en specialiserad URL. Använd *trackingId* och de tre första och sista siffrorna i *subscriptionId* i det här formatet:

https <i></i> ://app.Azure.com/h/*&lt; trackingId &gt;* / *&lt; de första tre och sista tre siffrorna i &gt; subscriptionId*

Om ditt *subscriptionId* till exempel är bba14129-e895-429b-8809-278e836ecdb3 och din *trackingId* är 0DET-URB, är din service Health URL:

https <i></i> ://app.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Använd nivån för att identifiera problemets allvarlighets grad
Från lägsta till högsta allvarlighets grad kan egenskapen **Level** i nytto lasten vara *information*, *Varning*, *fel* eller *kritisk*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analysera de berörda tjänsterna för att fastställa incident omfånget
Service Health aviseringar kan meddela dig om problem i flera regioner och tjänster. Du måste parsa värdet för för att få fullständig information `impactedServices` .

Det innehåll som är inuti är en Escaped [JSON](https://json.org/) -sträng som, vid avbrotts kontroll, innehåller ett annat JSON-objekt som kan analyseras regelbundet. Exempel:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

 blir:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Det här exemplet visar problem för:
- "Varningar & Mät värden" i östra Australien och Australien, sydöstra.
- "App Service" i Australien, sydöstra.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testa din webhook-integrering via en HTTP POST-begäran

Följ de här stegen:

1. Skapa den tjänst hälso nytto last som du vill skicka. Se ett exempel på tjänst hälsan webhook nytto Last [för Webhooks för Azure aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md).

1. Skapa en HTTP POST-begäran på följande sätt:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Du bör få svaret "2XX-lyckades".

1. Gå till [PagerDuty](https://www.pagerduty.com/) för att bekräfta att din integrering har kon figurer ATS korrekt.

## <a name="next-steps"></a>Nästa steg
- Granska [aktivitets logg aviseringens webhook-schema](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Läs mer om [meddelanden om tjänst hälsa](./service-notifications.md).
- Läs mer om [Åtgärds grupper](../azure-monitor/alerts/action-groups.md).
