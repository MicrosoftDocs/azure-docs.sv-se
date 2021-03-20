---
title: Fel sökning av Azure IoT Hub-fel 403002 IoTHubQuotaExceeded
description: Förstå hur du åtgärdar fel 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76961119"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

I den här artikeln beskrivs orsaker och lösningar för **403002 IoTHubQuotaExceeded** -fel.

## <a name="symptoms"></a>Symtom

Alla begär anden till IoT Hub fungerar inte med felet  **403002 IoTHubQuotaExceeded**. I Azure Portal laddas inte enhets listan för IoT Hub.

## <a name="cause"></a>Orsak

Den dagliga meddelande kvoten för IoT Hub har överskridits. 

## <a name="solution"></a>Lösning

[Uppgradera eller öka antalet enheter på IoT-hubben](iot-hub-upgrade.md) eller vänta tills nästa UTC-dag för att uppdatera den dagliga kvoten.

## <a name="next-steps"></a>Nästa steg

* Information om hur åtgärder räknas mot kvoten, till exempel dubbla frågor och direkta metoder, finns i [förstå IoT Hub priser](iot-hub-devguide-pricing.md#charges-per-operation)
* Konfigurera övervakning för daglig kvot användning genom att ställa in en avisering med det *totala antalet meddelanden som används*. Stegvisa instruktioner finns i [Konfigurera mått och aviseringar med IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)