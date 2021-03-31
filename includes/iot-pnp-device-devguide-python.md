---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f4eb312aff200389f59a3e342305b8eda98f213e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104582823"
---
## <a name="model-id-announcement"></a>Meddelande om modell-ID

För att meddela modell-ID måste enheten inkludera den i anslutnings informationen:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> För moduler och IoT Edge använder du `IoTHubModuleClient` i stället för `IoTHubDeviceClient` .

> [!TIP]
> Det här är den enda tid som en enhet kan ställa in modell-ID: t kan inte uppdateras efter att enheten har anslutits.

## <a name="dps-payload"></a>DPS-nyttolast

Enheter som använder [enhets etablerings tjänsten (DPS)](../articles/iot-dps/about-iot-dps.md) kan inkludera den `modelId` som ska användas under etablerings processen med hjälp av följande JSON-nyttolast.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementera telemetri, egenskaper och kommandon

Som beskrivs i [förstå komponenter i IoT plug and Play-modeller](../articles/iot-pnp/concepts-modeling-guide.md)måste enhets byggare bestämma om de vill använda komponenter för att beskriva sina enheter. När du använder komponenter måste enheterna följa reglerna som beskrivs i det här avsnittet.

### <a name="telemetry"></a>Telemetri

En standard komponent kräver ingen särskild egenskap.

När du använder kapslade komponenter måste enheterna ange en meddelande egenskap med namnet på komponenten:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Skrivskyddade egenskaper

Rapportering av en egenskap från standard komponenten kräver ingen särskild konstruktion:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

När du använder kapslade komponenter måste du skapa egenskaper i komponent namnet:

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Skrivbara egenskaper

Dessa egenskaper kan ställas in av enheten eller uppdateras av lösningen. Om lösningen uppdaterar en egenskap får klienten ett meddelande som ett återanrop i `IoTHubDeviceClient` eller `IoTHubModuleClient` . För att följa IoT Plug and Play-konventionerna måste enheten meddela tjänsten att egenskapen har tagits emot.

#### <a name="report-a-writable-property"></a>Rapportera en skrivbar egenskap

När en enhet rapporterar en skrivbar egenskap måste den innehålla de `ack` värden som definieras i konventionerna.

Så här rapporterar du en skrivbar egenskap från standard komponenten:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Om du vill rapportera en skrivbar egenskap från en kapslad komponent måste den dubbla innehålla en markör:

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Prenumerera på önskade egenskaps uppdateringar

Tjänster kan uppdatera önskade egenskaper som utlöser ett meddelande på de anslutna enheterna. Det här meddelandet innehåller uppdaterade önskade egenskaper, inklusive versions numret som identifierar uppdateringen. Enheter måste svara med samma `ack` meddelande som rapporterade egenskaper.

En standard komponent ser den enskilda egenskapen och skapar rapporterad `ack` med den mottagna versionen:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

Enheten, som visar egenskapen i de önskade och rapporterade avsnitten:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

En kapslad komponent tar emot önskade egenskaper omslutna med komponent namnet och bör rapportera tillbaka den `ack` rapporterade egenskapen:

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

Enhetens dubbla för komponenter visar de önskade och rapporterade avsnitten enligt följande:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Kommandon

En standard komponent får kommando namnet som det anropades av tjänsten.

En kapslad komponent får det kommando namn som föregås av komponent namnet och `*` avgränsaren.

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

#### <a name="request-and-response-payloads"></a>Nytto laster för begäran och svar

Kommandon använder typer för att definiera sina nytto laster för begäran och svar. En enhet måste deserialisera inkommande indataparameter och serialisera svaret. I följande exempel visas hur du implementerar ett kommando med komplexa typer som definieras i nytto lasterna:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Följande kodfragment visar hur en enhet implementerar den här kommando definitionen, inklusive de typer som används för att aktivera serialisering och deserialisering:

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> Namnen på begäran och svaren finns inte i de serialiserade nytto lasterna som överförs via kabeln.
