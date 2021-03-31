---
title: Azure IoT Protocol-Gateway | Microsoft Docs
description: Använda en Azure IoT-protokollversion för att utöka IoT Hub funktioner och protokoll stöd för att aktivera enheter för att ansluta till hubben med protokoll som inte stöds av IoT Hub internt.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325040"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Stöd för ytterligare protokoll för IoT Hub

Azure IoT Hub har inbyggt stöd för kommunikation över MQTT-, AMQP-och HTTPS-protokollen. I vissa fall kanske enheter eller fält-gatewayer inte kan använda något av dessa standard protokoll och kräver protokoll anpassning. I sådana fall kan du använda en anpassad Gateway. En anpassad Gateway möjliggör protokoll anpassning för IoT Hub slut punkter genom att överbrygga trafiken till och från IoT Hub. Du kan använda [Azure IoT Protocol-gatewayen](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) som en anpassad Gateway för att aktivera protokoll anpassning för IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT Protocol-Gateway

Azure IoT Protocol-gatewayen är ett ramverk för protokoll anpassning som är utformad för storskalig kommunikation med dubbelriktade enheter med IoT Hub. Protokoll-gatewayen är en direkt komponent som godkänner enhets anslutningar över ett speciellt protokoll. Den förenar trafiken till IoT Hub över AMQP 1,0.

Du kan distribuera protokoll-gatewayen i Azure på ett mycket skalbart sätt med hjälp av Azure Service Fabric, Azure Cloud Services Worker-roller eller Virtuella Windows-datorer. Dessutom kan protokoll-gatewayen distribueras i lokala miljöer, till exempel fält-gatewayer.

Azure IoT-protokollets Gateway innehåller ett MQTT-nätverkskort som gör att du kan anpassa MQTT-protokollets beteende om det behövs. Eftersom IoT Hub ger inbyggt stöd för MQTT v 3.1.1-protokollet bör du bara överväga att använda MQTT-protokollstacken om det krävs protokoll anpassning eller särskilda krav för ytterligare funktioner.

MQTT-adaptern visar även programmerings modellen för att skapa protokoll kort för andra protokoll. Dessutom kan du med programmerings modellen för Azure IoT Protocol Gateway koppla in anpassade komponenter för specialiserad bearbetning, till exempel anpassad autentisering, meddelande omvandlingar, komprimering/expandering eller kryptering/dekryptering av trafik mellan enheterna och IoT Hub.

Azure IoT Protocol-gatewayen och MQTT-implementeringen tillhandahålls i ett projekt med öppen källkod för flexibilitet. Du kan använda projektet med öppen källkod för att lägga till stöd för olika protokoll och protokoll versioner, eller anpassa implementeringen för ditt scenario. 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure IoT Protocol-gatewayen och hur du använder och distribuerar det som en del av din IoT-lösning finns i:

* [Azure IoT Protocol Gateway-lagringsplats på GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Guide för Azure IoT Protocol Gateway-utvecklare](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Mer information om hur du planerar din IoT Hub-distribution finns i:

* [Jämför med Event Hubs](iot-hub-compare-event-hubs.md)

* [Skalning, hög tillgänglighet och haveri beredskap](iot-hub-scaling.md)

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)