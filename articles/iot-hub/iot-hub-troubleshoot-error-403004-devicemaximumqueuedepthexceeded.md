---
title: Fel sökning av Azure IoT Hub-fel 403004 DeviceMaximumQueueDepthExceeded
description: Förstå hur du åtgärdar fel 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3a8a63837617bc03ced475ae514f748e85d9b237
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061340"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

I den här artikeln beskrivs orsaker och lösningar för **403004 DeviceMaximumQueueDepthExceeded** -fel.

## <a name="symptoms"></a>Symtom

När du försöker skicka ett meddelande från molnet till enheten Miss lyckas begäran med fel **403004** eller **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Orsak

Den bakomliggande orsaken är att antalet meddelanden som är köade för enheten överskrider [gränsen för kön (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Det beror förmodligen på att du använder HTTPS för att ta emot meddelandet, vilket leder till kontinuerlig avsökning med `ReceiveAsync` , vilket resulterar i IoT Hub begränsning av begäran.

## <a name="solution"></a>Lösning

Det mönster som stöds för meddelanden från moln till enhet med HTTPS är tillfälligt anslutna enheter som söker efter meddelanden som inte förekommer ofta (mindre än var 25: e minut). Om du vill minska sannolikheten för att det ska gå att köra gränsen för kön växlar du till AMQP eller MQTT för meddelanden från moln till enhet.

Du kan också förbättra enhets sidans logik för att slutföra, avvisa eller överge köade meddelanden snabbt, förkorta tiden till Live eller överväga att skicka färre meddelanden. Se [C2D-meddelandets TTL-värde](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Slutligen kan du överväga att använda [API: et rensa kö](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) för att regelbundet rensa väntande meddelanden innan gränsen har uppnåtts.