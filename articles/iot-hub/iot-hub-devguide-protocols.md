---
title: Azure IoT Hub kommunikations protokoll och portar | Microsoft Docs
description: Guide för utvecklare – beskriver de kommunikations protokoll som stöds för kommunikation mellan enhet och moln och kommunikation mellan enheter och port nummer som måste vara öppna.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: e2578b47d27ef062d83ba8621a49e9a8f439897c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919033"
---
# <a name="reference---choose-a-communication-protocol"></a>Referens – Välj ett kommunikations protokoll

Med IoT Hub kan enheter använda följande protokoll för kommunikation på enhetssidan:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT över WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP över WebSockets
* HTTPS

Information om hur dessa protokoll stöder vissa IoT Hub funktioner finns i avsnittet [om kommunikations vägledning för enhet till moln](iot-hub-devguide-d2c-guidance.md) och [kommunikation från moln till enhet](iot-hub-devguide-c2d-guidance.md).

I följande tabell finns rekommendationer på hög nivå för ditt val av protokoll:

| Protokoll | När du bör välja det här protokollet |
| --- | --- |
| MQTT <br> MQTT över WebSocket |Använd på alla enheter som inte behöver ansluta flera enheter (var och en med sina egna autentiseringsuppgifter per enhet) över samma TLS-anslutning. |
| AMQP <br> AMQP över WebSocket |Använd på fält-och moln-gatewayer för att dra nytta av kopplings-multiplexering mellan enheter. |
| HTTPS |Används för enheter som inte har stöd för andra protokoll. |

Tänk på följande när du väljer protokoll för kommunikation på enhets sidan:

* **Mönster för moln till enhet**. HTTPS har inget effektivt sätt att implementera Server-push. När du använder HTTPS avsöker enhets IoT Hub efter meddelanden från molnet till enheten. Den här metoden är ineffektiv för både enhets-och IoT Hub. Under aktuella HTTPS-rikt linjer ska varje enhet söka efter meddelanden var 25: e minut eller mer. Om du skickar mer HTTPS får du resultat i IoT Hub begränsning av begär Anden. MQTT och AMQP har stöd för Server-push när du tar emot meddelanden från molnet till enheten. De aktiverar direkt push-meddelanden från IoT Hub till enheten. Om leverans fördröjning är ett problem är MQTT eller AMQP de bästa protokollen som ska användas. För sällan anslutna enheter fungerar HTTPS också.

* **Fält-gatewayer**. MQTT och HTTPS stöder endast en enhets identitet (enhets-ID plus autentiseringsuppgifter) per TLS-anslutning. Därför stöds inte dessa protokoll för fält-Gateway- [scenarier](iot-hub-devguide-endpoints.md#field-gateways) som kräver multiplexering av meddelanden med hjälp av flera enhets identiteter i en enda eller pool av överordnade anslutningar till IoT Hub. Sådana gatewayer kan använda ett protokoll som stöder flera enhets identiteter per anslutning, till exempel AMQP, för den överordnade trafiken.

* **Låg resurs enheter**. MQTT-och HTTPS-biblioteken har en mindre storlek än AMQP-biblioteken. Om enheten till exempel har begränsade resurser (till exempel mindre än 1 MB RAM-minne) kan dessa protokoll vara den enda protokoll implementering som är tillgänglig.

* **Nätverks Traversal**. Standard-AMQP-protokollet använder port 5671 och MQTT lyssnar på port 8883. Användningen av dessa portar kan orsaka problem i nätverk som är stängda till icke-HTTPS-protokoll. Använd MQTT över WebSockets, AMQP över WebSockets eller HTTPS i det här scenariot.

* **Nytto Last storlek**. MQTT och AMQP är binära protokoll, vilket resulterar i mer kompakta nytto laster än HTTPS.

> [!WARNING]
> När du använder HTTPS ska varje enhet söka efter meddelanden från molnet till enheten högst en gång var 25: e minut. Varje enhet kan under utveckling söka oftare, om så önskas.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Port nummer

Enheter kan kommunicera med IoT Hub i Azure med olika protokoll. Valet av protokoll styrs vanligt vis av de särskilda kraven för lösningen. I följande tabell visas de utgående portar som måste vara öppna för att en enhet ska kunna använda ett speciellt protokoll:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT över WebSockets |443 |
| AMQP |5671 |
| AMQP över WebSockets |443 |
| HTTPS |443 |

När du har skapat en IoT-hubb i en Azure-region behåller IoT Hub samma IP-adress som IoT-hubbens livs längd. Men om Microsoft flyttar IoT-hubben till en annan skalnings enhet för att underhålla tjänst kvalitet, tilldelas den en ny IP-adress.

## <a name="next-steps"></a>Nästa steg

Mer information om hur IoT Hub implementerar MQTT-protokollet finns i [kommunicera med IoT Hub med hjälp av MQTT-protokollet](iot-hub-mqtt-support.md).
