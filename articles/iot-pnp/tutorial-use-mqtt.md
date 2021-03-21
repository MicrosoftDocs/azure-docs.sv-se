---
title: Självstudie – använda MQTT för att skapa en Azure IoT Plug and Play-enhets klient | Microsoft Docs
description: 'Självstudie – Använd MQTT-protokollet direkt för att skapa en IoT Plug and Play enhets klient utan att använda SDK: er för Azure IoT-enheter'
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e56142dfc68767945cd0d08c87f14e19551a156e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741471"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>Självstudie – använda MQTT för att utveckla en IoT Plug and Play enhets klient

Du bör använda en av Azure IoT-enhetens SDK: er för att bygga IoT-Plug and Play enhets klienter om det är möjligt. I scenarier som att använda en minnes begränsad enhet kan du dock behöva använda ett MQTT-bibliotek för att kommunicera med IoT-hubben.

Exemplet i den här självstudien använder [Mosquitto](http://mosquitto.org/) MQTT-biblioteket och Visual Studio. Stegen i den här självstudien förutsätter att du använder Windows på din utvecklings dator.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Hämta och bygg Mosquitto-biblioteket för Sol förmörkelse.
> * Ändra den C-baserade MQTT-exempel koden för att göra enheten till en IoT Plug and Play-enhet.
> * Identifiera de MQTT-ämnen som en IoT Plug and Play-enhet använder.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

Använd *Azure IoT Explorer* -verktyget för att lägga till en ny enhet i IoT Hub. Du konfigurerade IoT-hubben och Azure IoT Explorer-verktyget när du har [konfigurerat din miljö för IoT plug and Play snabb starter och självstudier](set-up-environment.md):

1. Starta **Azure IoT Explorer** -verktyget.
1. På sidan **IoT Hub** väljer du **Visa enheter i den här hubben**.
1. På sidan **enheter** väljer du **+ ny**.
1. Skapa en enhet med namnet *My-MQTT-Device* som använder en automatiskt genererad symmetrisk nyckel.
1. På sidan **enhets identitet** expanderar du **ANSLUTNINGS sträng med SAS-token**.
1. Välj den **primära nyckel** som ska användas som **symmetrisk nyckel**, ange förfallo tiden till 60 minuter och välj **generera**.
1. Kopiera den genererade **SAS-tokens anslutnings sträng**. du använder det här värdet senare i självstudien.

## <a name="clone-sample-repo"></a>Klona exempel lagrings platsen

Använd följande kommando för att klona exempel lagrings platsen till en lämplig plats på den lokala datorn:

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>Installera MQTT-bibliotek

Använd `vcpkg` verktyget för att ladda ned och bygga Mosquitto-biblioteket för Sol förmörkelse.

Använd följande kommando för att klona **Vcpkg** -lagringsplatsen till en lämplig plats på den lokala datorn:

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

Använd följande kommandon för att förbereda `vcpkg` miljön. Du behöver en upphöjd kommando tolk när du kör `vcpkg integrate install` :

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

Använd följande kommando för att ladda ned och bygga Mosquitto-biblioteket för Sol förmörkelse:

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>Migrera exemplet till IoT Plug and Play

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>Granska exempel koden för icke-IoT Plug and Play

Uppdatera koden med information om din IoT-hubb och-enhet innan du skapar och kör den.

Om du vill visa exempel koden i Visual Studio öppnar du lösnings filen *MQTTWin32. SLN* i mappen *IoTMQTTSample\src\Windows* .

I **Solution Explorer** högerklickar du på projektet **TelemetryMQTTWin32** och väljer **Ange som start projekt**.

Öppna käll filen **MQTT_Mosquitto. cpp** i **TelemetryMQTTWin32** -projektet. Uppdatera anslutnings informations definitionerna med enhets informationen som du antecknade tidigare. Ersätt plats hållarna för token sträng för:

* `IOTHUBNAME` identifierare med namnet på din IoT Hub.
* `DEVICEID` identifierare med `my-mqtt-device` .
* `PWD` identifierare med rätt del av anslutnings strängen för SAS-token som du skapade för enheten. Använd delen av anslutnings strängen från `SharedAccessSignature sr=` till slutet.

Kontrol lera att koden fungerar som den ska genom att starta Azure IoT Explorer och börja lyssna på telemetri.

Kör programmet (Ctrl + F5) efter några sekunder visas utdata som ser ut så här:

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="Utdata från MQTT exempel program":::

I Azure IoT Explorer kan du se att enheten inte är en IoT Plug and Play-enhet:

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Icke-IoT Plug and Play-enhet i Azure IoT Explorer":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>Gör enheten till en IoT Plug and Play-enhet

IoT Plug and Play-enheten måste följa en uppsättning enkla konventioner. Om en enhet skickar ett modell-ID när den ansluter blir den en IoT Plug and Play-enhet.

I det här exemplet lägger du till ett modell-ID i MQTT-anslutnings paketet. Du skickar modell-ID: t som QueryString-parameter i `USERNAME` och ändrar `api-version` till `2020-09-30` :

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

Återskapa och kör exemplet.

Enheten, som nu innehåller modell-ID:

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Visa modell-ID i Azure IoT Explorer":::

Nu kan du navigera i IoT Plug and Play-komponenten:

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Visa komponenter i Azure IoT Explorer":::

Du kan nu ändra enhets koden för att implementera telemetri, egenskaper och kommandon som definierats i din modell. Om du vill se en exempel implementering av termostat-enheten med hjälp av Mosquitto-biblioteket läser du [använda MQTT PnP med Azure IoTHub utan IoT SDK i Windows](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32) på GitHub.

> [!NOTE]
>Klienten använder `IoTHubRootCA_Baltimore.pem` rot certifikat filen för att verifiera identiteten för IoT Hub som den ansluter till.

### <a name="mqtt-topics"></a>MQTT ämnen

Följande definitioner gäller för de MQTT-ämnen som enheten använder för att skicka information till IoT Hub. Läs mer i [kommunicera med IoT-hubben med hjälp av MQTT-protokollet](../iot-hub/iot-hub-mqtt-support.md):

* Den `DEVICE_CAPABILITIES_MESSAGE` definierar det ämne som enheten använder för att rapportera de gränssnitt som implementeras.
* Den `DEVICETWIN_PATCH_MESSAGE` definierar det ämne som enheten använder för att rapportera egenskaps uppdateringar till IoT Hub.
* Den `DEVICE_TELEMETRY_MESSAGE` definierar det avsnitt som enheten använder för att skicka telemetri till din IoT-hubb.

Mer information om MQTT finns i MQTT- [exemplen för Azure IoT](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub-lagringsplatsen.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ändrar en MQTT-enhets klient för att följa IoT Plug and Play-konventionerna. Mer information om IoT Hub-stöd för MQTT-protokollet finns i:

> [!div class="nextstepaction"]
> [Kommunicera med IoT-hubben med MQTT-protokollet](../iot-hub/iot-hub-mqtt-support.md)