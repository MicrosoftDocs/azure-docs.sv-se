---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510799"
---
Följande resurser är också tillgängliga:

- [Referensdokumentation för Python SDK](/python/api/azure-iot-hub/azure.iot.hub)
- [Tjänst klient exempel](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Digitala dubbla exempel](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Exempel på IoT Hub tjänst klient

I det här avsnittet visas python-exempel med hjälp av IoT Hub tjänst klienten och **IoTHubRegistryManager** -och **CloudToDeviceMethod** -klasserna. Du kan använda **IoTHubRegistryManager** -klassen för att interagera med enhetens tillstånd med hjälp av enhets dubbla. Du kan också använda klassen **IoTHubRegistryManager** för att [fråga efter enhets registreringar](../articles/iot-hub/iot-hub-devguide-query-language.md) i din IoT Hub. Du använder klassen **CloudToDeviceMethod** för att anropa kommandon på enheten. [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar. I kodfragmenten `device_id` innehåller variabeln enhets-ID för iot plug and Play-enheten som registrerats med IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Hämta enhetens dubbla och modell-ID

För att hämta enhetens dubbla och modell-ID för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Uppdatera enhet, dubbla

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Exemplet visar hur du måste hämta den dubbla appen `etag` innan du uppdaterar den. Egenskapen definieras i standard komponenten för enheten:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Exemplet visar hur du måste hämta den dubbla appen `ETag` innan du uppdaterar den. Egenskapen definieras i **thermostat1** -komponenten:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

För en egenskap i en komponent ser egenskaps korrigeringen ut som i följande exempel:

```json
{
"thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **thermostat1** -komponenten:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitala, dubbla exempel

Du använder klassen **DigitalTwinClient** för att interagera med enhetens tillstånd med hjälp av digitala dubbla. [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar.

`device_id`Variabeln innehåller enhets-ID: t för iot plug and Play-enheten registrerad i IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Hämta det digitala dubbla och modell-ID: t

För att hämta det digitala dubbla och modell-ID: t för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Uppdatera Digital, delad

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Egenskapen definieras i standard komponenten för enheten:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Egenskapen definieras i **thermostat1** -komponenten:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **thermostat1** -komponenten:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Läsa telemetri för enhet

IoT Plug and Play-enheter skickar telemetri som definierats i DTDL-modellen till IoT Hub. Som standard dirigerar IoT Hub telemetri till en Event Hubs-slutpunkt där du kan använda den. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Följande kodfragment visar hur du läser Telemetrin från standard slut punkten för Event Hubs. Koden i det här kodfragmentet hämtas från IoT Hub snabb start [Skicka telemetri från en enhet till en IoT-hubb och läsa den med ett Server dels program](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

Följande utdata från föregående kod visar den temperatur telemetri som skickas av IoT Plug and Play-enheten (No-Component **termostat** IoT) som bara har standard komponenten. `dt-dataschema`Egenskapen system visar modell-ID:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

Följande utdata från föregående kod visar de temperatur-telemetri som skickas av **TemperatureController** IoT plug and Play-enheten med flera komponenter. `dt-subject`Egenskapen system visar namnet på komponenten som skickade telemetri. I det här exemplet är de två komponenterna `thermostat1` och `thermostat2` enligt definitionen i DTDL-modellen. `dt-dataschema`Egenskapen system visar modell-ID:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Läs enhets meddelanden med dubbla ändringar

Du kan konfigurera IoT Hub att generera enhets dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående python-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

Koden som visas i föregående python-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Läs digitala dubbla ändrings meddelanden

Du kan konfigurera IoT Hub att generera digitala dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående python-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

Koden som visas i föregående python-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
