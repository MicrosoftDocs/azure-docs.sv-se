---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 30ea75a2df63fa935314fc103fe1e7e092f655b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612994"
---
Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet är skrivet i C# och ingår i Azure IoT-exemplen för C#. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten i Windows behöver du följande program vara installerad på utvecklings datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Ladda ned koden

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT-exempel för C#-lagringsplatsen.

Öppna en kommando tolk i valfri mapp. Kör följande kommando för att klona [Microsoft Azure IoT-exempel för C# (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Skapa koden

Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

1. Öppna projekt filen *Azure-IoT-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.CSPROJ* i Visual Studio 2019.

1. I Visual Studio navigerar du till **Project > termostat-egenskaper > Felsök**. Lägg sedan till följande miljövariabler i projektet:

    | Name | Värde |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | – |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | min-PnP-enhet |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Värdet du antecknade när du slutförde [konfiguration av din miljö](../articles/iot-pnp/set-up-environment.md) |

Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Om du vill spåra kod körningen i Visual Studio på Windows lägger du till en Bryt punkt i `main` funktionen i filen program. cs.

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](../articles/iot-pnp/concepts-modeling-guide.md). [DTDL-Model-filen (Digital Definition Language) för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden ansluter till IoT-hubben med hjälp av standard `CreateFromConnectionString` metoden. Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

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

Modell-ID: t lagras i koden som visas i följande kodfragment:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

Den kod som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventioner.

Exemplet använder ett JSON-bibliotek för att parsa JSON-objekt i de nytto laster som skickas från din IoT Hub:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
