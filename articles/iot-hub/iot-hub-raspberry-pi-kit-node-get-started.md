---
title: Ansluta Raspberry Pi till Azure IoT Hub i molnet (Node.js)
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi för att skicka data till Azure Cloud Platform i den här självstudien.
author: wesmc7777
manager: eliotgra
keywords: Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi skicka data till molnet, Raspberry Pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97802532"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Anslut Raspberry Pi till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi som kör Raspbian. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel går du till [Windows Dev Center](https://www.windowsondevices.com/).

Har du inget paket än? Prova [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp ett nytt paket [här](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.

* Registrera en enhet för PI i din IoT-hubb.

* Konfigurera Raspberry Pi.

* Kör ett exempel program på Pi för att skicka sensor data till IoT Hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhets anslutnings sträng.

* Hur man ansluter PI med en BME280-sensor.

* Samla in sensor data genom att köra ett exempel program på Pi.

* Skicka sensor data till din IoT-hubb.

## <a name="what-you-need"></a>Detta behöver du

![Detta behöver du](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* En Raspberry Pi 2 eller Raspberry Pi 3-tavla.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* En övervakare, ett USB-tangentbord och en mus som ansluter till Pi.

* En Mac-eller PC-dator som kör Windows eller Linux.

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
> Om du inte har de valfria objekten kan du använda simulerade sensor data.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurera Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativ systemet Raspbian för PI

Förbered microSD-kortet för installation av Raspbian-avbildningen.

1. Ladda ned Raspbian.

   a. [Raspbian Buster med Desktop](https://www.raspberrypi.org/software/) (zip-filen).

   b. Extrahera Raspbian-avbildningen till en mapp på datorn.

2. Installera Raspbian på microSD-kortet.

   a. [Hämta och installera verktyget etsadr SD Card-brännare](https://etcher.io/).

   b. Kör Etsader och välj Raspbian-avbildningen som du extraherade i steg 1.

   c. Välj microSD-kort enheten. Etsadren kanske redan har valt rätt enhet.

   d. Klicka på Flash för att installera Raspbian på microSD-kortet.

   e. Ta bort microSD-kortet från datorn när installationen är klar. Det är säkert att ta bort microSD-kortet direkt eftersom Etsadren automatiskt matar ut eller demonterar microSD-kortet vid slut för ande.

   f. Sätt i microSD-kortet i PI.

### <a name="enable-ssh-and-i2c"></a>Aktivera SSH och I2C

1. Anslut Pi till skärmen, tangent bordet och musen.

2. Starta Pi och logga sedan in på Raspbian genom `pi` att använda som användar namn och `raspberry` lösen ord.

3. Klicka på ikonen Raspberry > **Inställningar**  >  **Raspberry Pi-konfiguration**.

   ![Menyn Raspbian-inställningar](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. På fliken **gränssnitt** ställer du in **I2C** och **SSH** för att **Aktivera** och klickar sedan på **OK**. Om du inte har fysiska sensorer och vill använda simulerade sensor data är det här steget valfritt.

   ![Aktivera I2C och SSH på Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om du vill aktivera SSH och I2C kan du hitta fler referens dokument på [RaspberryPi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Anslut sensorn till Pi

Använd breadboard och bygel-kablar för att ansluta en indikator och en BME280 till Pi på följande sätt. [Hoppa över det här avsnittet](#connect-pi-to-the-network)om du inte har sensorn.

![Raspberry Pi och sensor anslutning](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280-sensorn kan samla in temperatur-och fuktighets data. INDIKATORn blinkar när enheten skickar ett meddelande till molnet.

För sensor stift använder du följande kablar:

| Start (sensor & lampa)     | End (tavla)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-5G)             | 3,3 v-PWR (stift 1)       | Vit kabel   |
| GND (PIN-7G)             | GND (PIN 6)            | Brun-kabel   |
| SDI (PIN 10G)            | I2C1-SDA (PIN 3)       | Röd kabel     |
| SCK (PIN-8G)             | I2C1 SCL (stift 5)       | Orange kabel  |
| Indikator för VDD (PIN-18F)        | GPIO 24 (PIN 18)       | Vit kabel   |
| Indikator för GND (PIN-17F)        | GND (PIN-kod 20)           | Svart kabel   |

Klicka om du vill visa [Raspberry Pi 2 & 3 PIN-mappningar](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) för din referens.

När du har anslutit BME280 till din Raspberry Pi bör det vara likadant som under bilden.

![Anslutna Pi och BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera PI med mikrousb-kabeln och strömförsörjningen. Använd Ethernet-kabeln för att ansluta Pi till det kabelanslutna nätverket eller följ [anvisningarna från Raspberry Pi-stiftelsen](https://www.raspberrypi.org/documentation/configuration/wireless/) för att ansluta Pi till det trådlösa nätverket. När din PI har lyckats ansluta till nätverket måste du anteckna [IP-adressen för din PI](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Ansluten till kabelanslutet nätverk](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Kontrol lera att PI är anslutet till samma nätverk som din dator. Om datorn till exempel är ansluten till ett trådlöst nätverk medan PI är anslutet till ett kabelanslutet nätverk kanske du inte ser IP-adressen i devdisco-utdata.

## <a name="run-a-sample-application-on-pi"></a>Köra ett exempel program på Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klona exempel programmet och installera nödvändiga paket

1. Anslut till din Raspberry Pi med någon av följande SSH-klienter från värddatorn:

   **Windows-användare**

   a. Ladda ned och installera [SparaTillFil](https://www.putty.org/) för Windows.

   b. Kopiera IP-adressen för din PI till avsnittet värd namn (eller IP-adress) och välj SSH som Anslutnings typ.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac-och Ubuntu-användare**

   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` för att ansluta PI via SSH.

   > [!NOTE]
   > Standard användar namnet är `pi` och lösen ordet är `raspberry` .

2. Installera Node.js och NPM till din PI.

   Kontrol lera först Node.js-versionen.

   ```bash
   node -v
   ```

   Om versionen är lägre än 10. x, eller om det inte finns någon Node.js på din PI, installerar du den senaste versionen.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klona exempel programmet.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Installera alla paket för exemplet. Installationen inkluderar Azure IoT-enhetens SDK, BME280 sensor Library och kabel PI-biblioteket.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Det kan ta flera minuter att slutföra installations processen, beroende på din nätverks anslutning.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Öppna konfigurations filen genom att köra följande kommandon:

   ```bash
   nano config.json
   ```

   ![Konfigurations fil](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Det finns två objekt i den här filen som du kan konfigurera. Det första är `interval` , som definierar tidsintervall (i millisekunder) mellan meddelanden som skickas till molnet. Den andra är `simulatedData` , vilket är ett booleskt värde för om du vill använda simulerade sensor data eller inte.

   Om du **inte har sensorn** ställer du in `simulatedData` värdet på `true` för att göra exempel programmet att skapa och använda simulerade sensor data.

   *Obs: I2C-adressen som används i den här självstudien är 0x77 som standard. Beroende på din konfiguration kan det också vara 0x76: om du stöter på ett I2C-fel kan du försöka ändra värdet till 118 och se om det fungerar bättre. Om du vill se vilken adress som används av sensorn kör du `sudo i2cdetect -y 1` i ett gränssnitt på Raspberry Pi*

2. Spara och avsluta genom att skriva Control-O > anger > Control-X.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

Kör exempel programmet genom att köra följande kommando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Se till att du kopierar – klistra in enhets anslutnings strängen i enkla citat tecken.

Du bör se följande utdata som visar de sensordata och de meddelanden som skickas till din IoT-hubb.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelanden som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av din IoT Hub från din enhet är att använda Azure IoT-verktyg för Visual Studio Code. Läs mer i [använda Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt om du vill ha fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempel program för att samla in sensor data och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
