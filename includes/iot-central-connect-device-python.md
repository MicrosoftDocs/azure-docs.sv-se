---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: d878c7abf025b5c66790a96f9f921f669dcdf1ef
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491111"
---
## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln behöver du följande resurser:

* Ett Azure IoT Central-program som skapats med hjälp av den **anpassade program** mal len. Mer information finns i [snabbstarten om att skapa ett program](../articles/iot-central/core/quick-deploy-iot-central.md). Programmet måste ha skapats den 14 juli 2020 eller senare.
* En utvecklings dator med [python](https://www.python.org/) version 3,7 eller senare installerad. Du kan köra `python --version` på kommando raden för att kontrol lera din version. Python är tillgängligt för många olika operativ system. Anvisningarna i den här självstudien förutsätter att du kör **python** -kommandot i kommando tolken i Windows.
* En lokal kopia av [Microsoft Azure IoT SDK för python](https://github.com/Azure/azure-iot-sdk-python) GitHub-lagringsplatsen som innehåller exempel koden. Använd den här länken för att ladda ned en kopia av lagrings platsen: [Ladda ned ZIP](https://github.com/Azure/azure-iot-sdk-python/archive/master.zip). Zippa sedan upp filen till en lämplig plats på den lokala datorn.

## <a name="review-the-code"></a>Granska koden

I kopian av Microsoft Azure IoT SDK för python som du laddade ned tidigare, öppnar du filen *Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP/temp_controller_with_thermostats. py* i en text redigerare.

När du kör exemplet för att ansluta till IoT Central, används enhets etablerings tjänsten (DPS) för att registrera enheten och skapa en anslutnings sträng. Exemplet hämtar den information om DPS-anslutningen som krävs från kommando rads miljön.

`main`Funktionen:

* Använder DPS för att etablera enheten. Etablerings informationen innehåller modell-ID: t. IoT Central använder modell-ID: t för att identifiera eller generera enhets mal len för den här enheten. Mer information finns i [associera en enhet med en enhets mall](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Skapar ett- `Device_client` objekt och anger `dtmi:com:example:TemperatureController;2` modell-ID: t innan det öppnar anslutningen.
* Skickar inledande egenskaps värden till IoT Central. Den använder `pnp_helper` för att skapa korrigeringarna.
* Skapar lyssnare för `getMaxMinReport` `reboot` kommandona och. Varje termostat-komponent har sitt eget `getMaxMinReport` kommando.
* Skapar egenskaps lyssnare, för att lyssna efter skrivbara egenskaps uppdateringar.
* Startar en slinga för att skicka temperatur telemetri från de två termostat-komponenterna och telemetri för arbets uppsättningar från standard komponenten var 8: e sekund.

```python
async def main():
    switch = os.getenv("IOTHUB_DEVICE_SECURITY_TYPE")
    if switch == "DPS":
        provisioning_host = (
            os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            if os.getenv("IOTHUB_DEVICE_DPS_ENDPOINT")
            else "global.azure-devices-provisioning.net"
        )
        id_scope = os.getenv("IOTHUB_DEVICE_DPS_ID_SCOPE")
        registration_id = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_ID")
        symmetric_key = os.getenv("IOTHUB_DEVICE_DPS_DEVICE_KEY")

        registration_result = await provision_device(
            provisioning_host, id_scope, registration_id, symmetric_key, model_id
        )

        if registration_result.status == "assigned":
            print("Device was assigned")
            print(registration_result.registration_state.assigned_hub)
            print(registration_result.registration_state.device_id)
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
                product_info=model_id,
            )
        else:
            raise RuntimeError(
                "Could not provision device. Aborting Plug and Play device connection."
            )

    elif switch == "connectionString":
        # ...

    # Connect the client.
    await device_client.connect()

    ################################################
    # Update readable properties from various components

    properties_root = pnp_helper.create_reported_properties(serialNumber=serial_number)
    properties_thermostat1 = pnp_helper.create_reported_properties(
        thermostat_1_component_name, maxTempSinceLastReboot=98.34
    )
    properties_thermostat2 = pnp_helper.create_reported_properties(
        thermostat_2_component_name, maxTempSinceLastReboot=48.92
    )
    properties_device_info = pnp_helper.create_reported_properties(
        device_information_component_name,
        swVersion="5.5",
        manufacturer="Contoso Device Corporation",
        model="Contoso 4762B-turbo",
        osName="Mac Os",
        processorArchitecture="x86-64",
        processorManufacturer="Intel",
        totalStorage=1024,
        totalMemory=32,
    )

    property_updates = asyncio.gather(
        device_client.patch_twin_reported_properties(properties_root),
        device_client.patch_twin_reported_properties(properties_thermostat1),
        device_client.patch_twin_reported_properties(properties_thermostat2),
        device_client.patch_twin_reported_properties(properties_device_info),
    )

    ################################################
    # Get all the listeners running
    print("Listening for command requests and property updates")

    global THERMOSTAT_1
    global THERMOSTAT_2
    THERMOSTAT_1 = Thermostat(thermostat_1_component_name, 10)
    THERMOSTAT_2 = Thermostat(thermostat_2_component_name, 10)

    listeners = asyncio.gather(
        execute_command_listener(
            device_client, method_name="reboot", user_command_handler=reboot_handler
        ),
        execute_command_listener(
            device_client,
            thermostat_1_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_command_listener(
            device_client,
            thermostat_2_component_name,
            method_name="getMaxMinReport",
            user_command_handler=max_min_handler,
            create_user_response_handler=create_max_min_report_response,
        ),
        execute_property_listener(device_client),
    )

    ################################################
    # Function to send telemetry every 8 seconds

    async def send_telemetry():
        print("Sending telemetry from various components")

        while True:
            curr_temp_ext = random.randrange(10, 50)
            THERMOSTAT_1.record(curr_temp_ext)

            temperature_msg1 = {"temperature": curr_temp_ext}
            await send_telemetry_from_temp_controller(
                device_client, temperature_msg1, thermostat_1_component_name
            )

            curr_temp_int = random.randrange(10, 50)  # Current temperature in Celsius
            THERMOSTAT_2.record(curr_temp_int)

            temperature_msg2 = {"temperature": curr_temp_int}

            await send_telemetry_from_temp_controller(
                device_client, temperature_msg2, thermostat_2_component_name
            )

            workingset_msg3 = {"workingSet": random.randrange(1, 100)}
            await send_telemetry_from_temp_controller(device_client, workingset_msg3)

    send_telemetry_task = asyncio.ensure_future(send_telemetry())

    # ...
```

`provision_device`Funktionen använder DPS för att etablera enheten och registrera den med IoT Central. Funktionen inkluderar enhets modell-ID: t, som IoT Central använder för att [associera en enhet med en enhets mall](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template), i etablerings nytto lasten:

```python
async def provision_device(provisioning_host, id_scope, registration_id, symmetric_key, model_id):
    provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
        provisioning_host=provisioning_host,
        registration_id=registration_id,
        id_scope=id_scope,
        symmetric_key=symmetric_key,
    )

    provisioning_device_client.provisioning_payload = {"modelId": model_id}
    return await provisioning_device_client.register()
```

`execute_command_listener`Funktionen hanterar kommando begär Anden, kör `max_min_handler` funktionen när enheten tar emot `getMaxMinReport` kommandot för termostat-komponenterna och `reboot_handler` funktionen när enheten tar emot `reboot` kommandot. Den använder `pnp_helper` modulen för att bygga svaret:

```python
async def execute_command_listener(
    device_client,
    component_name=None,
    method_name=None,
    user_command_handler=None,
    create_user_response_handler=None,
):
    while True:
        if component_name and method_name:
            command_name = component_name + "*" + method_name
        elif method_name:
            command_name = method_name
        else:
            command_name = None

        command_request = await device_client.receive_method_request(command_name)
        print("Command request received with payload")
        values = command_request.payload
        print(values)

        if user_command_handler:
            await user_command_handler(values)
        else:
            print("No handler provided to execute")

        (response_status, response_payload) = pnp_helper.create_response_payload_with_status(
            command_request, method_name, create_user_response=create_user_response_handler
        )

        command_response = MethodResponse.create_from_method_request(
            command_request, response_status, response_payload
        )

        try:
            await device_client.send_method_response(command_response)
        except Exception:
            print("responding to the {command} command failed".format(command=method_name))
```

`async def execute_property_listener`Hanterar uppdateringar av skrivbara egenskaper, till exempel `targetTemperature` för termostat-komponenter och genererar JSON-svaret. Den använder `pnp_helper` modulen för att bygga svaret:

```python
async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        print(patch)
        properties_dict = pnp_helper.create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

`send_telemetry_from_temp_controller`Funktionen skickar telemetri-meddelandena från termostat-komponenterna till IoT Central. Den använder `pnp_helper` modulen för att bygga meddelandena:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = pnp_helper.create_telemetry(telemetry_msg, component_name)
    await device_client.send_message(msg)
    print("Sent message")
    print(msg)
    await asyncio.sleep(5)
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Kör koden

Kör exempel programmet genom att öppna en kommando rads miljö och navigera till mappen *Azure-IoT-SDK-python/Azure-IoT-Device/samples/PnP* som innehåller exempel filen *temp_controller_with_thermostats. py* .

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Installera de nödvändiga paketen:

```cmd/sh
pip install azure-iot-device
```

Kör exemplet:

```cmd/sh
python temp_controller_with_thermostats.py
```

Följande utdata visar enhets registrering och anslutning till IoT Central. Exemplet skickar `maxTempSinceLastReboot` egenskaperna från de två termostat-komponenterna innan den börjar skicka telemetri:

```cmd/sh
Device was assigned
iotc-60a.....azure-devices.net
sample-device-01
Updating pnp properties for root interface
{'serialNumber': 'alohomora'}
Updating pnp properties for thermostat1
{'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
Updating pnp properties for thermostat2
{'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
Updating pnp properties for deviceInformation
{'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
Listening for command requests and property updates
Press Q to quit
Sending telemetry from various components
Sent message
{"temperature": 27}
Sent message
{"temperature": 17}
Sent message
{"workingSet": 13}
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Du kan se hur enheten svarar på kommandon och egenskaps uppdateringar:

```cmd/sh
{'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
the data in the desired properties patch was: {'thermostat1': {'targetTemperature': 67, '__t': 'c'}, '$version': 2}
Values received are :-
{'targetTemperature': 67, '__t': 'c'}
Sent message

...

Command request received with payload
2021-03-31T05:00:00.000Z
Will return the max, min and average temperature from the specified time 2021-03-31T05:00:00.000Z to the current time
Done generating
{"avgTemp": 4.0, "endTime": "2021-03-31T12:29:48.322427", "maxTemp": 18, "minTemp": null, "startTime": "2021-03-31T12:28:28.322381"}
```
