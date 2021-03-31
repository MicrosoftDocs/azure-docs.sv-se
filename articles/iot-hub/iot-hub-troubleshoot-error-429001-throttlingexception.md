---
title: Fel sökning av Azure IoT Hub-fel 429001 ThrottlingException
description: Förstå hur du åtgärdar fel 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545369"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

I den här artikeln beskrivs orsaker och lösningar för **429001 ThrottlingException** -fel.

## <a name="symptoms"></a>Symtom

Dina begär anden till IoT Hub fungerar inte med fel **429001-ThrottlingException**.

## <a name="cause"></a>Orsak

IoT Hub [begränsnings gränser](./iot-hub-devguide-quotas-throttling.md) har överskridits för den begärda åtgärden.

## <a name="solution"></a>Lösning

Kontrol lera att du når begränsnings gränsen genom att jämföra dina *telemetri-meddelanden* med de gränser som anges ovan. Du kan också kontrol lera *antalet begränsnings fel* . Information om dessa mått finns i mått för [enhets telemetri](monitor-iot-hub-reference.md#device-telemetry-metrics). Information om hur du använder mått för att övervaka din IoT Hub finns i [övervaka IoT Hub](monitor-iot-hub.md).

IoT Hub returnerar 429 ThrottlingException endast efter att gränsen har överskridits för för lång tid. Detta görs så att dina meddelanden inte försvinner om din IoT Hub får burst-trafik. Under tiden bearbetar IoT Hub meddelandena med begränsad hastighet, vilket kan gå långsamt om det finns för mycket kvarvarande trafik att hantera. Läs mer i [Trafikkvotering](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Nästa steg

Överväg att [skala upp IoT Hub](./iot-hub-scaling.md) om du kör i kvot-eller begränsnings gränser.