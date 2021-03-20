---
title: Skicka meddelanden till en MQTT-server med hjälp av klient biblioteket för Azure MQTT
description: Lär dig hur du använder klient biblioteket för MQTT för att skicka meddelanden till en MQTT-Broker. Lär dig också hur du konfigurerar din mXChip IoT-DevKit som en MQTT-klient.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: fb8bf593568825793a1a205a2955599b16fa78cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151760"
---
# <a name="send-messages-to-an-mqtt-server"></a>Skicka meddelanden till en MQTT-Server

Sakernas Internet-system (IoT) hanterar ofta återkommande, dålig kvalitet eller långsamma Internet anslutningar. MQTT är ett M2M-protokoll (Machine-to-Machine) som har utvecklats med dessa utmaningar i åtanke. 

Det MQTT-klient bibliotek som används här är en del av [PAHO](https://www.eclipse.org/paho/) -projektet, som innehåller API: er för att använda MQTT över flera transport medel.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet får du lära dig:
- Så här använder du klient biblioteket MQTT för att skicka meddelanden till en MQTT-Broker.
- Så här konfigurerar du din MXChip IoT-DevKit som en MQTT-klient.

## <a name="what-you-need"></a>Detta behöver du

Slutför [komma igång guiden](./iot-hub-arduino-iot-devkit-az3166-get-started.md) för att:

* Låt din DevKit vara ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

## <a name="open-the-project-folder"></a>Öppna projektmappen

1. Om DevKit redan är ansluten till datorn kopplar du från den.

2. Starta VS Code.

3. Anslut DevKit till din dator.

## <a name="open-the-mqttclient-sample"></a>Öppna exemplet MQTTClient

Expandera exempel avsnittet för vänster **ARDUINO** , bläddra till **exempel för MXCHIP AZ3166 > MQTT** och välj **MQTTClient**. Ett nytt VS Code-fönster öppnas med en projektmapp i den.

> [!NOTE]
> Du kan också öppna exempel från kommando paletten. Använd `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) för att öppna kommando-paletten, Skriv **Arduino** och leta upp och välj **Arduino: exempel**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Bygg och överför Arduino-skissen till DevKit

Typ `Ctrl+P` (MacOS: `Cmd+P` ) som ska köras `task device-upload` . När uppladdningen är klar startar DevKit om och kör skissen.

![Skärm bild som visar ett kommando tolks fönster som laddar upp och kör Arduino-skissen.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Du kan få fel meddelandet "Error: AZ3166: unknowed Package". Det här felet uppstår när tavla-paketets index inte har uppdaterats på rätt sätt. För att lösa det här felet, se [avsnittet utveckling i vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

I VS Code följer du stegen nedan för att öppna och konfigurera den seriella övervakaren:

1. Klicka på `COM[X]` ordet i statusfältet för att ange rätt COM-port med `STMicroelectronics` : ![ skärm bild som visar Visual Studio Code med COM8 S T Micro Electronics valt.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klicka på ikonen för Power-plugin-programmet i statusfältet för att öppna den seriella övervakaren: ![ skärm bilden visar versions sammanfattningen och ikonen för ström kontakten i statusfältet.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. I statusfältet klickar du på det tal som representerar överföringshastigheten och ställer in det på `115200` : ![ skärm bilden visar hur överföringshastigheten visas i Visual Studio Code.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

I den seriella övervakaren visas alla meddelanden som skickas av exempel ritningen. Skissen ansluter DevKit till Wi-Fi. När Wi-Fi anslutningen lyckas skickar skissen ett meddelande till MQTT-koordinatorn. Efter det skickar exemplet upprepade gånger två "iot.eclipse.org"-meddelanden med QoS 0 och QoS 1.

![Skärm bild visar den seriella övervakaren som visar de meddelanden som skickas av skissen.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problem och feedback

Om det uppstår problem kan du läsa [vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller ansluta med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Se även

* [Ansluta IoT DevKit-AZ3166 till Azure IoT Hub i molnet](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Skaka, skaka om för en tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din MXChip IoT-DevKit som en MQTT-klient och använder MQTT-klient biblioteket för att skicka meddelanden till en MQTT-Broker, är här det föreslagna nästa steg: [Översikt över Azure IoT Remote Monitoring Solution Accelerator](/azure/iot-suite/)