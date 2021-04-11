---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612926"
---
I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet i C# och ingår i Azure IoT-enhetens SDK för C#. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här kursen får du:

> [!div class="checklist"]
> * Hämta exempel koden.
> * Bygg exempel koden.
> * Kör enhets exempel programmet och kontrol lera att det ansluter till din IoT-hubb.
> * Granska käll koden.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klona SDK-lagringsplatsen med exempel koden

Om du har slutfört [snabb starten: ansluta ett exempel på IoT plug and Play Device-program som körs på Windows till IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md)har du redan klonat lagrings platsen.

Klona exemplen från Azure IoT-exempel för C# GitHub-lagringsplatsen. Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT-exempel för .net](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplats:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

I den här snabb starten använder du en exempel temperatur styrenhets enhet som är skriven i C# som IoT Plug and Play-enheten. Så här kör du exempel enheten:

1. Öppna projekt filen *Azure-IoT-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.CSPROJ* i Visual Studio 2019.

1. I Visual Studio navigerar du till **Project > TemperatureController-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | – |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | min-PnP-enhet |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |


1. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här instansen av Visual Studio. du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](../articles/iot-pnp/concepts-modeling-guide.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden ansluter till IoT-hubben med hjälp av standard `CreateFromConnectionString` metoden. Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

Modell-ID: t lagras i koden som visas i följande kodfragment:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

När enheten ansluter till din IoT-hubb registreras kommando hanterare i koden. `reboot`Kommandot definieras i standard komponenten. `getMaxMinReport`Kommandot definieras i var och en av de två termostat-komponenterna:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Det finns separata hanterare för önskade egenskaps uppdateringar för de två termostat-komponenterna:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Exempel koden skickar telemetri från varje termostat-komponent:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync`Metoden använder `PnpHhelper` klassen för att skapa meddelanden för varje komponent:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Klassen innehåller andra exempel metoder som du kan använda med en modell för flera komponenter.

Använd Azure IoT Explorer-verktyget för att Visa telemetri och egenskaper från de två termostat-komponenterna:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Enhet för flera komponenter i Azure IoT Explorer":::

Du kan också använda Azure IoT Explorer-verktyget för att anropa kommandon i någon av de två termostat-komponenterna eller i standard komponenten.
