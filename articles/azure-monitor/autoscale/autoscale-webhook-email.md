---
title: Använd autoskalning för att skicka aviseringar via e-post och webhook
description: Lär dig hur du använder automatiska skalnings åtgärder för att anropa webb adresser eller skicka e-postaviseringar i Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100623789"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Använd åtgärder för autoskalning för att skicka aviseringar via e-post och webhook i Azure Monitor
Den här artikeln visar hur du konfigurerar utlösare så att du kan anropa vissa webb adresser eller skicka e-postmeddelanden baserat på automatiska skalnings åtgärder i Azure.  

## <a name="webhooks"></a>Webhooks
Med Webhooks kan du dirigera Azures aviserings meddelanden till andra system för efter bearbetning eller anpassade meddelanden. Till exempel vidarebefordra aviseringen till tjänster som kan hantera en inkommande webbegäran om att skicka SMS, logga buggar, meddela ett team med chatt-eller meddelande tjänster, osv. Webhook-URI: n måste vara en giltig HTTP-eller HTTPS-slutpunkt.

## <a name="email"></a>E-post
E-post kan skickas till alla giltiga e-postadresser. Administratörer och medadministratörer för den prenumeration där regeln körs kommer också att meddelas.

## <a name="cloud-services-and-app-services"></a>Cloud Services och App Services
Du kan välja mellan Azure Portal för Cloud Services och Server grupper (App Services).

* Välj **skala efter** mått.

![skala efter](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Skalningsuppsättningar för Virtual Machines
För nyare Virtual Machines som skapats med Resource Manager (skalnings uppsättningar för virtuella datorer) kan du konfigurera detta med hjälp av REST API, Resource Manager-mallar, PowerShell och CLI. Ett Portal gränssnitt är inte tillgängligt än.
När du använder REST API-eller Resource Manager-mallen inkluderar du meddelande elementet i din [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) med följande alternativ.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Fält | Erforderlig? | Beskrivning |
| --- | --- | --- |
| operation |ja |värdet måste vara "Scale" |
| sendToSubscriptionAdministrator |ja |värdet måste vara "sant" eller "falskt" |
| sendToSubscriptionCoAdministrators |ja |värdet måste vara "sant" eller "falskt" |
| customEmails |ja |värdet kan vara null [] eller sträng mat ris med e-post |
| Webhooks |ja |värdet kan vara null eller en giltig URI |
| serviceUri |ja |en giltig https-URI |
| properties |ja |värdet måste vara tomt {} eller innehålla nyckel/värde-par |

## <a name="authentication-in-webhooks"></a>Autentisering i Webhooks
Webhooken kan autentiseras med hjälp av tokenbaserad autentisering, där du sparar webhook-URI: n med ett token-ID som frågeparameter. Till exempel https: \/ /mysamplealert/webcallback? tokenID = sometokenid&someparameter = someValue

## <a name="autoscale-notification-webhook-payload-schema"></a>Autoskalning meddelande webhook nytto Last schema
När meddelandet för autoskalning skapas, ingår följande metadata i webhook-nytto lasten:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Fält | Erforderlig? | Beskrivning |
| --- | --- | --- |
| status |ja |Status som anger att en åtgärd för autoskalning har genererats |
| operation |ja |För en ökning av instanser blir det "skala ut" och en minskning av instanserna blir "skala in" |
| Edit |ja |Åtgärds kontext för autoskalning |
| timestamp |ja |Tidstämpel när åtgärden för autoskalning utlöstes |
| id |Ja |Resource Manager-ID för den automatiska skalnings inställningen |
| name |Ja |Namnet på den automatiska skalnings inställningen |
| information |Ja |Förklaring av åtgärden att AutoScale-tjänsten tog och ändringen i instans antalet |
| subscriptionId |Ja |Prenumerations-ID för mål resursen som skalas |
| resourceGroupName |Ja |Resurs grupp namn för den mål resurs som skalas |
| resourceName |Ja |Namnet på mål resursen som skalas |
| resourceType |Ja |De tre värdena som stöds: "Microsoft. classiccompute/domän namn/platser/roller" – moln tjänst roller, "Microsoft. Compute/virtualmachinescalesets"-Virtual Machine Scale Sets och "Microsoft. Web/Server grupper" – webbapp |
| resourceId |Ja |Resource Manager-ID för mål resursen som skalas |
| portalLink |Ja |Azure Portal länk till sidan Sammanfattning för mål resursen |
| oldCapacity |Ja |Det aktuella (gamla) instans antalet när autoskalning vidtog en skalnings åtgärd |
| newCapacity |Ja |Den nya instansen räknas som autoskalning som skalar resursen till |
| properties |Inga |Valfritt. Uppsättning <nyckel, värde> par (till exempel ord lista <sträng, sträng>). Egenskaps fältet är valfritt. I ett anpassat användar gränssnitt eller ett logiskt app-baserat arbets flöde kan du ange nycklar och värden som kan skickas med nytto lasten. Ett annat sätt att skicka anpassade egenskaper tillbaka till utgående webhook-anrop är att använda webhook-URI: n (som frågeparametrar) |
