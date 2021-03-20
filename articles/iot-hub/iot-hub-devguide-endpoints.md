---
title: Förstå Azure IoT Hub-slutpunkter | Microsoft Docs
description: Guide för utvecklare – referens information om IoT Hub enhets-och slut punkter som riktas mot slutanvändare.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: a58e141c6232db08b125b265e3d4ad74c784ba24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152190"
---
# <a name="reference---iot-hub-endpoints"></a>Referens – IoT Hub slut punkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub namn

Du kan hitta värd namnet för IoT-hubben som är värd för dina slut punkter i portalen på din Hubbs  **översikts** sida. DNS-namnet för en IoT-hubb ser som standard ut så här: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub slut punkter

Azure IoT Hub är en tjänst för flera innehavare som visar sina funktioner för olika aktörer. Följande diagram visar de olika slut punkter som IoT Hub visar.

![IoT Hub-slutpunkter](./media/iot-hub-devguide-endpoints/endpoints.png)

I följande lista beskrivs slut punkterna:

* **Resurs leverantör**. IoT Hub resurs leverantören exponerar ett [Azure Resource Manager](../azure-resource-manager/management/overview.md) -gränssnitt. Det här gränssnittet gör det möjligt för Azure-prenumerations ägare att skapa och ta bort IoT-hubbar och uppdatera IoT Hub-egenskaper. IoT Hub egenskaper styr [säkerhets principer på hubbnivå](iot-hub-devguide-security.md#access-control-and-permissions), i stället för åtkomst kontroll på enhets nivå, och funktionella alternativ för meddelande hantering från moln till enhet och enhet till moln. Med IoT Hub Resource Provider kan du också [Exportera enhets identiteter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Hantering av enhets identitet**. Varje IoT-hubb exponerar en uppsättning HTTPS REST-slutpunkter för att hantera enhets identiteter (skapa, Hämta, uppdatera och ta bort). [Enhets identiteter](iot-hub-devguide-identity-registry.md) används för enhetsautentisering och åtkomst kontroll.

* **Enhetens dubbla hantering**. Varje IoT-hubb exponerar en uppsättning service Facing HTTPS REST-slutpunkt för att fråga och uppdatera [enhetens dubbla](iot-hub-devguide-device-twins.md) (uppdatera Taggar och egenskaper). 

* **Jobb hantering**. Varje IoT-hubb exponerar en uppsättning service Facing HTTPS REST-slutpunkt för att fråga och hantera [jobb](iot-hub-devguide-jobs.md).

* **Enhets slut punkter**. För varje enhet i identitets registret visar IoT Hub en uppsättning slut punkter. Förutom vad som anges visas dessa slut punkter med [MQTT v 3.1.1](https://mqtt.org/)-, https 1,1-och [AMQP 1,0](https://www.amqp.org/) -protokoll. AMQP och MQTT är också tillgängliga via [WebSockets](https://tools.ietf.org/html/rfc6455) på port 443.

  * *Skicka meddelanden från enheten till molnet*. En enhet använder slut punkten för att [skicka meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md).

  * *Ta emot meddelanden från molnet till enheten*. En enhet använder slut punkten för att ta emot riktade [meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md).

  * *Initiera fil överföringar*. En enhet använder den här slut punkten för att ta emot en Azure Storage SAS-URI från IoT Hub för att [Ladda upp en fil](iot-hub-devguide-file-upload.md).

  * *Hämta och uppdatera enhetens dubbla egenskaper*. En enhet använder den här slut punkten för att få åtkomst till [enhetens dubbla](iot-hub-devguide-device-twins.md)egenskaper. HTTPS stöds inte.

  * *Ta emot begär Anden om direkta metoder*. En enhet använder slut punkten för att lyssna efter [direkta metod](iot-hub-devguide-direct-methods.md)begär Anden. HTTPS stöds inte.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Tjänst slut punkter**. Varje IoT-hubb exponerar en uppsättning slut punkter för lösningens Server del för att kommunicera med dina enheter. Med ett undantag exponeras dessa slut punkter endast med hjälp av protokollen [AMQP](https://www.amqp.org/) och AMQP över WebSockets. Anrops slut punkten för direkt metoden exponeras via HTTPS-protokollet.
  
  * *Ta emot meddelanden från enheten till molnet*. Den här slut punkten är kompatibel med [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). En backend-tjänst kan använda den för att läsa [meddelanden från enheten till molnet](iot-hub-devguide-messages-d2c.md) som skickas av dina enheter. Du kan skapa anpassade slut punkter i IoT Hub förutom den här inbyggda slut punkten.
  
  * *Skicka meddelanden från moln till enhet och få leverans bekräftelser*. Med de här slut punkterna kan Server delen av lösningen skicka Reliable [-meddelanden från molnet till enheten](iot-hub-devguide-messages-c2d.md)och ta emot motsvarande leverans-eller förfallo bekräftelser.
  
  * *Ta emot fil meddelanden*. Med den här meddelande slut punkten kan du få meddelanden om när enheterna har laddat upp en fil. 
  
  * *Direkt metod anrop*. Den här slut punkten gör det möjligt för en server dels tjänst att anropa en [direkt metod](iot-hub-devguide-direct-methods.md) på en enhet.
  
  * *Ta emot övervaknings händelser för åtgärder*. Med den här slut punkten kan du ta emot övervaknings händelser för operationer om din IoT Hub har kon figurer ATS för att generera dem. Mer information finns i [IoT Hub Operations Monitoring](iot-hub-operations-monitoring.md).

Artikeln om [Azure IoT-SDK](iot-hub-devguide-sdks.md) : er beskriver de olika sätten att komma åt dessa slut punkter.

Alla IoT Hub-slutpunkter använder [TLS](https://tools.ietf.org/html/rfc5246) -protokollet och ingen slut punkt exponeras aldrig på okrypterade/oskyddade kanaler.

## <a name="custom-endpoints"></a>Anpassade slut punkter

Du kan länka befintliga Azure-tjänster i din prenumeration till din IoT Hub för att agera som slut punkter för meddelanderoutning. Dessa slut punkter fungerar som tjänst slut punkter och används som mottagare för meddelande vägar. Enheter kan inte skriva direkt till de ytterligare slut punkterna. Lär dig mer [om meddelanderoutning](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slut punkter:

* Azure Storage behållare
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

Begränsningarna för antalet slut punkter som du kan lägga till finns i [kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

## <a name="endpoint-health"></a>Slut punkts hälsa

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Fält-gatewayer

I en IoT-lösning är en *fält-Gateway* placerad mellan dina enheter och dina IoT Hub slut punkter. Den finns vanligt vis nära dina enheter. Enheterna kommunicerar direkt med fält-gatewayen med hjälp av ett protokoll som stöds av enheterna. Fält-gatewayen ansluter till en IoT Hub-slutpunkt med ett protokoll som stöds av IoT Hub. En fält-Gateway kan vara en dedikerad maskin varu enhet eller en dator med låg strömförbrukning som kör anpassad Gateway-programvara.

Du kan använda [Azure IoT Edge](../iot-edge/index.yml) för att implementera en fält-Gateway. IoT Edge erbjuder funktioner som multiplexering av kommunikation från flera enheter till samma IoT Hub anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referens ämnen i den här IoT Hub Developer Guide är:

* [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md)
* [Förstå IP-adressen för IoT Hub](iot-hub-understand-ip-address.md)