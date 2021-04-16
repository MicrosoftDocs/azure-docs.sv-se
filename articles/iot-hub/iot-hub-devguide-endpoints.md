---
title: Förstå Azure IoT Hub slutpunkter | Microsoft Docs
description: Utvecklarguide – referensinformation IoT Hub för enhets- och tjänstriktade slutpunkter.
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
ms.openlocfilehash: d2b9ea2e075ddcf20860ccb9ab1f2eff654993ad
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499384"
---
# <a name="reference---iot-hub-endpoints"></a>Referens – IoT Hub slutpunkter

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub namn

Du hittar värdnamnet för den IoT-hubb som är värd för  dina slutpunkter i portalen på hubbens översiktssida. Som standard ser DNS-namnet för en IoT-hubb ut så här: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Lista över inbyggda IoT Hub slutpunkter

Azure IoT Hub är en tjänst för flera innehavare som exponerar dess funktioner för olika aktörer. Följande diagram visar de olika slutpunkter som IoT Hub exponerar.

![IoT Hub-slutpunkter](./media/iot-hub-devguide-endpoints/endpoints.png)

I följande lista beskrivs slutpunkterna:

* **Resursprovider**. Resursprovidern IoT Hub exponerar [](../azure-resource-manager/management/overview.md) ett Azure Resource Manager gränssnitt. Med det här gränssnittet kan Azure-prenumerationsägare skapa och ta bort IoT-hubbar och uppdatera IoT Hub-egenskaper. IoT Hub styr säkerhetsprinciper på hubbnivå [i](iot-hub-devguide-security.md#access-control-and-permissions)stället för åtkomstkontroll på enhetsnivå och funktionella alternativ för meddelanden från moln till enhet och från enhet till moln. Med IoT Hub-resursprovidern kan du [också exportera enhetsidentiteter.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Hantering av enhetsidentiteter.** Varje IoT-hubb exponerar en uppsättning HTTPS REST-slutpunkter för att hantera enhetsidentiteter (skapa, hämta, uppdatera och ta bort). [Enhetsidentiteter](iot-hub-devguide-identity-registry.md) används för enhetsautentisering och åtkomstkontroll.

* **Hantering av enhetstvilling.** Varje IoT-hubb exponerar en uppsättning tjänstriktade HTTPS REST-slutpunkter för att fråga och uppdatera enhetstvillingarna [(uppdateringstaggar](iot-hub-devguide-device-twins.md) och egenskaper). 

* **Jobbhantering**. Varje IoT-hubb exponerar en uppsättning tjänstriktade HTTPS REST-slutpunkter för att fråga efter och hantera [jobb.](iot-hub-devguide-jobs.md)

* **Enhetsslutpunkter**. För varje enhet i identitetsregistret IoT Hub en uppsättning slutpunkter. Om inget annat anges exponeras dessa slutpunkter med hjälp av [protokollen MQTT v3.1.1,](https://mqtt.org/)HTTPS 1.1 och [AMQP 1.0.](https://www.amqp.org/) AMQP och MQTT är också tillgängliga via [WebSockets](https://tools.ietf.org/html/rfc6455) på port 443.

  * *Skicka meddelanden från enhet till moln.* En enhet använder den här slutpunkten [för att skicka "enhet till molnet"-meddelanden.](iot-hub-devguide-messages-d2c.md)

  * *Ta emot meddelanden från moln till enhet.* En enhet använder den här slutpunkten för att ta emot riktade meddelanden [från moln till enhet.](iot-hub-devguide-messages-c2d.md)

  * *Initiera filuppladdningar*. En enhet använder den här slutpunkten för att ta Azure Storage SAS-URI från IoT Hub för [att ladda upp en fil](iot-hub-devguide-file-upload.md).

  * *Hämta och uppdatera enhetstvillingens egenskaper*. En enhet använder den här slutpunkten för att få åtkomst [till enhetstvillingens](iot-hub-devguide-device-twins.md)egenskaper. HTTPS stöds inte.

  * *Ta emot begäranden om direktmetod*. En enhet använder den här slutpunkten för att [lyssna efter begäranden för](iot-hub-devguide-direct-methods.md)direktmetod. HTTPS stöds inte.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Tjänstslutpunkter**. Varje IoT-hubb exponerar en uppsättning slutpunkter som backend-lösningen kan använda för att kommunicera med dina enheter. Med ett undantag exponeras dessa slutpunkter endast med hjälp av [AMQP och](https://www.amqp.org/) AMQP över WebSockets-protokoll. Slutpunkten för direktmetodanrop exponeras via HTTPS-protokollet.
  
  * *Ta emot meddelanden från enhet till moln.* Den här slutpunkten är kompatibel [med Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). En backend-tjänst kan använda den för att läsa ["enhet till molnet"-meddelanden som](iot-hub-devguide-messages-d2c.md) skickas av dina enheter. Du kan skapa anpassade slutpunkter på din IoT-hubb utöver den här inbyggda slutpunkten.
  
  * *Skicka meddelanden från moln till enhet och ta emot leveransbekräftelser.* Med dessa slutpunkter kan din lösnings backend [skicka](iot-hub-devguide-messages-c2d.md)tillförlitliga meddelanden från molnet till enheten och ta emot motsvarande leverans- eller förfallobekräftelser.
  
  * *Ta emot filmeddelanden*. Med den här slutpunkten för meddelanden kan du få meddelanden om när dina enheter har laddat upp en fil. 
  
  * *Anrop av direktmetod.* Med den här slutpunkten kan en backend-tjänst anropa [en direktmetod](iot-hub-devguide-direct-methods.md) på en enhet.
  
  * *Ta emot övervakningshändelser för åtgärder*. Med den här slutpunkten kan du ta emot övervakningshändelser om din IoT-hubb har konfigurerats att generera dem. Mer information finns i [IoT Hub övervakning av .](iot-hub-operations-monitoring.md)

Artikeln [om Azure IoT-SDK:er](iot-hub-devguide-sdks.md) beskriver de olika sätten att komma åt dessa slutpunkter.

Alla IoT Hub slutpunkter använder [TLS-protokollet](https://tools.ietf.org/html/rfc5246) och ingen slutpunkt exponeras någonsin på okrypterade/oskyddade kanaler.

## <a name="custom-endpoints"></a>Anpassade slutpunkter

Du kan länka befintliga Azure-tjänster i dina Azure-prenumerationer till din IoT-hubb för att fungera som slutpunkter för meddelanderoutning. Dessa slutpunkter fungerar som tjänstslutpunkter och används som mottagare för meddelandevägar. Enheter kan inte skriva direkt till de ytterligare slutpunkterna. Läs mer om [meddelanderoutning.](../iot-hub/iot-hub-devguide-messages-d2c.md)

IoT Hub stöder för närvarande följande Azure-tjänster som ytterligare slutpunkter:

* Azure Storage containrar
* Event Hubs
* Service Bus-köer
* Avsnitt om Service Bus

Information om gränserna för antalet slutpunkter som du kan lägga till finns [i Kvoter och begränsning.](iot-hub-devguide-quotas-throttling.md)

## <a name="endpoint-health"></a>Slutpunktshälsa

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Fältgatewayer

I en IoT-lösning finns *en fältgateway* mellan dina enheter och IoT Hub slutpunkter. Den finns vanligtvis nära dina enheter. Dina enheter kommunicerar direkt med fältgatewayen med hjälp av ett protokoll som stöds av enheterna. Fältgatewayen ansluter till IoT Hub slutpunkt med hjälp av ett protokoll som stöds av IoT Hub. En fältgateway kan vara en dedikerad maskinvaruenhet eller en dator med låg ström som kör anpassad gatewayprogramvara.

Du kan använda [Azure IoT Edge för](../iot-edge/index.yml) att implementera en fältgateway. IoT Edge funktioner som att multiplexera kommunikation från flera enheter till samma IoT Hub anslutning.

## <a name="next-steps"></a>Nästa steg

Andra referensämnen i den här IoT Hub utvecklarhandboken är:

* [IoT Hub för enhetstvillingarna, jobben och meddelanderoutning](iot-hub-devguide-query-language.md)
* [Kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md)
* [Förstå IP-adressen för din IoT Hub](iot-hub-understand-ip-address.md)
