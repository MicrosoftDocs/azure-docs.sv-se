---
title: Anropa en webhook med en klassisk mått avisering i Azure Monitor
description: Lär dig hur du omdirigerar Azure Metric-aviseringar till andra icke-Azure-system.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: alerts
ms.openlocfilehash: 93b8a9935e1a26df5aa76ca45c890036589a48ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624076"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Anropa en webhook med en klassisk mått avisering i Azure Monitor

> [!WARNING]
> Den här artikeln beskriver hur du använder äldre klassiska mått varningar. Azure Monitor har nu stöd för [nyare aviseringar i nästan real tid och nya aviserings upplevelser](../platform/alerts-overview.md). Klassiska aviseringar [dras tillbaka](../platform/monitoring-classic-retirement.md) för offentliga moln användare, men fortfarande i begränsad användning för resurser som ännu inte stöder de nya aviseringarna.
>

Du kan använda Webhooks för att dirigera ett meddelande om Azure-aviseringar till andra system för efter bearbetning eller anpassade åtgärder. Du kan använda en webhook för en avisering för att dirigera den till tjänster som skickar SMS-meddelanden, logga buggar för att meddela ett team via chatt-eller meddelande tjänster eller för olika andra åtgärder. 

Den här artikeln beskriver hur du ställer in en webhook i en Azure Metric-avisering. Det visar också vad nytto lasten för HTTP-inlägget till en webhook ser ut. Information om installationen och schemat för en Azure aktivitets logg avisering (varning om händelser) finns i [anropa en webhook på en Azure aktivitets logg avisering](../alerts/alerts-log-webhook.md).

Azure-aviseringar använder HTTP POST för att skicka aviserings innehållet i JSON-format till en webhook-URI som du anger när du skapar aviseringen. Schemat definieras längre fram i den här artikeln. URI: n måste vara en giltig HTTP-eller HTTPS-slutpunkt. Azure skickar en post per förfrågan när en avisering aktive ras.

## <a name="configure-webhooks-via-the-azure-portal"></a>Konfigurera Webhooks via Azure Portal
Om du vill lägga till eller uppdatera webhook-URI: n går du till **Skapa/uppdatera aviseringar** i [Azure Portal](https://portal.azure.com/).

![Fönstret Lägg till en varnings regel](./media/alerts-webhooks/Alertwebhook.png)

Du kan också konfigurera en avisering för att publicera till en webhook-URI genom att använda [Azure PowerShell cmdlets](../samples/powershell-samples.md#create-metric-alerts), ett [plattforms oberoende CLI](../samples/cli-samples.md#work-with-alerts)eller [Azure Monitor REST-API: er](/rest/api/monitor/alertrules).

## <a name="authenticate-the-webhook"></a>Autentisera webhooken
Webhooken kan autentiseras med hjälp av token-baserad auktorisering. Webhook-URI: n sparas med ett token-ID. Exempelvis: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Nytto Last schema
POST-åtgärden innehåller följande JSON-nyttolast och schema för alla Metric-baserade aviseringar:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Fält | Obligatorisk | Fast värde uppsättning | Kommentarer |
|:--- |:--- |:--- |:--- |
| status |Y |Aktive rad, löst |Status för aviseringen baserat på de villkor du anger. |
| Edit |Y | |Aviserings kontexten. |
| timestamp |Y | |Tiden då aviseringen utlöstes. |
| id |Y | |Varje varnings regel har ett unikt ID. |
| name |Y | |Aviseringens namn. |
| beskrivning |Y | |En beskrivning av aviseringen. |
| conditionType |Y |Mått, händelse |Två typer av aviseringar stöds: Metric och event. Mått varningar baseras på ett mått villkor. Händelse aviseringar baseras på en händelse i aktivitets loggen. Använd det här värdet för att kontrol lera om aviseringen baseras på ett mått eller en händelse. |
| moduletype |Y | |De speciella fälten som ska kontrol leras baserat på **conditionType** -värdet. |
| metricName |För mått varningar | |Namnet på måttet som definierar vad regeln övervakar. |
| metricUnit |För mått varningar |Byte, BytesPerSecond, antal, CountPerSecond, procent, sekunder |Enheten som tillåts i måttet. Se [tillåtna värden](/previous-versions/azure/reference/dn802430(v=azure.100)). |
| metricValue |För mått varningar | |Det faktiska värdet för det mått som orsakade aviseringen. |
| fastställd |För mått varningar | |Tröskelvärdet då aviseringen aktive ras. |
| windowSize |För mått varningar | |Den tids period som används för att övervaka aviserings aktiviteten baserat på tröskelvärdet. Värdet måste vara mellan 5 minuter och 1 dag. Värdet måste vara i formatet ISO 8601-varaktighet. |
| timeAggregation |För mått varningar |Genomsnitt, senaste, högsta, minimum, none, total |Hur data som samlas in ska kombineras över tid. Standardvärdet är Average. Se [tillåtna värden](/previous-versions/azure/reference/dn802410(v=azure.100)). |
| operator |För mått varningar | |Den operator som används för att jämföra aktuella mått data med angivet tröskelvärde. |
| subscriptionId |Y | |ID för Azure-prenumerationen. |
| resourceGroupName |Y | |Namnet på resurs gruppen för den berörda resursen. |
| resourceName |Y | |Resurs namnet för den berörda resursen. |
| resourceType |Y | |Resurs typen för den berörda resursen. |
| resourceId |Y | |Resurs-ID för den berörda resursen. |
| resourceRegion |Y | |Den berörda resursens region eller plats. |
| portalLink |Y | |En direkt länk till sammanfattnings sidan för Portal resursen. |
| properties |N |Valfritt |En uppsättning nyckel/värde-par som innehåller information om händelsen. Till exempel `Dictionary<String, String>`. Egenskaps fältet är valfritt. Användare kan ange nyckel/värde-par som kan skickas via nytto lasten i ett anpassat användar gränssnitt eller logiskt app-baserat arbets flöde. Ett annat sätt att skicka anpassade egenskaper tillbaka till webhooken är via webhook-URI: n (som frågeparametrar). |

> [!NOTE]
> Du kan bara ange fältet **Egenskaper** genom att använda [Azure Monitor REST API: er](/rest/api/monitor/alertrules).
>
>

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om Azure-aviseringar och Webhooks i videon [integrera Azure-aviseringar med PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Lär dig hur du [kör Azure Automation skript (Runbooks) i Azure-aviseringar](https://go.microsoft.com/fwlink/?LinkId=627081).
* Lär dig hur du [använder en Logic app för att skicka ett SMS-meddelande via Twilio från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Lär dig hur du [använder en Logic app för att skicka ett slack-meddelande från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Lär dig hur du [använder en Logic app för att skicka ett meddelande till en Azure-kö från en Azure-avisering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).
