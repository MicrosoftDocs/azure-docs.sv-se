---
title: Utveckla utan Azure IoT SDK | Microsoft Docs
description: Guide för utvecklare – information om och länkar till ämnen som du kan använda för att bygga enhets program och backend-appar utan att använda Azure IoT SDK.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461703"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Utveckla utan att använda Azure IoT Hub SDK

Det här avsnittet innehåller användbar information och länkar till utvecklare som vill utveckla enhets-eller backend-appar utan att använda Azure IoT-SDK: er.

Microsoft rekommenderar starkt att du använder en Azure IoT SDK. Azure IoT-enheten och tjänst-SDK: er publiceras på många populära plattformar. SDK: er tillhandahåller ett bekvämlighets lager som hanterar mycket av det underliggande kommunikations protokollets komplexitet, inklusive enhets anslutning och åter anslutning, och gör om en princip. SDK: er uppdateras regelbundet för att tillhandahålla de senaste funktionerna som visas av IoT Hub och säkerhets uppdateringar. Genom att använda SDK: er kan du minska utvecklings tiden och tiden för att få kod underhåll. Mer information om Azure IoT SDK: er finns i [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md): er. Mer information om fördelarna med att använda en Azure IoT SDK finns i fördelarna med att [använda Azure IoT-SDK: er och fall GRO par för att undvika om du inte](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) publicerar inlägget.

Även om IoT Hub stöder AMQP, AMQP via WebSockets, HTTPS, MQTT och MQTT via WebSockets för kommunikation med enheter, rekommenderar vi att du använder MQTT om enheten stöder det.

## <a name="development-prerequisites"></a>Utvecklings krav

Innan du börjar utveckla bör du ha en grundlig kunskap om IoT Hub och de funktioner som du vill att din enhet eller backend-app ska implementera. Här är en mycket förkortad lista med ämnen som du bör känna till:

* Se till att du förstår de slut punkter som exponeras av IoT Hub och de protokoll som stöds för varje slut punkt. Mer information finns i [IoT Hub slut punkter](iot-hub-devguide-endpoints.md).

* Om ett val av protokoll är involverat för enhets appar rekommenderar vi starkt att du använder MQTT. Innan du väljer ett protokoll bör du kontrol lera att du förstår de begränsningar som gäller för varje. Läs mer i [Välj ett kommunikations protokoll](iot-hub-devguide-protocols.md).

* Information om autentisering med IoT Hub finns i [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Hjälp om olika protokoll

För hjälp med följande protokoll utan Azure IoT SDK:

* Enhets-eller backend-appar på **AMQP** finns i [AMQP-support](iot-hub-amqp-support.md).

* Enhets program på **MQTT** finns i [MQTT-support](iot-hub-mqtt-support.md). De flesta av de här avsnitten behandlar användningen av MQTT-protokollet direkt. Den innehåller också information om hur du använder [IoT MQTT-exempel lagrings plats](https://github.com/Azure-Samples/IoTMQTTSample). Den här lagrings platsen innehåller C-exempel som använder Mosquitto-biblioteket för Sol förmörkelse för att skicka meddelanden till IoT Hub.

* Enhets-eller backend-appar på **https** finns i [Azure IoT Hub REST-API: er](/rest/api/iothub/). Tänk på det som beskrivs i [utvecklings krav](#development-prerequisites), att du inte kan använda X. 509-autentisering med certifikat utfärdare (ca) med https.

För enheter rekommenderar vi starkt att du använder MQTT om enheten stöder det.

## <a name="next-steps"></a>Nästa steg

* [MQTT-support](iot-hub-mqtt-support.md)