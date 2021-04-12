---
title: Skicka Defender för IoT-enhet, säkerhets meddelanden
description: Lär dig hur du skickar säkerhets meddelanden med hjälp av Defender för IoT.
ms.topic: how-to
ms.date: 2/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: dcda7f4c1d078fa32a5ab8c7b7ce690ab2134afe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781644"
---
# <a name="send-security-messages-sdk"></a>Skicka Security-meddelanden SDK

I den här instruktions guiden förklaras funktionerna för Defender för IoT-tjänster när du väljer att samla in och skicka enhets säkerhets meddelanden utan att använda en Defender för IoT-agenten och förklarar hur du gör det.

I den här guiden får du lära du dig att:

> [!div class="checklist"]
> * Skicka säkerhets meddelanden med Azure IoT C SDK
> * Skicka säkerhets meddelanden med Azure IoT C# SDK
> * Skicka säkerhets meddelanden med Azure IoT python SDK
> * Skicka säkerhets meddelanden med Azure IoT Node.js SDK
> * Skicka säkerhets meddelanden med Azure IoT Java SDK

## <a name="defender-for-iot-capabilities"></a>Defender för IoT-funktioner

Defender för IoT kan bearbeta och analysera alla typer av säkerhets meddelande data så länge data som skickas överensstämmer med [Defender för IoT-schemat](https://aka.ms/iot-security-schemas) och meddelandet anges som ett säkerhets meddelande.

## <a name="security-message"></a>Säkerhets meddelande

Defender för IoT definierar ett säkerhets meddelande med följande kriterier:

- Om meddelandet skickades med Azure IoT SDK
- Om meddelandet överensstämmer med [säkerhets meddelandets schema](https://aka.ms/iot-security-schemas)
- Om meddelandet angavs som ett säkerhets meddelande innan det skickas

Varje säkerhets meddelande innehåller metadata för avsändaren, till `AgentId` exempel `AgentVersion` , `MessageSchemaVersion` och en lista över säkerhets händelser.
Schemat definierar de giltiga och nödvändiga egenskaperna för säkerhets meddelandet, inklusive typer av händelser.

> [!NOTE]
> Meddelanden som skickas som inte följer schemat ignoreras. Se till att verifiera schemat innan du påbörjar sändning av data eftersom ignorerade meddelanden inte lagras i nuläget.

> [!NOTE]
> Meddelanden som skickas som inte angavs som ett säkerhets meddelande med hjälp av Azure IoT SDK kommer inte att dirigeras till-pipeline för Defender för IoT.

## <a name="valid-message-example"></a>Exempel på giltigt meddelande

Exemplet nedan visar ett giltigt säkerhets meddelande objekt. Exemplet innehåller meddelandets metadata och en `ProcessCreate` säkerhets händelse.

När det har angetts som ett säkerhets meddelande och skickas, kommer det här meddelandet att bearbetas av Defender för IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Skicka säkerhets meddelanden

Skicka säkerhets meddelanden *utan att* använda Defender för IoT-agenten med hjälp av [Azure IoT C-ENHETs-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview): n, [Azure IoT C#-enhets-SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT python SDK](https://github.com/Azure/azure-iot-sdk-python)eller [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Om du vill skicka enhets data från dina enheter för bearbetning av Defender för IoT, använder du någon av följande API: er för att markera meddelanden för korrekt routning till Defender för IoT-bearbetning av pipelinen.

Alla data som skickas, även om de har marker ATS med rätt sidhuvud, måste också följa [schema för Defender för IoT-meddelanden](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Skicka API för säkerhets meddelande

API för att **Skicka Security-meddelanden** är för närvarande tillgängligt i C och C#, Python, Node.js och Java.

#### <a name="c-api"></a>C-API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C#-API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js-API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

Om du vill använda python-API: et måste du installera paketet [Azure-IoT-Device](https://pypi.org/project/azure-iot-device/).

När du använder python-API: et kan du antingen skicka säkerhets meddelandet via modulen eller via enheten med hjälp av anslutnings strängen för den unika enheten eller modulen. När du använder följande Python-skript exempel med en enhet, använder du **IoTHubDeviceClient** och använder en modul, använder du **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java-API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Defender for IoT-tjänsten
- Läs mer om Defender för IoT- [arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [aviseringar](concept-security-alerts.md)
