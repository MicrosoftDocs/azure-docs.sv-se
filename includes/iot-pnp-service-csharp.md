---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4308dd2b63b33604af83b360e5c1c0f02a3dec27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95510641"
---
IoT Plug and Play fören klar IoT genom att göra det möjligt att interagera med enhetens funktioner utan att du behöver ha kunskap om den underliggande enhets implementeringen. Den här snabb starten visar hur du använder C# för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten i Windows behöver du följande program vara installerad på utvecklings datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klona SDK-lagringsplatsen med exempel koden

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md)har du redan klonat lagrings platsen.

Klona exemplen från Azure IoT-exempel för C# GitHub-lagringsplatsen. Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT-exempel för .net](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplats:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel termostat-enhet som är skriven i C# som IoT Plug and Play-enhet. Så här kör du exempel enheten:

1. Öppna projekt filen *Azure-IoT-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.CSPROJ* i Visual Studio 2019.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | – |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | min-PnP-enhet |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |

1. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här instansen av Visual Studio. du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I [Konfigurera din miljö för iot plug and Play snabb starter och självstudier](../articles/iot-pnp/set-up-environment.md) som du har skapat två miljövariabler för att konfigurera exemplet för att ansluta till din IoT-hubb och-enhet:

* **IOTHUB_CONNECTION_STRING**: den IoT Hub-anslutningssträng som du antecknade tidigare.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

I den här snabb starten använder du en exempel-IoT-lösning i C# för att interagera med den exempel enhet som du nyss konfigurerade.

1. Öppna projektet *Azure-IoT-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.CSPROJ* i en annan instans av Visual Studio.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | min-PnP-enhet |
    | IOTHUB_CONNECTION_STRING | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |

1. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

### <a name="get-device-twin"></a>Hämta enhetens dubbla

Följande kodfragment visar hur tjänst programmet hämtar enheten dubbla:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> I det här exemplet används namn området **Microsoft. Azure. devices. client** från **IoT Hub-tjänst klienten**. Mer information om API: er, inklusive digitala dubbla API: er, finns i [service Developer-guiden](../articles/iot-pnp/concepts-developer-guide-service.md).

Den här koden genererar följande utdata:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

Följande kodfragment visar hur du använder en *korrigering* för att uppdatera egenskaper via enheten:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Den här koden genererar följande utdata från enheten när tjänsten uppdaterar `targetTemperature` egenskapen:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Anropa ett kommando

Följande kodfragment visar hur du anropar ett kommando:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Den här koden genererar följande utdata från enheten när tjänsten anropar `getMaxMinReport` kommandot:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```
