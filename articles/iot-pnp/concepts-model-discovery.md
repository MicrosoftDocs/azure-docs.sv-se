---
title: Använda IoT Plug and Play-modeller i en lösning | Microsoft Docs
description: Som Solution Builder kan du läsa mer om hur du kan använda IoT Plug and Play-modeller i IoT-lösningen.
author: arunmannengal
ms.author: arunmann
ms.date: 07/23/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7e0d93d3fd179d672b49967c575bbfb9c0fc5de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213699"
---
# <a name="use-iot-plug-and-play-models-in-an-iot-solution"></a>Använda IoT Plug and Play-modeller i en IoT-lösning

Den här artikeln beskriver hur du i en IoT-lösning kan identifiera modell-ID för en IoT Plug and Play-enhet och sedan hämta dess modell definition.

Det finns två breda kategorier av en IoT-lösning:

- En *syfte-konstruerad lösning* fungerar med en känd uppsättning modeller för IoT plug and Play enheter som ska ansluta till lösningen. Du använder dessa modeller när du utvecklar lösningen.

- En *modell driven* lösning kan fungera med modellen för alla IoT plug and Play-enheter. Att skapa en modell driven lösning är mer komplex, men fördelen är att din lösning fungerar med alla enheter som kan läggas till i framtiden. En modell driven IoT-lösning hämtar en modell och använder den för att fastställa telemetri, egenskaper och kommandon som enheten implementerar.

Om du vill använda en IoT Plug and Play-modell, en IoT-lösning:

1. Identifierar modell-ID för modellen som implementeras av IoT Plug and Play enhet, modul eller IoT Edge modul som är ansluten till lösningen.

1. Använder modell-ID: t för att hämta modell definitionen för den anslutna enheten från en modell databas eller ett anpassat arkiv.

## <a name="identify-model-id"></a>Identifiera modell-ID

När en IoT Plug and Play-enhet ansluter till IoT Hub registreras modell-ID: t för den modell som den implementerar med IoT Hub.

IoT Hub meddelar lösningen med enhets modell-ID som en del av enhets anslutnings flödet.

En lösning kan hämta modell-ID: t för IoT Plug and Play-enheten genom att använda någon av följande tre metoder:

### <a name="get-device-twin-api"></a>Hämta enhetens dubbla API

Lösningen kan använda [enhetens dubbla](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) API för att hämta modell-ID för IoT plug and Play-enheten.

> [!TIP]
> För moduler och IoT Edge moduler använder du [ModuleClient. getTwin](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient.gettwin).

I följande enhet med dubbla svars kodfragment `modelId` innehåller modell-ID: t för en IoT plug and Play-enhet:

```json
{
    "deviceId": "sample-device",
    "etag": "AAAAAAAAAAc=",
    "deviceEtag": "NTk0ODUyODgx",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "2020-07-17T06:12:26.8402249Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {...}
    }
}
```

### <a name="get-digital-twin-api"></a>Hämta digital, dubbla API

Lösningen kan använda det [digitala dubbla](/rest/api/iothub/service/digitaltwin/getdigitaltwin) API: t för att hämta modell-ID: t för modellen som implementeras av IoT plug and Play-enheten.

I följande digitala dubbla svar-kodfragment `$metadata.$model` innehåller modell-ID: t för en IoT plug and Play-enhet:

```json
{
    "$dtId": "sample-device",
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="digital-twin-change-event-notification"></a>Digital varning om att ändra händelse

En enhets anslutning resulterar i ett digitalt meddelande om [ändrings händelser](concepts-digital-twin.md#digital-twin-change-events) . En lösning måste prenumerera på den här händelse aviseringen. Information om hur du aktiverar routning för digitala dubbla händelser finns i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enhet till moln till olika slut punkter](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Lösningen kan använda den händelse som visas i följande kodfragment för att lära dig mer om IoT Plug and Play-enheten som ansluter och får sitt modell-ID:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/22/2020 8:02:27 PM
iothub-message-source:digitalTwinChangeEvents
correlation-id:100f322dc2c5
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:example:TemperatureController;1"
  }
]
```

## <a name="retrieve-a-model-definition"></a>Hämta en modell definition

En lösning använder modell-ID som identifierades ovan för att hämta motsvarande modell definition.

En lösning kan hämta modell definitionen genom att använda något av följande alternativ:

### <a name="model-repository"></a>Modelldatabas

Lösningar kan använda [modell databasen](concepts-model-repository.md) för att hämta modeller. Enhets byggarna eller lösnings byggarna måste ladda upp sina modeller till databasen i förväg så att lösningen kan hämta dem.

Följ dessa steg när du har identifierat modell-ID för en ny enhets anslutning:

1. Hämta modell definitionen med modell-ID från modell databasen. Mer information finns i [enhets modellens lagrings plats](concepts-model-repository.md).

1. Med hjälp av modell definitionen för den anslutna enheten kan du räkna upp enhetens funktioner.

1. Med hjälp av enhetens uppräknade funktioner kan du tillåta att användare [interagerar med enheten](quickstart-service.md).

### <a name="custom-store"></a>Anpassat Arkiv

Lösningar kan lagra dessa modell definitioner i ett lokalt fil system, i ett offentligt fil arkiv eller använda en anpassad implementering.

Följ dessa steg när du har identifierat modell-ID för en ny enhets anslutning:

1. Hämta modell definitionen med modell-ID från ditt anpassade arkiv.

1. Med hjälp av modell definitionen för den anslutna enheten kan du räkna upp enhetens funktioner. 

1. Med hjälp av enhetens uppräknade funktioner kan du tillåta att användare [interagerar med enheten](quickstart-service.md).  

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du integrerar IoT Plug and Play-modeller i en IoT-lösning är några förslag på nästa steg:

- [Interagera med en enhet från din lösning](quickstart-service.md)
- [IoT Digital, dubbla REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
