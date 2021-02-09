---
title: Självstudie – ansluta en allmän Azure IoT Plug and Play-modul | Microsoft Docs
description: Självstudie – använda exempel C# IoT Plug and Play enhets kod i en allmän modul.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831219"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Självstudie: ansluta en IoT Plug and Play-modul (C#)

Den här självstudien visar hur du ansluter en allmän IoT Plug and Play- [modul](../iot-hub/iot-hub-devguide-module-twins.md).

En enhet är en IoT Plug and Play-enhet om den publicerar sitt modell-ID när den ansluter till en IoT-hubb och implementerar de egenskaper och metoder som beskrivs i DTDL-modellen (Digital-definitions språk) som identifieras av modell-ID: t. Mer information om hur enheter använder ett DTDL och modell-ID finns i [IoT plug and Play Developer Guide](./concepts-developer-guide-device.md). Moduler använder modell-ID: n och DTDL modeller på samma sätt.

För att demonstrera hur du implementerar en IoT Plug and Play-modul visar den här självstudien hur du:

> [!div class="checklist"]
> * Lägg till en enhet med en modul i IoT Hub.
> * Konvertera termostat C#-enhets exemplet till en allmän modul.
> * Använd service SDK för att interagera med modulen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Använd Azure IoT Explorer-verktyget för att lägga till en ny enhet som kallas **min-module-enhet** i IoT Hub.

Lägg till en modul som kallas **My-module** i **min-modul-Device**:

1. I Azure IoT Explorer-verktyget, navigerar du till enheten för **min-modul-enhet** .

1. Välj **modulens identitet** och välj sedan **+ Lägg till**.

1. Ange **My-module** som modulens identitets namn och välj **Spara**.

1. Välj **min-modul** i listan med modul identiteter. Kopiera sedan den primära anslutnings strängen. Du använder den här modulens anslutnings sträng senare i den här självstudien.

1. Välj fliken med **dubbla** flikar och Lägg märke till att det inte finns några önskade eller rapporterade egenskaper:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Ladda ned koden

Om du inte redan har gjort det, klona Azure IoT Hub Device C# SDK GitHub-lagringsplatsen till den lokala datorn:

Öppna en kommando tolk i valfri mapp. Använd följande kommando för att klona [Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Förbereda projektet

Så här öppnar och förbereder du exempelprojektet:

1. Öppna projekt filen *Azure-IoT-SDK-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.CSPROJ* i Visual Studio 2019.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Namn | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Begär |
    | IOTHUB_MODULE_CONNECTION_STRING | Den anslutnings sträng för modul som du antecknade tidigare |

    Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Ändra koden

Ändra koden så att den fungerar som en modul i stället för en enhet:

1. Öppna *parameter.cs* i Visual Studio och ändra raden som anger variabeln **PrimaryConnectionString** enligt följande:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. I Visual Studio öppnar du *program.cs* och ersätter de sju instanserna av `DeviceClient` klassen med `ModuleClient` klassen.

    > [!TIP]
    > Använd Visual Studio search och ersätt funktionen med **match-Case** och **Matcha hela ord** som är aktiverade för att ersätta `DeviceClient` med `ModuleClient` .

1. Öppna *thermostat.cs* i Visual Studio och ersätt båda förekomsterna av `DeviceClient` klassen med `ModuleClient` klassen enligt följande.

1. Spara ändringarna av de filer som du ändrade.

Om du kör koden och sedan använder Azure IoT Explorer för att visa den uppdaterade modulen, ser du den uppdaterade enheten med modell-ID: t och den rapporterade egenskapen för modulen:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interagera med en enhets modul

Med tjänst-SDK: er kan du hämta modell-ID: t för anslutna IoT Plug and Play enheter och moduler. Du kan använda tjänst-SDK: er för att ange skrivbara egenskaper och anropa kommandon:

1. Öppna projektet *Azure-IoT-SDK-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.CSPROJ* i en annan instans av Visual Studio.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Namn | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | min-modul-enhet |
    | IOTHUB_CONNECTION_STRING | Värdet du antecknade när du slutförde [konfiguration av din miljö](set-up-environment.md) |

    > [!TIP]
    > Du kan också hitta anslutnings strängen för IoT Hub i Azure IoT Explorer-verktyget.

1. Öppna filen *program.cs* och ändra raden som anropar ett kommando enligt följande:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. I *program.cs* -filen ändrar du raden som hämtar enheten på följande sätt:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Se till att modulens klient exempel fortfarande körs och kör sedan tjänst exemplet. Utdata från tjänst exemplet visar modell-ID: t från enheten, och kommando anropet:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    Utdata från modulen klient visar kommando hanterarens svar:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Konvertera till en IoT Edge modul

Om du vill konvertera det här exemplet så att det fungerar som en IoT-Plug and Play IoT Edge modul måste du Använd programmet. Du behöver inte göra några ytterligare kod ändringar. Miljövariabeln för anslutnings strängen injiceras av IoT Edge runtime vid start. Mer information finns i [använda Visual Studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Information om hur du distribuerar en container-modul finns i:

* [Kör Azure IoT Edge på Ubuntu Virtual Machines](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Installera Azure IoT Edge runtime på Debian-baserade Linux-system](../iot-edge/how-to-install-iot-edge.md).

Du kan använda Azure IoT Explorer-verktyget för att se:

* Modell-ID: t för din IoT Edges enhet i modul två.
* Telemetri från IoT Edge-enheten.
* IoT Edge moduls dubbla egenskaps uppdateringar utlöser IoT Plug and Play-meddelanden.
* IoT Edge-modulen reagerar på IoT Plug and Play-kommandon.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet med moduler till en IoT-hubb. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Modeling Developer Guide](./concepts-developer-guide-device.md)