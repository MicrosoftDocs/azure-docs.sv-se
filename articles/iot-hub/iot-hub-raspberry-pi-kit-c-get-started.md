---
title: Anslut Raspberry Pi till Azure IoT Hub med C | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi för att skicka data till Azures moln plattform
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97804062"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Anslut Raspberry Pi till Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi som kör Raspbian. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel går du till [Windows Dev Center](https://www.windowsondevices.com/).

Har du inget paket än? Prova [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp ett nytt paket [här](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.

* Registrera en enhet för PI i din IoT-hubb.

* Setup Raspberry Pi.

* Kör ett exempel program på Pi för att skicka sensor data till IoT Hub.

Anslut Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempel program på Pi för att samla in temperatur-och fuktighets data från en BME280-sensor. Slutligen skickar du sensor data till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhets anslutnings sträng.

* Hur man ansluter PI med en BME280-sensor.

* Samla in sensor data genom att köra ett exempel program på Pi.

* Skicka sensor data till din IoT-hubb.

## <a name="what-you-need"></a>Detta behöver du

![Detta behöver du](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 eller Raspberry Pi 3-tavlan.

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt Azures utvärderings konto](https://azure.microsoft.com/free/) på bara några minuter.

* En övervakare, ett USB-tangentbord och en mus som ansluter till Pi.

* En Mac eller en dator som kör Windows eller Linux.

* En Internet anslutning.

* Ett 16 GB eller högre microSD-kort.

* Ett USB-SD-kort eller microSD-kort för att bränna operativ Systems avbildningen på microSD-kortet.

* En 5-volt 2-amp-strömförsörjning med 6-fot Micro USB-kabeln.

Följande objekt är valfria:

* En monterad Adafruit BME280 temperatur, tryck och fuktighets sensor.

* En breadboard.

* 6 F/M hopp ledningar.

* En diffus 10-mm-indikator.

> [!NOTE]
> Dessa objekt är valfria eftersom kod exemplet stöder simulerade sensor data.
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurera Raspberry Pi

Nu har du konfigurerat Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativ systemet Raspbian för PI

Förbered microSD-kortet för installation av Raspbian-avbildningen.

1. Ladda ned Raspbian.

   1. [Ladda ned Raspbian-sträckning med Desktop](https://www.raspberrypi.org/software/) (. zip-filen).

   2. Extrahera Raspbian-avbildningen till en mapp på datorn.

2. Installera Raspbian på microSD-kortet.

   1. [Hämta och installera verktyget etsadr SD Card-brännare](https://etcher.io/).

   2. Kör Etsader och välj Raspbian-avbildningen som du extraherade i steg 1.

   3. Välj microSD-kort enheten. Observera att Etsadren kanske redan har valt rätt enhet.

   4. Klicka på Flash för att installera Raspbian på microSD-kortet.

   5. Ta bort microSD-kortet från datorn när installationen är klar. Det är säkert att ta bort microSD-kortet direkt eftersom Etsadren automatiskt matar ut eller demonterar microSD-kortet vid slut för ande.

   6. Sätt i microSD-kortet i PI.

### <a name="enable-ssh-and-spi"></a>Aktivera SSH och SPI

1. Anslut Pi till skärmen, tangent bordet och musen, starta Pi och logga sedan in på Raspbian genom att använda `pi` som användar namn och `raspberry` lösen ord.
 
2. Klicka på ikonen Raspberry > **Inställningar**  >  **Raspberry Pi-konfiguration**.

   ![Menyn Raspbian-inställningar](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. På fliken **gränssnitt** ställer du in **SPI** och **SSH** för att **Aktivera** och klickar sedan på **OK**. Om du inte har fysiska sensorer och vill använda simulerade sensor data är det här steget valfritt.

   ![Aktivera SPI och SSH på Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om du vill aktivera SSH och SPI kan du hitta fler referens dokument för [RaspberryPi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [RASPI-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Anslut sensorn till Pi

Använd breadboard och bygel-kablar för att ansluta en indikator och en BME280 till Pi på följande sätt. [Hoppa över det här avsnittet](#connect-pi-to-the-network)om du inte har sensorn.

![Raspberry Pi och sensor anslutning](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280-sensorn kan samla in temperatur-och fuktighets data. INDIKATORn kommer att blinka om det finns en kommunikation mellan enheten och molnet.

För sensor stift använder du följande kablar:

| Start (sensor & lampa)     | End (tavla)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| Indikator för VDD (PIN-5G)         | GPIO 4 (PIN 7)         | Vit kabel   |
| Indikator för GND (PIN-6G)         | GND (PIN 6)            | Svart kabel   |
| VDD (PIN-18F)            | 3,3 v PWR (stift 17)      | Vit kabel   |
| GND (PIN-20F)            | GND (PIN-kod 20)           | Svart kabel   |
| SCK (PIN-21F)            | SPI0 SCLK (stift 23)     | Orange kabel  |
| SDO (PIN-22F)            | SPI0 MISO (PIN-kod 21)     | Gul kabel  |
| SDI (PIN-23F)            | SPI0 MOSI (PIN 19)     | Grön kabel   |
| CS (PIN-24F)             | SPI0 CS (PIN 24)       | Blå kabel    |

Klicka om du vill visa [Raspberry Pi 2 & 3 PIN-mappningar](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) för din referens.

När du har anslutit BME280 till din Raspberry Pi bör det vara likadant som under bilden.

![Anslutna Pi och BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera PI med mikrousb-kabeln och strömförsörjningen. Använd Ethernet-kabeln för att ansluta Pi till det kabelanslutna nätverket eller följ [anvisningarna från Raspberry Pi-stiftelsen](https://www.raspberrypi.org/documentation/configuration/wireless/) för att ansluta Pi till det trådlösa nätverket. När din PI har lyckats ansluta till nätverket måste du anteckna [IP-adressen för din PI](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Ansluten till kabelanslutet nätverk](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Köra ett exempel program på Pi

### <a name="sign-into-your-raspberry-pi"></a>Logga in på din Raspberry Pi

1. Använd någon av följande SSH-klienter från värddatorn för att ansluta till din Raspberry Pi.
   
   **Windows-användare**
   1. Ladda ned och installera [SparaTillFil](https://www.putty.org/) för Windows. 
   1. Kopiera IP-adressen för din PI till avsnittet värd namn (eller IP-adress) och välj SSH som Anslutnings typ.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-och Ubuntu-användare**

   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` för att ansluta PI via SSH.
   > [!NOTE]
   > Standard användar namnet är `pi` och lösen ordet är `raspberry` .


### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Klona exempel programmet genom att köra följande kommando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Kör installations skript:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Om du **inte har en fysisk BME280** kan du använda parametern--simulerade data som kommando rads parameter för att simulera temperatur&fuktighets data. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Skapa och kör exempel programmet

1. Bygg exempel programmet genom att köra följande kommando:

   ```bash
   cmake . && make
   ```
   
   ![Bygg utdata](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Kör exempel programmet genom att köra följande kommando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Se till att du kopierar – klistra in enhets anslutnings strängen i enkla citat tecken.
   >

Du bör se följande utdata som visar de sensordata och de meddelanden som skickas till din IoT-hubb.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelanden som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av din IoT Hub från din enhet är att använda Azure IoT-verktyg för Visual Studio Code. Läs mer i [använda Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt om du vill ha fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempel program för att samla in sensor data och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]