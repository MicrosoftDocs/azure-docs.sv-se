---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: b7b0cfa20257ad07d8418c39af68724d613adf41
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821043"
---
Den här snabbstarten visar hur du skapar ett exempelprogram för IoT Plug and Play-enheter, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa telemetrin som skickas. Exempelprogrammet är skrivet i C# och ingår i Azure IoT-exempel för C#. En lösningsbyggare kan använda verktyget Azure IoT Explorer för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhetskod.

[![Sökkod](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabbstarten i Windows behöver du följande programvara installerad på utvecklingsdatorn:

* [Visual Studio (Community, Professional eller Enterprise).](https://visualstudio.microsoft.com/downloads/)
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Ladda ned koden

I den här snabbstarten förbereder du en utvecklingsmiljö som du kan använda för att klona och skapa Azure IoT-exempel för C#-lagringsplatsen.

Öppna en kommandotolk i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT Samples for C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplats till den här platsen:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Skapa koden

Nu kan du skapa exemplet i Visual Studio köra det i felsökningsläge.

1. Öppna projektfilen *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Termostat\Termostat.csproj* i Visual Studio 2019.

1. I Visual Studio du till **Project > Termostategenskaper > Felsöka**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Dps |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Det värde som du antecknade när du [slutförde Konfigurera din miljö](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Det värde som du antecknade när du [slutförde Konfigurera din miljö](../articles/iot-pnp/set-up-environment.md) |

Nu kan du skapa exemplet i Visual Studio och köra det i felsökningsläge.

## <a name="run-the-device-sample"></a>Kör enhetsexempel

Om du vill spåra kodkörningen Visual Studio i Windows lägger du till en `main` brytpunkt i funktionen i filen program.cs.

Enheten är nu redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till hubben. Låt exemplet fortsätta köras när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använda Azure IoT Explorer för att verifiera koden

När enhetsklientexempel startar använder du verktyget Azure IoT Explorer för att kontrollera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en enkel IoT Plug and Play termostatenhet. Modellen som det här exemplet implementerar använder inte [](../articles/iot-pnp/concepts-modeling-guide.md)IoT Plug and Play komponenter . Modellfilen [Digital Twins DTDL (Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) för termostatenheten definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhetskoden ansluter till din IoT Hub med hjälp av `CreateFromConnectionString` standardmetoden. Enheten skickar modell-ID:t för den DTDL-modell som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play enhet:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

Modell-ID:t lagras i koden enligt följande kodavsnitt:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Koden som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventionerna.

Exemplet använder ett JSON-bibliotek för att parsa JSON-objekt i nyttolaster som skickas från din IoT Hub:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
