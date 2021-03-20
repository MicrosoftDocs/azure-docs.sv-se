---
title: Ansluta Raspberry Pi Web Simulator till Azure IoT Hub (Node.js)
description: Anslut Raspberry Pi Web Simulator till Azure IoT Hub för Raspberry Pi för att skicka data till Azure-molnet.
author: wesmc7777
manager: philmea
keywords: Raspberry Pi-simulator, Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi skicka data till molnet, Raspberry Pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: 702dee108577665eded6dd1a92203236d74e866e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308358"
---
# <a name="connect-raspberry-pi-online-simulator-to-azure-iot-hub-nodejs"></a>Ansluta Raspberry Pi online Simulator till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi online Simulator. Du lär dig sedan att sömlöst ansluta PI-simulatorn till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md).

<p>
<div id="diag" style="width:100%; text-align:center">
<img src="media/iot-hub-raspberry-pi-web-simulator/3-banner.png" alt="Connect Raspberry Pi web simulator to Azure IoT Hub" width="400">
</div>
</p>
<p>
<div id="button" style="width:100%; text-align:center">
<a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#getstarted" target="_blank">
<img src="media/iot-hub-raspberry-pi-web-simulator/6-button-default.png" alt="Start Raspberry Pi simulator" width="400" onmouseover="this.src='media/iot-hub-raspberry-pi-web-simulator/5-button-click.png';" onmouseout="this.src='media/iot-hub-raspberry-pi-web-simulator/6-button-default.png';">
</a>
</div>
</p>

Om du har fysiska enheter kan du gå till [Anslut Raspberry Pi till Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md) för att komma igång.

## <a name="what-you-do"></a>Vad du gör

* Lär dig grunderna i Raspberry Pi online Simulator.

* Skapa en IoT-hubb.

* Registrera en enhet för PI i din IoT-hubb.

* Kör ett exempel program på Pi för att skicka simulerade sensor data till IoT Hub.

Anslut simulerade Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempel program med simulatorn för att generera sensor data. Slutligen skickar du sensor data till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhets anslutnings sträng. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt Azures utvärderings konto](https://azure.microsoft.com/free/) på bara några minuter.

* Så här arbetar du med Raspberry Pi online Simulator.

* Skicka sensor data till din IoT-hubb.

## <a name="overview-of-raspberry-pi-web-simulator"></a>Översikt över Raspberry Pi Web Simulator

Klicka på knappen för att starta Raspberry Pi online Simulator.

> [!div class="button"]
> <a href="https://azure-samples.github.io/raspberry-pi-web-simulator/#GetStarted" target="_blank">Starta Raspberry Pi-Simulator</a>

Det finns tre områden i webb simulatorn.

1. Sammansättningsområdeet – standardkretsen är att en Pi ansluts till en BME280-sensor och en LED. Området är låst i förhandsversionen, så för tillfället går det inte att göra anpassningar.

2. Kodningsområdet – en onlinekodredigerare så att du kan köra med Raspberry Pi. Standardexempelprogrammet hjälper till att samla in sensordata från BME280-sensorn och skickar till din Azure IoT Hub. Programmet är helt kompatibelt med verkliga Pi-enheter. 

3. Integrerat konsolfönster – visar utdata för din kod. Längst upp i det här fönstret finns tre knappar.

   * **Run** (Kör) – kör programmet i kodningsområdet.

   * **Reset** (Återställ) – återställ kodningsområdet till standardexempelprogrammet.

   * **Fold/Expand** (Vik/expandera) – på höger sida finns det en knapp som viker/expanderar konsolfönstret.

> [!NOTE]
> Raspberry Pi Web Simulator är nu tillgänglig i för hands versionen. Vi vill höra din röst i [gitter-chatroom](https://gitter.im/Microsoft/raspberry-pi-web-simulator). Käll koden är offentlig på [GitHub](https://github.com/Azure-Samples/raspberry-pi-web-simulator).

![Översikt över PI online Simulator](media/iot-hub-raspberry-pi-web-simulator/0-overview.png)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="run-a-sample-application-on-pi-web-simulator"></a>Köra ett exempelprogram på Pi-webbsimulatorn

1. I kodningsområdet ser du till att du arbetar med det standardmässiga exempelprogrammet. Ersätt platshållaren på rad 15 med Azure IoT-hubbens anslutningssträng.
1. 
   ![Ersätta enhetens anslutningssträng](media/iot-hub-raspberry-pi-web-simulator/1-connectionstring.png)

2. Välj **Kör** eller Skriv `npm start` för att köra programmet.

Du bör se följande utdata som visar sensor data och meddelanden som skickas till din IoT Hub ![ -utdata från Raspberry Pi till din IoT-hubb](media/iot-hub-raspberry-pi-web-simulator/2-run-application.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelanden som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av din IoT Hub från den simulerade enheten är att använda Azure IoT-verktyg för Visual Studio Code. Läs mer i [använda Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt om du vill ha fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempel program för att samla in sensor data och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
