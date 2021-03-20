---
title: Azure IoT Hub och Event Grid | Microsoft Docs
description: Använd Azure Event Grid för att utlösa processer baserat på åtgärder som inträffar i IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152138"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagera på IoT Hub händelser genom att använda Event Grid för att utlösa åtgärder

Du kan integrera Azure IoT Hub med Azure Event Grid så att du kan skicka händelseaviseringar till andra tjänster och aktivera underordnade processer. Konfigurera dina företagsprogram så att de lyssnar efter IoT Hub-händelser. Då kan du reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt. Du kan till exempel skapa ett program som uppdaterar en databas, skapar ett arbetsärende och levererar en e-postavisering varje gång en ny IoT-enhet registreras i din IoT-hubb.

[Azure Event Grid](../event-grid/overview.md) är en helt hanterad tjänst för händelse dirigering som använder en publicerings prenumerations modell. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-overview.md)och kan leverera händelse aviseringar till icke-Azure-tjänster med hjälp av Webhooks. En fullständig lista över de händelse hanterare som Event Grid stöder finns i [en introduktion till Azure Event Grid](../event-grid/overview.md).

![Azure Event Grid arkitektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Event Grid-integrering är tillgänglig för IoT-hubbar som finns i regionerna där Event Grid stöds. Den senaste listan över regioner finns i [en introduktion till Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Händelsetyper

IoT Hub publicerar följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerad när en enhet registreras i en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerad när en enhet tas bort från en IoT-hubb. |
| Microsoft.Devices.DeviceConnected | Publicerad när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected | Publicerad när en enhet kopplas från en IoT-hubb. |
| Microsoft.Devices.DeviceTelemetry | Publiceras när ett meddelande med enhetstelemetri skickas till en IoT-hubb |

Använd antingen Azure Portal eller Azure CLI för att konfigurera vilka händelser som ska publiceras från varje IoT-hubb. För ett exempel kan du prova självstudien [skicka e-postaviseringar om Azure IoT Hub händelser med Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Händelseschema

IoT Hub händelser innehåller all information som du behöver för att svara på ändringar i enhetens livs cykel. Du kan identifiera en IoT Hub-händelse genom att kontrollera att egenskapen eventType börjar med **Microsoft.Devices**. Mer information om hur du använder Event Grid händelse egenskaper finns i [Event Grid händelse schema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Enhetens anslutna schema

I följande exempel visas schemat för en enhets ansluten händelse:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```



### <a name="device-telemetry-schema"></a>Schema för telemetri av enhet

Telemetriprocessor för enheten måste vara i ett giltigt JSON-format med contentType inställd på **Application/JSON** och contentEncoding inställd på **UTF-8** i meddelande [systemets egenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Båda dessa egenskaper är Skift läges känsliga. Om innehålls kodningen inte har angetts skrivs meddelandena i bas 64-kodat format IoT Hub.

Du kan utöka händelser för telemetri innan de publiceras till Event Grid genom att välja slut punkten som Event Grid. Mer information finns i [Översikt över meddelande översikt](iot-hub-message-enrichments-overview.md).

I följande exempel visas schemat för en händelse för telemetri av enheter:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Enhet skapad schema

I följande exempel visas schemat för en enhet som skapar en händelse:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```


> [!WARNING]
> *Dubbla data* som är associerade med en enhets skapande händelse är en standard konfiguration och *bör* inte förlitas på för faktiska `authenticationType` och andra enhets egenskaper på en nyligen skapad enhet. För `authenticationType` och andra enhets egenskaper i en nyligen skapad enhet använder du API: t för register hanteraren som finns i Azure IoT SDK: er.

En detaljerad beskrivning av varje egenskap finns i [Azure Event Grid händelse schema för IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrera händelser

IoT Hub händelse prenumerationer kan filtrera händelser baserat på händelse typ, data innehåll och ämne, vilket är enhetens namn.

Event Grid aktiverar [filtrering](../event-grid/event-filtering.md) av händelse typer, ämnen och data innehåll. När du skapar Event Grid prenumerationen kan du välja att prenumerera på valda IoT-händelser. Ämnes filter i Event Grid arbete baserat på **börjar med** (prefix) och **slutar med** (suffix) matchningar. Filtret använder en `AND` operator, så händelser med ett ämne som matchar både prefixet och suffixet levereras till prenumeranten.

Syftet med IoT-händelser använder formatet:

```json
devices/{deviceId}
```

Event Grid kan också filtrera efter attribut för varje händelse, inklusive data innehållet. På så sätt kan du välja vilka händelser som levereras baserat innehåll i telemetri-meddelandet. Se [avancerad filtrering](../event-grid/event-filtering.md#advanced-filtering) för att visa exempel. För filtrering i meddelande texten för telemetri måste du ange contentType till **Application/JSON** och ContentEncoding till **UTF-8** i meddelande [systemets egenskaper](./iot-hub-devguide-routing-query-syntax.md#system-properties). Båda dessa egenskaper är Skift läges känsliga.

För icke-telemetri-händelser som DeviceConnected, DeviceDisconnected, DeviceCreated och DeviceDeleted, kan Event Grid filtrering användas när du skapar prenumerationen. För telemetri-händelser kan användare förutom filtreringen i Event Grid även filtrera på enhets-och meddelande egenskaper och brödtext via meddelande cirkulations frågan. 

När du prenumererar på telemetri-händelser via Event Grid skapar IoT Hub en standard meddelande väg för att skicka data käll typen enhets meddelanden till Event Grid. Mer information om meddelanderoutning finns i [IoT Hub](iot-hub-devguide-messages-d2c.md)meddelanderoutning. Den här vägen visas i portalen under IoT Hub > meddelanderoutning. Endast en väg till Event Grid skapas oavsett hur många tex-prenumerationer som skapats för telemetri-händelser. Så om du behöver flera prenumerationer med olika filter kan du använda operatorn OR i dessa frågor på samma väg. Skapandet och borttagningen av vägen styrs genom prenumerationen av telemetri-händelser via Event Grid. Du kan inte skapa eller ta bort en väg för att Event Grid att använda IoT Hub meddelanderoutning.

Om du vill filtrera meddelanden innan telemetridata skickas kan du uppdatera din [cirkulations fråga](iot-hub-devguide-routing-query-syntax.md). Observera att cirkulations frågan bara kan användas i meddelande texten om texten är JSON. Du måste också ange contentType till **Application/JSON** och ContentEncoding till **UTF-8** i meddelande [systemets egenskaper](./iot-hub-devguide-routing-query-syntax.md#system-properties).

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Begränsningar för enhet ansluten/enhet frånkopplad-händelser

För att ta emot händelser för enhets anslutnings tillstånd måste en enhet göra antingen en "2C"-skicka telemetri "eller" C2D Receive Message "-åtgärd med IoT Hub. Observera dock att om en enhet använder AMQP-protokollet för att ansluta till IoT Hub, rekommenderar vi att de gör en "C2D Receive Message"-åtgärd i övrigt deras anslutnings tillstånds meddelanden kan fördröjas med några minuter. Om enheten använder MQTT-protokoll kommer IoT Hub att låta C2D-länken vara öppen. För AMQP kan du öppna länken C2D genom att anropa det asynkrona API: et för mottagning för IoT Hub C# SDK eller [enhets klienten för AMQP](iot-hub-amqp-support.md#device-client).

D2C-länken är öppen om du skickar telemetri. 

Om enhets anslutningen flimrar, vilket innebär att enheten ansluter och kopplar från ofta, kommer vi inte att skicka varje enskilt anslutnings tillstånd, men kommer att publicera det aktuella anslutnings läget på en periodisk ögonblicks bild, tills flimmen fortsätter. Om du tar emot antingen samma anslutnings tillstånds händelse med olika sekvensnummer eller olika anslutnings tillstånds händelser innebär det att du har ändrat enhetens anslutnings tillstånd.

## <a name="tips-for-consuming-events"></a>Tips om att använda händelser

Program som hanterar IoT Hub-händelser bör följa de här rekommendationerna:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, så anta inte att händelser kommer från en viss källa. Kontrollera alltid meddelandets ämne för att se till att det kommer från den IoT-hubb som du förväntar dig.

* Anta inte att alla händelser du tar emot är av de typer du förväntar dig. Kontrol lera alltid eventType innan du bearbetar meddelandet.

* Meddelanden kan tas emot i annan ordning eller med fördröjning. Använd etag-fältet för att ta reda på om din information om objekt är uppdaterad för enhet som skapats eller enhets borttagnings händelser.

## <a name="next-steps"></a>Nästa steg

* [Prova själv studie kursen om IoT Hub händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](iot-hub-how-to-order-connection-state-events.md)

* [Läs mer om Event Grid](../event-grid/overview.md)

* [Jämför skillnaderna mellan routning IoT Hub händelser och meddelanden](iot-hub-event-grid-routing-comparison.md)

* [Lär dig hur du använder IoT-telemetri för att implementera IoT spatial Analytics med hjälp av Azure Maps](../azure-maps/tutorial-iot-hub-maps.md)