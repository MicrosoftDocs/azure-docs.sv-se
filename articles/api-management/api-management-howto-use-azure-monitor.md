---
title: 'Självstudie – övervaka publicerade API: er i Azure API Management | Microsoft Docs'
description: 'Följ stegen i den här självstudien för att lära dig hur du använder mått, varningar, aktivitets loggar och resurs loggar för att övervaka dina API: er i Azure API Management.'
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 1cb902c4b59193c46dbeca47bb355f0695a0f2c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572802"
---
# <a name="tutorial-monitor-published-apis"></a>Självstudie: övervaka publicerade API: er

Med Azure Monitor kan du visualisera, fråga, cirkulera, arkivera och vidta åtgärder för mått eller loggar som kommer från Azure API Management-tjänsten.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa mått för din API 
> * Konfigurera en varnings regel 
> * Visa aktivitetsloggar
> * Aktivera och Visa resurs loggar

Du kan också använda API Management inbyggda [analyser](howto-use-analytics.md) för att övervaka användning och prestanda för dina API: er.

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visa mått för dina API:er

API Management utvärderar [måtten](../azure-monitor/essentials/data-platform-metrics.md) varje minut, vilket ger dig nära insyn i real tid i dina API: er tillstånd och hälsa. Följande är de två oftast använda måtten. En lista över alla tillgängliga mått finns i mått som [stöds](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice).

* **Kapacitet** – hjälper dig att fatta beslut om att uppgradera/NEDGRADERA dina APIM-tjänster. Måttet genereras per minut och återspeglar gatewaykapaciteten vid tidpunkten för rapporten. Måtten sträcker sig från 0 till 100 och beräknas utifrån gatewayens resurser som CPU och minnesanvändning.
* **Förfrågningar** – hjälper dig att analysera API-trafik som går via dina API Management-tjänster. Måttet genereras per minut och rapporterar antalet Gateway-begäranden med dimensioner, inklusive svars koder, plats, värdnamn och fel. 

> [!IMPORTANT]
> Följande mått är föråldrade från maj 2019 och kommer att dras tillbaka i augusti 2023: totalt antal Gateway-begäranden, lyckade Gateway-begäranden, obehöriga Gateway-begäranden, misslyckade Gateway-begäranden, andra gateway-begäranden. Migrera till begär ande måttet som ger motsvarande funktionalitet.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Skärm bild av mått i API Management översikt":::

Så här får du åtkomst till mått:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen. På sidan **Översikt** granskar du nyckel mått för dina API: er.
1. Om du vill undersöka mått i detalj väljer du **mått** från menyn längst ned på sidan.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Skärm bild av mått objekt i övervaknings menyn":::

1. Från listrutan väljer du mått som du är intresserad av. Till exempel **begär Anden**. 
1. Diagrammet visar det totala antalet API-anrop.
1. Diagrammet kan filtreras med dimensionerna för **begär** ande mått. Välj **till exempel Lägg till filter**, Välj **svars kod kategori för Server** del, ange 500 som värde. Nu visar diagrammet antalet begär Anden som misslyckats i API-backend-servern.   

## <a name="set-up-an-alert-rule"></a>Konfigurera en varnings regel 

Du kan få [aviseringar](../azure-monitor/alerts/alerts-metric-overview.md) baserat på mått och aktivitets loggar. Med Azure Monitor kan du [Konfigurera en avisering](../azure-monitor/alerts/alerts-metric.md) för att göra följande när den utlöser:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Så här konfigurerar du ett exempel på en varnings regel baserat på ett begär ande mått:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Välj **aviseringar** från meny raden nästan längst ned på sidan.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Skärm bild av alternativet aviseringar i menyn övervakning":::

1. Välj **+ Ny aviseringsregel**.
1. I fönstret **skapa aviserings regel** **väljer du villkor**.
1. I fönstret **Konfigurera signal logik** :
    1. I **signal typ** väljer du **mått**.
    1. I **signal namn** väljer du **begär Anden**.
    1. I **dela efter dimensioner**, i **Dimensions namn**, väljer du **kategori för gateway-svars kod**.
    1. I **Dimensions värden** väljer du **4xx** för klient fel som obehöriga eller ogiltiga begär Anden.
    1. I **aviserings logik** anger du ett tröskelvärde efter vilken aviseringen ska utlösas och väljer sedan **slutförd**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Skärm bild av konfigurera signal logik fönster":::

1. Välj en befintlig åtgärds grupp eller skapa en ny. I följande exempel skapas en ny åtgärds grupp. Ett e-postmeddelande skickas till admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Skärm bild av meddelanden för ny åtgärds grupp":::

1. Ange ett namn och en beskrivning av varnings regeln och välj allvarlighets grad. 
1. Välj **Skapa varningsregel**.
1. Testa nu varnings regeln genom att anropa konferens-API: et utan en API-nyckel. Exempel:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    En avisering aktive ras baserat på utvärderings perioden och e-postmeddelandet skickas till admin@contoso.com . 

    Aviseringar visas också på sidan **aviseringar** för API Management-instansen.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Skärm bild av aviseringar i portalen":::

## <a name="activity-logs"></a>Aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina API Management-tjänster. Med aktivitetsloggar kan du bestämma ”vad, vem och när” för skrivåtgärder (PUT, POST, DELETE) som ska vidtas för dina API Management-tjänster.

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET), åtgärder som utförs i Azure-portalen eller via ursprungliga hanterings-API:er.

Du kan få åtkomst till aktivitetsloggar i API Management-tjänsten eller få åtkomst till loggar för alla dina Azure-resurser i Azure Monitor. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Skärm bild av aktivitets loggen i portalen":::

Så här visar du aktivitets loggen:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.

1. Välj **aktivitets logg**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Skärm bild av aktivitets logg objekt i övervaknings menyn":::
1. Välj önskat filtrerings omfång och **Använd** sedan.

## <a name="resource-logs"></a>Resursloggar

Resurs loggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning samt fel söknings syfte. Resurs loggar skiljer sig från aktivitets loggar. Aktivitets loggen ger insikter om de åtgärder som utfördes på dina Azure-resurser. Resurs loggar ger insikt om åtgärder som din resurs utfört.

Så här konfigurerar du resurs loggar:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
2. Välj **diagnostikinställningar**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Skärm bild av objektet diagnostiska inställningar i övervaknings menyn":::

1. Välj **+ Lägg till diagnostisk inställning**.
1. Välj de loggar eller mått som du vill samla in.

   Du kan arkivera resurs loggar tillsammans med mått till ett lagrings konto, strömma dem till en Händelsehubben eller skicka dem till en Log Analytics-arbetsyta. 

Mer information finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Visa diagnostikdata i Azure Monitor

Om du aktiverar insamling av GatewayLogs eller mått i en Log Analytics arbets yta kan det ta några minuter innan data visas i Azure Monitor. Så här visar du data:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Välj **loggar** på menyn nästan längst ned på sidan.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Skärm bild av objektet loggar i menyn övervakning":::

Kör frågor för att visa data. Det finns flera [exempel frågor](../azure-monitor/logs/example-queries.md) eller så kör du egna. Följande fråga hämtar till exempel de senaste 24 timmarna av data från tabellen GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Mer information om hur du använder resurs loggar för API Management finns i:

* [Kom igång med Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)eller testa [Log Analytics demo miljön](https://portal.loganalytics.io/demo).

* [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

Följande JSON visar en exempel post i GatewayLogs för en lyckad API-begäran. Mer information finns i [schema referensen](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa mått för din API
> * Konfigurera en varnings regel 
> * Visa aktivitetsloggar
> * Aktivera och Visa resurs loggar


Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Spåra anrop](api-management-howto-api-inspector.md)