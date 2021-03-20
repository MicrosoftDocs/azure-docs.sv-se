---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 6b861baea93c2c57b8f66ebac928a7e4fd3adfa8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95510637"
---
IoT Plug and Play fören klar IoT genom att göra det möjligt att interagera med en enhets modell utan kunskaper om den underliggande enhets implementeringen. Den här snabb starten visar hur du använder python för att ansluta till och styra en IoT Plug and Play-enhet som är ansluten till din lösning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

För att slutföra den här snabb starten behöver du python 3,7 på din utvecklings dator. Du kan ladda ned den senaste rekommenderade versionen för flera plattformar från [python.org](https://www.python.org/). Du kan kontrol lera din python-version med följande kommando:  

```cmd/sh
python --version
```

Paketet **Azure-IoT-Device** publiceras som en pip.

I din lokala python-miljö installerar du paketet på följande sätt:

```cmd/sh
pip install azure-iot-device
```

Installera **Azure-IoT-Hub-** paketet genom att köra följande kommando:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>Kör exempel enheten

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Läs mer om exempel konfigurationen i [README-exemplet](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md).

I den här snabb starten använder du en exempel termostat-enhet som skrivits i python som IoT Plug and Play-enhet. Så här kör du exempel enheten:

1. Öppna ett terminalfönster i valfri mapp. Kör följande kommando för att klona [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub-lagringsplatsen till den här platsen:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. Det här terminalfönstret används som **enhetens** Terminal. Gå till mappen för den klonade lagrings platsen och navigera till mappen */Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP* .

1. Kör exempel termostat-enheten med följande kommando:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Du ser meddelanden som säger att enheten har skickat viss information och rapporterat online. Dessa meddelanden indikerar att enheten har börjat skicka telemetridata till hubben och är nu redo att ta emot kommandon och egenskaps uppdateringar. Stäng inte den här terminalen, du behöver den för att bekräfta att tjänst exemplet fungerar.

## <a name="run-the-sample-solution"></a>Kör exempel lösningen

I den här snabb starten använder du en exempel IoT-lösning i python för att interagera med den exempel enhet som du nyss konfigurerade.

1. Öppna ett annat terminalfönster som ska användas som **tjänstens** Terminal.

1. Navigera till mappen */Azure-IoT-SDK-python/Azure-IoT-Hub/samples* för den klonade python SDK-lagringsplatsen.

1. Öppna filen *registry_manager_pnp_sample. py* och granska koden. Det här exemplet visar hur du använder klassen **IoTHubRegistryManager** för att interagera med din IoT plug and Play-enhet.

> [!NOTE]
> Dessa tjänst exempel använder klassen **IoTHubRegistryManager** från den **IoT Hub tjänst klienten**. Mer information om API: er, inklusive digitala dubbla API: er, finns i [service Developer-guiden](../articles/iot-pnp/concepts-developer-guide-service.md).

### <a name="get-the-device-twin"></a>Hämta enheten med dubbla

I [Konfigurera din miljö för iot plug and Play snabb starter och självstudier](../articles/iot-pnp/set-up-environment.md) som du har skapat två miljövariabler för att konfigurera exemplet för att ansluta till din IoT-hubb och-enhet:

* **IOTHUB_CONNECTION_STRING**: den IoT Hub-anslutningssträng som du antecknade tidigare.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Använd följande kommando i **service-** terminalen för att köra det här exemplet:

```cmd/sh
set IOTHUB_METHOD_NAME="getMaxMinReport"
set IOTHUB_METHOD_PAYLOAD="hello world"
python registry_manager_pnp_sample.py
```

> [!NOTE]
> Om du kör det här exemplet i Linux använder du `export` i stället för `set` .

Resultatet visar att enheten är dubbel och skriver ut dess modell-ID:

```cmd/sh
The Model ID for this device is:
dtmi:com:example:Thermostat;1
```

I följande kodfragment visas exempel koden från *registry_manager_pnp_sample. py*:

```python
    # Create IoTHubRegistryManager
    iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

    # Get device twin
    twin = iothub_registry_manager.get_twin(device_id)
    print("The device twin is: ")
    print("")
    print(twin)
    print("")

    # Print the device's model ID
    additional_props = twin.additional_properties
    if "modelId" in additional_props:
        print("The Model ID for this device is:")
        print(additional_props["modelId"])
        print("")
```

### <a name="update-a-device-twin"></a>Uppdatera en enhet med dubbla

Det här exemplet visar hur du uppdaterar den `targetTemperature` skrivbara egenskapen i enheten:

```python
    # Update twin
    twin_patch = Twin()
    twin_patch.properties = TwinProperties(
        desired={"targetTemperature": 42}
    )  # this is relevant for the thermostat device sample
    updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
    print("The twin patch has been successfully applied")
    print("")
```

Du kan kontrol lera att uppdateringen används i **enhetens** Terminal som visar följande utdata:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
```

**Tjänsten** Terminal bekräftar att korrigeringen lyckades:

```cmd/sh
The twin patch has been successfully applied
```

### <a name="invoke-a-command"></a>Anropa ett kommando

Exemplet anropar sedan ett kommando:

**Tjänsten** Terminal visar ett bekräftelse meddelande från enheten:

```cmd/sh
The device method has been successfully invoked
```

I **enhetens** Terminal ser du att enheten tar emot kommandot:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
```
