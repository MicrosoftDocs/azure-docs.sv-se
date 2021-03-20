---
title: Skapa webhookar för regler i Azure IoT Central | Microsoft Docs
description: Skapa Webhooks i Azure IoT Central för att automatiskt meddela andra program när regler utlöses.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87337236"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder för regler i Azure IoT Central

*Det här avsnittet gäller för byggare och administratörer.*

Med Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och-aviseringar. Webhooks meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i IoT Central-appen. Din IoT Central-App skickar en POST-begäran till det andra programmets HTTP-slutpunkt när en regel utlöses. Nytto lasten innehåller information om enhets information och regel utlösare.

## <a name="set-up-the-webhook"></a>Konfigurera webhooken

I det här exemplet ansluter du till RequestBin för att få meddelanden när regler som aktive ras med Webhooks.

1. Öppna [RequestBin](https://requestbin.net/).

1. Skapa en ny RequestBin och kopiera **bin-URL: en**.

1. Skapa en [telemetri-regel](tutorial-create-telemetry-rules.md). Spara regeln och Lägg till en ny åtgärd.

    ![Skärm bild för skapande av webhook](media/howto-create-webhooks/webhookcreate.png)

1. Välj åtgärden webhook och ange ett visnings namn och klistra in URL-adressen som återanrops-URL.

1. Spara regeln.

Nu när regeln utlöses visas en ny begäran i RequestBin.

## <a name="payload"></a>Innehållet

När en regel utlöses görs en HTTP POST-begäran till återanrops-URL: en med en JSON-nyttolast med telemetri-, enhets-, regel-och program information. Nytto lasten kan se ut så här:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Om regeln övervakar aggregerad telemetri under en viss tids period, kommer nytto lasten att innehålla en annan telemetri-sektion.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Ändrings meddelande för data format

Om du har en eller flera Webhooks skapade och sparade före **3 April 2020** måste du ta bort webhooken och skapa en ny webhook. Detta beror på att äldre Webhooks använder ett gammalt nytto Last format som kommer att bli inaktuellt i framtiden.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Webhook-nyttolast (format föråldras från och med 3 april 2020)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Kända begränsningar

För närvarande finns det inget programmerings sätt för att prenumerera/avslutning från dessa webhookar via ett API.

Om du har idéer om hur du kan förbättra den här funktionen kan du publicera dina förslag till vårt [användar röst forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar och använder Webhooks är det föreslagna nästa steg att utforska [konfigurationen av Azure Monitor åtgärds grupper](howto-use-action-groups.md).
