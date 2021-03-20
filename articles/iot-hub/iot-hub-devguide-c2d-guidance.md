---
title: Alternativ för moln-till-enhet i Azure IoT Hub | Microsoft Docs
description: Guide för utvecklare – vägledning om när du ska använda direkta metoder, enhetens inbyggda egenskaper eller meddelanden från moln till enhet för kommunikation från moln till enhet.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093016"
---
# <a name="cloud-to-device-communications-guidance"></a>Vägledning för kommunikation mellan moln och enhet

IoT Hub innehåller tre alternativ för enhets appar för att exponera funktioner till en backend-app:

* [Direkta metoder](iot-hub-devguide-direct-methods.md) för kommunikation som kräver omedelbar bekräftelse av resultatet. Direkta metoder används ofta för interaktiv kontroll av enheter, t. ex. aktivering av en fläkt.

* [De två önskade egenskaperna](iot-hub-devguide-device-twins.md) för långvariga kommandon som är avsedda att försätta enheten i ett visst önskat tillstånd. Ange till exempel intervallet för att skicka telemetri till 30 minuter.

* [Meddelanden från moln till enhet](iot-hub-devguide-messages-c2d.md) för enkelriktade meddelanden till Device-appen.

Information om hur [Azure iot plug and Play](../iot-pnp/overview-iot-plug-and-play.md) använder de här alternativen för att kontrol lera IoT plug and Play-enheter finns i [IoT plug and Play service Developer Guide](../iot-pnp/concepts-developer-guide-service.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Här är en detaljerad jämförelse av de olika kommunikations alternativen från moln till enhet.

| Kategorier | Direkta metoder | Dubbla önskade egenskaper | Meddelanden från moln till enhet |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenario | Kommandon som kräver omedelbar bekräftelse, till exempel att aktivera en fläkt. | Tids krävande kommandon som är avsedda att försätta enheten i ett visst önskat tillstånd. Ange till exempel intervallet för att skicka telemetri till 30 minuter. | Enkelriktade meddelanden till Device-appen. |
| Dataflöde | Dubbelriktat. Device-appen kan svara på-metoden direkt. Lösningens Server del tar emot resultatet i sammanhang till begäran. | Enkelriktat. Enhetens app tar emot ett meddelande med egenskaps ändringen. | Enkelriktat. Enhets appen tar emot meddelandet
| Varaktighet | Frånkopplade enheter kontaktas inte. Server delen av lösningen meddelas om att enheten inte är ansluten. | Egenskaps värden bevaras i enheten med dubbla. Enheten kommer att läsa den vid nästa åter anslutning. Egenskaps värden kan hämtas med [IoT Hub frågespråk](iot-hub-devguide-query-language.md). | Meddelanden kan behållas genom IoT Hub i upp till 48 timmar. |
| Targets | En enhet som använder **deviceId** eller flera enheter som använder- [jobb](iot-hub-devguide-jobs.md). | En enhet som använder **deviceId** eller flera enheter som använder- [jobb](iot-hub-devguide-jobs.md). | En enhet per **deviceId**. |
| Storlek | Maximal nytto Last storlek för direkt metod är 128 KB. | Maximal önskad egenskaps storlek är 32 KB. | Upp till 64 KB-meddelanden. |
| Frekvens | Hög. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). | Medium. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). | Låg. Mer information finns i [IoT Hub gränser](iot-hub-devguide-quotas-throttling.md). |
| Protokoll | Tillgängligt med MQTT eller AMQP. | Tillgängligt med MQTT eller AMQP. | Tillgängligt på alla protokoll. Enheten måste avsöka när HTTPS används. |

Lär dig hur du använder direkta metoder, önskade egenskaper och meddelanden från molnet till enheten i följande Självstudier:

* [Använda direkta metoder](quickstart-control-device-node.md)
* [Använd önskade egenskaper för att konfigurera enheter](tutorial-device-twins.md) 
* [Skicka meddelanden från moln till enhet](iot-hub-node-node-c2d.md)
