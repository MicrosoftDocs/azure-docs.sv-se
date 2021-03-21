---
title: Förstå MQTT-stöd för Azure IoT Device Provisioning-tjänsten | Microsoft Docs
description: Guide för utvecklare – stöd för enheter som ansluter till enhets slut punkten för Azure IoT Device Provisioning (DPS) med MQTT-protokollet.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967133"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunicera med din DPS med MQTT-protokollet

DPS gör det möjligt för enheter att kommunicera med DPS-enhetens slut punkt med:

* [MQTT v 3.1.1](https://mqtt.org/) på port 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) över WebSocket på port 443.

DPS är inte en komplett MQTT-Broker och har inte stöd för alla beteenden som anges i MQTT v 3.1.1-standarden. I den här artikeln beskrivs hur enheter kan använda MQTT-beteenden som stöds för att kommunicera med DPS.

All enhets kommunikation med DPS måste skyddas med TLS/SSL. DPS stöder därför inte anslutningar som inte är säkra via port 1883.

 > [!NOTE] 
 > DPS stöder för närvarande inte enheter som använder [mekanismen](./concepts-service.md#attestation-mechanism) för TPM-attestering över MQTT-protokollet.

## <a name="connecting-to-dps"></a>Ansluta till DPS

En enhet kan använda MQTT-protokollet för att ansluta till en DPS med något av följande alternativ.

* Bibliotek i SDK: er för [Azure IoT-etablering](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks).
* MQTT-protokollet direkt.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Använda MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda enhets-SDK: erna kan den fortfarande ansluta till den offentliga enhetens slut punkter med MQTT-protokollet på port 8883. I **Connect** -paketet ska enheten använda följande värden:

* Använd **registrationId** för fältet **ClientId** .

* För fältet **username** använder du `{idScope}/registrations/{registration_id}/api-version=2019-03-31` , där `{idScope}` är [idScope](./concepts-service.md#id-scope) för DPS.

* I fältet **lösen ord** använder du en SAS-token. Formatet på SAS-token är detsamma som för både HTTPS-och AMQP-protokollen:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` ResourceURI bör ha formatet `{idScope}/registrations/{registration_id}` . Princip namnet måste vara `registration` .

  > [!NOTE]
  > Om du använder autentisering med X. 509-certifikat krävs inte lösen ord för SAS-token.

  Mer information om hur du genererar SAS-token finns i avsnittet säkerhetstoken i [kontrol lera åtkomst till DPS](how-to-control-access.md#security-tokens).

Följande är en lista över de funktioner som används för DPS-implementering:

 * DPS stöder inte funktionerna i **CleanSession** -flaggan som anges till **0**.

 * När en enhets app prenumererar på ett ämne med **QoS 2**, tilldelar DPS högsta QoS-nivå 1 i **SUBACK** -paketet. Därefter levererar DPS meddelanden till enheten med QoS 1.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Om du vill använda MQTT-protokollet direkt *måste* klienten ansluta via TLS 1,2. Försök att hoppa över det här steget Miss lyckas med anslutnings fel.


## <a name="registering-a-device"></a>Registrera en enhet

Om du vill registrera en enhet via DPS bör en enhet prenumerera med ett `$dps/registrations/res/#` **ämnes filter**. Jokertecken `#` i ämnes filtret används bara för att tillåta att enheten tar emot ytterligare egenskaper i ämnes namnet. DPS tillåter inte användning av `#` eller `?` jokertecken för filtrering av underavsnitt. Eftersom DPS inte är en allmän pub-underordnad meddelande tjänst, stöder den bara de dokumenterade ämnes namn och ämnes filter.

Enheten bör publicera ett registrerings meddelande till DPS med `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` ett **ämnes namn**. Nytto lasten ska innehålla [enhetens registrerings](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) objekt i JSON-format.
I ett lyckat scenario får enheten ett svar på `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` ämnes namnet där x är ett nytt försök efter-värdet i sekunder. Nytto lasten för svaret kommer att innehålla [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) -objektet i JSON-format.

## <a name="polling-for-registration-operation-status"></a>Avsökning för registrerings åtgärds status

Enheten måste polla tjänsten regelbundet för att få resultatet av enhets registrerings åtgärden. Förutsatt att enheten redan har prenumererat på `$dps/registrations/res/#` avsnittet som anges ovan, kan den publicera ett Get operationstatus-meddelande till `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` ämnes namnet. Åtgärds-ID: t i det här meddelandet ska vara det värde som tas emot i RegistrationOperationStatus-svarsmeddelandet i föregående steg. I så fall kommer tjänsten att svara på `$dps/registrations/res/200/?$rid={request_id}` avsnittet. Nytto lasten för svaret kommer att innehålla RegistrationOperationStatus-objektet. Enheten bör fortsätta att söka efter tjänsten om svars koden är 202 efter en fördröjning som motsvarar återförsöket-efter-period. Enhets registrerings åtgärden har slutförts om tjänsten returnerar status koden 200.

## <a name="connecting-over-websocket"></a>Ansluta via WebSocket
När du ansluter via WebSocket anger du under protokollet som `mqtt` . Följ [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i MQTT- [dokumentationen](https://mqtt.org/).

För att ytterligare utforska funktionerna i DPS, se:

* [Om IoT DPS](about-iot-dps.md)