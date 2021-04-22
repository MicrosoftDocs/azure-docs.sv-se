---
title: Förstå Azure IoT Hub MQTT-| Microsoft Docs
description: Stöd för enheter som ansluter till en IoT Hub enhetsriktad slutpunkt med hjälp av MQTT-protokollet. Innehåller information om inbyggt MQTT-stöd i Azure IoT-enhets-SDK:er.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: 077b4ea6c7d4506e29673cbb4d6d89a92657b9c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873693"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunicera med din IoT-hubb med hjälp av MQTT-protokollet

IoT Hub enheter kan kommunicera med enhetens IoT Hub med hjälp av:

* [MQTT v3.1.1](https://mqtt.org/) på port 8883
* MQTT v3.1.1 via WebSocket på port 443.

IoT Hub är inte en fullständig MQTT-koordinator och stöder inte alla beteenden som anges i MQTT v3.1.1-standarden. Den här artikeln beskriver hur enheter kan använda MQTT-beteenden som stöds för att kommunicera med IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

All enhetskommunikation med IoT Hub måste skyddas med TLS/SSL. Därför IoT Hub inte icke-säkra anslutningar via port 1883.

## <a name="connecting-to-iot-hub"></a>Ansluta till IoT Hub

En enhet kan använda MQTT-protokollet för att ansluta till en IoT-hubb med något av följande alternativ.

* Bibliotek i [Azure IoT-SDK:erna](https://github.com/Azure/azure-iot-sdks).
* MQTT-protokollet direkt.

MQTT-porten (8883) är blockerad i många företags- och utbildningsnätverksmiljöer. Om du inte kan öppna port 8883 i brandväggen rekommenderar vi att du använder MQTT över Web Sockets. MQTT via Web Sockets kommunicerar via port 443, som nästan alltid är öppen i nätverksmiljöer. Information om hur du anger MQTT och MQTT över Web Sockets-protokoll när du använder Azure IoT-SDK:er finns i Använda [enhets-SDK:erna](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Använda enhets-SDK:er

[Enhets-SDK:er](https://github.com/Azure/azure-iot-sdks) som stöder MQTT-protokollet är tillgängliga för Java, Node.js, C, C# och Python. Enhets-SDK:erna använder standardanslutningssträngen IoT Hub för att upprätta en anslutning till en IoT-hubb. Om du vill använda MQTT-protokollet måste klientprotokollparametern anges till **MQTT**. Du kan också ange MQTT över Web Sockets i parametern för klientprotokoll. Som standard ansluter enhets-SDK:er till en IoT Hub med flaggan CleanSession inställd på **0** och använder **QoS 1** för meddelandeutbyte med IoT-hubben.  Även om det är möjligt att **konfigurera QoS 0** för snabbare meddelandeutbyte bör du observera att leveransen inte är garanterad eller bekräftad. Därför kallas **QoS 0** ofta för "fire and forget".

När en enhet är ansluten till en IoT-hubb tillhandahåller enhets-SDK:erna metoder som gör det möjligt för enheten att utbyta meddelanden med en IoT-hubb.

Följande tabell innehåller länkar till kodexempel för varje språk som stöds och anger den parameter som ska användas för att upprätta en anslutning till IoT Hub med hjälp av MQTT eller MQTT över Web Sockets-protokollet.

| Språk | MQTT-protokollparameter | MQTT över protokollparametern Web Sockets
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). Mqtt | TransportType.Mqtt faller tillbaka till MQTT över Web Sockets om MQTT misslyckas. Om du bara vill ange MQTT över webbsocketar använder du TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Stöder MQTT som standard | Lägg `websockets=True` till i anropet för att skapa klienten |

Följande fragment visar hur du anger MQTT över Web Sockets-protokollet när du använder Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Följande fragment visar hur du anger MQTT över Web Sockets-protokollet när du använder Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Standardtids tidsgräns för keep-alive

För att säkerställa att en klient-/IoT Hub-anslutning förblir levande skickar både tjänsten och klienten regelbundet en *keep-alive-ping* till varandra. Klienten som använder IoT SDK skickar ett keep-alive-meddelande vid det intervall som definieras i den här tabellen nedan:

|Språk  |Standardintervall för keep-alive  |Konfigurerbar  |
|---------|---------|---------|
|Node.js     |   180 sekunder      |     No    |
|Java     |    230 sekunder     |     No    |
|C     | 240 sekunder |  [Ja](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekunder |  [Ja](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 sekunder |  No   |

Om du [följer MQTT-IoT Hub](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)är pingintervallet keep-alive 1,5 gånger värdet för keep-alive för klienten. Men IoT Hub begränsar den maximala tidsgränsen på serversidan till 29,45 minuter (1 767 sekunder) eftersom alla Azure-tjänster är bundna till tidsgränsen för TCP-inaktivitet i Azure-lastbalanseraren, som är 29,45 minuter. 

En enhet som använder Java SDK skickar till exempel keep-alive-ping och förlorar sedan nätverksanslutningen. 230 sekunder senare missar enheten keep-alive-ping eftersom den är offline. Men IoT Hub inte anslutningen omedelbart – den väntar ytterligare några sekunder innan enheten kopplas från med felet `(230 * 1.5) - 230 = 115` [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

Det maximala värdet för klient-keep-alive som du kan ange är `1767 / 1.5 = 1177` sekunder. All trafik återställer keep-alive. Till exempel återställer en lyckad uppdatering av SAS-token keep-alive.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrera en enhetsapp från AMQP till MQTT

Om du använder [enhets-ID:erna](https://github.com/Azure/azure-iot-sdks)måste du ändra protokollparametern i klient initieringen för att växla från att använda AMQP till MQTT, enligt tidigare.

När du gör det bör du kontrollera följande:

* AMQP returnerar fel för många villkor, medan MQTT avslutar anslutningen. Därför kan logiken för undantagshantering kräva vissa ändringar.

* MQTT stöder inte *avslagsåtgärder när* meddelanden från molnet till enheten tas [emot.](iot-hub-devguide-messaging.md) Om din backend-app behöver ta emot ett svar från enhetsappen kan du överväga att använda [direktmetoder](iot-hub-devguide-direct-methods.md).

* AMQP stöds inte i Python SDK.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Exempel i C med MQTT utan Azure IoT SDK

I [IoT MQTT-exempellagringsplatsen](https://github.com/Azure-Samples/IoTMQTTSample)finns ett par C/C++-demoprojekt som visar hur du skickar telemetrimeddelanden och tar emot händelser med en IoT-hubb utan att använda Azure IoT C SDK. 

De här exemplen använder Eclipse Mosquitto-biblioteket för att skicka meddelanden till MQTT Broker som implementerats i IoT-hubben.

Information om hur du anpassar exemplen för att använda [Azure IoT Plug and Play-konventioner](../iot-pnp/overview-iot-plug-and-play.md) finns i Självstudie – Använda MQTT för att utveckla [en IoT Plug and Play enhetsklient](../iot-pnp/tutorial-use-mqtt.md).

Den här lagringsplatsen innehåller:

**För Windows:**

* TelemetryMQTTWin32: innehåller kod för att skicka ett telemetrimeddelande till en Azure IoT-hubb som skapats och körts på en Windows-dator.

* SubscribeMQTTWin32: innehåller kod för att prenumerera på händelser för en viss IoT-hubb på en Windows-dator.

* DeviceTwinMQTTWin32: innehåller kod för att fråga och prenumerera på enhetstvillinghändelser för en enhet i Azure IoT Hub på en Windows-dator.

* PnPMQTTWin32: innehåller kod för att skicka ett telemetrimeddelande med IoT Plug and Play-enhetsfunktioner till en Azure IoT Hub som skapats och körts på en Windows-dator. Du kan läsa mer om [IoT-Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)

**För Linux:**

* MQTTLinux: innehåller kod och byggskript som ska köras på Linux (WSL, Ubuntu och Raspbian har testats hittills).

* LinuxConsoleVS2019: innehåller samma kod men i ett VS2019-projekt som riktar in sig på WSL (Windows Linux-undersystem). Med det här projektet kan du felsöka koden som körs på Linux steg för steg från Visual Studio.

**För mosquitto_pub:**

Den här mappen innehåller två exempelkommandon som används med mosquitto_pub verktygsverktyget som tillhandahålls av Mosquitto.org.

* Mosquitto_sendmessage: för att skicka ett enkelt sms till en Azure IoT-hubb som fungerar som en enhet.

* Mosquitto_subscribe: för att se händelser som inträffar i en Azure IoT Hub.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Använda MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda enhets-SDK:er kan den fortfarande ansluta till de offentliga enhetsslutpunkterna med hjälp av MQTT-protokollet på port 8883. I **CONNECT-paketet** ska enheten använda följande värden:

* I fältet **ClientId** använder du **deviceId**.

* I fältet **Användarnamn** använder du `{iothubhostname}/{device_id}/?api-version=2018-06-30` , där är det fullständiga `{iothubhostname}` CName för IoT-hubben.

    Om namnet på din IoT-hubb till exempel **är contoso.azure-devices.net** och om namnet på enheten är **MyDevice01** ska det fullständiga **fältet** Användarnamn innehålla:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Vi rekommenderar starkt att du inkluderar API-version i fältet . Annars kan det orsaka oväntade beteenden. 
    
* I fältet **Lösenord** använder du en SAS-token. Formatet för SAS-token är samma som för både HTTPS- och AMQP-protokollen:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Om du använder X.509-certifikatautentisering krävs inga SAS-tokenlösenord. Mer information finns i [Konfigurera X.509-säkerhet i din Azure IoT Hub](iot-hub-security-x509-get-started.md) och följ anvisningarna i [TLS/SSL-konfigurationsavsnittet](#tlsssl-configuration).

  Mer information om hur du genererar SAS-token finns i enhetsavsnittet [i Använda IoT Hub säkerhetstoken.](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

  När du testar kan du också använda plattformsoberoende Azure IoT Tools för [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller CLI-tilläggskommandot [az iot hub generate-sas-token för](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) att snabbt generera en SAS-token som du kan kopiera och klistra in i din egen kod.

### <a name="for-azure-iot-tools"></a>För Azure IoT Tools

1. Expandera fliken **AZURE IOT HUB DEVICES (AZURE IOT** HUB-ENHETER) i det nedre vänstra hörnet Visual Studio Code.
  
2. Högerklicka på enheten och välj Generera **SAS-token för enhet.**
  
3. Ange **förfallotid** och tryck på Retur.
  
4. SAS-token skapas och kopieras till Urklipp.

   SAS-token som genereras har följande struktur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Den del av denna token som ska användas som **lösenordsfält** för att ansluta med MQTT är:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

För MQTT-anslutning och frånkoppling av IoT Hub en händelse i **operationsövervakningskanalen.** Den här händelsen innehåller ytterligare information som kan hjälpa dig att felsöka anslutningsproblem.

Enhetsappen kan ange ett **Will-meddelande** i **CONNECT-paketet.** Enhetsappen ska använda `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som Will-ämnesnamn för att definiera **Will-meddelanden** som ska vidarebefordras som ett telemetrimeddelande.  I det här fallet, om nätverksanslutningen  är stängd, men ett FRÅNKOPPLINGspaket inte tidigare togs emot från enheten, skickar IoT Hub **Will-meddelandet** som angavs i **CONNECT-paketet** till telemetrikanalen. Telemetrikanalen kan antingen vara **standardslutpunkten händelser eller** en anpassad slutpunkt som definieras av IoT Hub routning. Meddelandet har egenskapen **iothub-MessageType** med värdet **Will tilldelad** till den.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Använda MQTT-protokollet direkt (som en modul)

Att ansluta till IoT Hub via MQTT med hjälp av en modulidentitet liknar enheten (beskrivs i avsnittet om att använda [MQTT-protokollet](#using-the-mqtt-protocol-directly-as-a-device)direkt som en enhet), men du måste använda följande:

* Ange klient-ID:t till `{device_id}/{module_id}` .

* Om du autentiserar med användarnamn och lösenord anger du användarnamnet till och använder `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` den SAS-token som är associerad med modulidentiteten som lösenord.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som ämne för att publicera telemetri.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som WILL-ämne.

* Tvillingämnena GET och PATCH är identiska för moduler och enheter.

* Tvillingstatusavsnittet är identiskt för moduler och enheter.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Om du vill använda MQTT-protokollet direkt måste *klienten ansluta* via TLS/SSL. Försöker hoppa över det här steget misslyckas med anslutningsfel.

För att upprätta en TLS-anslutning kan du behöva ladda ned och referera till DigiCert Baltimore-rotcertifikatet. Det här certifikatet är det certifikat som Azure använder för att skydda anslutningen. Du hittar det här certifikatet på [lagringsplatsen Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Mer information om dessa certifikat finns på [Digicerts webbplats.](https://www.digicert.com/digicert-root-certificates.htm)

Ett exempel på hur du implementerar detta med Python-versionen av [Paho MQTT-biblioteket](https://pypi.python.org/pypi/paho-mqtt) från Eclipse Foundation kan se ut så här.

Installera först Paho-biblioteket från kommandoradsmiljön:

```cmd/sh
pip install paho-mqtt
```

Implementera sedan klienten i ett Python-skript. Ersätt platshållarna på följande sätt:

* `<local path to digicert.cer>` är sökvägen till en lokal fil som innehåller DigiCert Baltimore-rotcertifikatet. Du kan skapa den här filen genom att kopiera certifikatinformationen från [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) i Azure IoT SDK för C. Inkludera raderna och , ta bort markeringarna i början och slutet av varje rad och ta bort tecknen i slutet av `-----BEGIN CERTIFICATE-----` `-----END CERTIFICATE-----` varje `"` `\r\n` rad.

* `<device id from device registry>` är ID:t för en enhet som du har lagt till i din IoT-hubb.

* `<generated SAS token>` är en SAS-token för enheten som skapades enligt beskrivningen tidigare i den här artikeln.

* `<iot hub name>` namnet på din IoT-hubb.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Om du vill autentisera med ett enhetscertifikat uppdaterar du kodfragmentet ovan med följande ändringar (se Hämta ett [X.509 CA-certifikat](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) om hur du förbereder för certifikatbaserad autentisering):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Skicka meddelanden från enheten till molnet

När anslutningen har lyckats kan en enhet skicka meddelanden till IoT Hub med hjälp `devices/{device_id}/messages/events/` av eller som ett `devices/{device_id}/messages/events/{property_bag}` **ämnesnamn**. Elementet `{property_bag}` gör att enheten kan skicka meddelanden med ytterligare egenskaper i ett URL-kodat format. Exempel:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Det `{property_bag}` här elementet använder samma kodning som frågesträngar i HTTPS-protokollet.

Följande är en lista över IoT Hub för implementeringsspecifika beteenden:

* IoT Hub stöder inte QoS 2-meddelanden. Om en enhetsapp publicerar ett meddelande **med QoS 2** IoT Hub stänger nätverksanslutningen.

* IoT Hub bevarar inte Behåll meddelanden. Om en enhet skickar ett meddelande med **flaggan RETAIN** inställd på 1 lägger IoT Hub till **programegenskapen mqtt-retain** i meddelandet. I det här fallet skickar IoT Hub det till backend-appen i stället för att behålla meddelandet.

* IoT Hub stöder endast en aktiv MQTT-anslutning per enhet. En ny MQTT-anslutning för samma enhets-ID gör att IoT Hub tar bort den befintliga anslutningen och **400027 ConnectionForcefullyClosedOnNewConnection** loggas in i IoT Hub Logs


Mer information finns i [utvecklarhandboken för meddelanden.](iot-hub-devguide-messaging.md)

## <a name="receiving-cloud-to-device-messages"></a>Ta emot meddelanden från moln till enhet

För att ta emot meddelanden IoT Hub bör en enhet prenumerera med hjälp `devices/{device_id}/messages/devicebound/#` av som ett **ämnesfilter.** Jokertecknet på flera nivåer `#` i ämnesfiltret används bara för att tillåta att enheten får ytterligare egenskaper i ämnesnamnet. IoT Hub tillåter inte användning av jokertecken `#` eller för filtrering av `?` underämnen. Eftersom IoT Hub inte är en allmän koordinatortjänst för pub-sub-meddelanden har den endast stöd för dokumenterade ämnesnamn och ämnesfilter.

Enheten får inga meddelanden från IoT Hub förrän den har prenumererat på sin enhetsspecifika slutpunkt, som representeras av `devices/{device_id}/messages/devicebound/#` ämnesfiltret. När en prenumeration har upprättats tar enheten emot moln-till-enhet-meddelanden som skickades till den efter prenumerationens tid. Om enheten ansluter med **Flaggan CleanSession** inställd på **0** bevaras prenumerationen mellan olika sessioner. I det här fallet får enheten eventuella utestående meddelanden som skickas till den när den kopplas från nästa gång den ansluter till **CleanSession 0.** Om enheten använder **Flaggan CleanSession** inställd på **1** tar den dock inte emot några meddelanden från IoT Hub förrän den prenumererar på sin enhetsslutpunkt.

IoT Hub levererar meddelanden med **ämnesnamnet** `devices/{device_id}/messages/devicebound/` , eller när det finns `devices/{device_id}/messages/devicebound/{property_bag}` meddelandeegenskaper. `{property_bag}` innehåller url-kodade nyckel-/värdepar för meddelandeegenskaper. Endast programegenskaper och användar setable systemegenskaper (till exempel **messageId** eller **correlationId**) ingår i egenskapsdecket. Systemegenskapsnamn har **$** prefixet , programegenskaper använder det ursprungliga egenskapsnamnet utan prefix. Mer information om formatet för egenskapsdegensen finns i [Skicka meddelanden från enheten till molnet.](#sending-device-to-cloud-messages)

I meddelanden från moln till enhet representeras värden i egenskapsdegenskapsen som i följande tabell:

| Egenskapsvärde | Representation | Description |
|----|----|----|
| `null` | `key` | Endast nyckeln visas i egenskapsdegenskapsen |
| tom sträng | `key=` | Nyckeln följt av ett likhetstecken utan värde |
| icke-null, icke-tomt värde | `key=value` | Nyckeln följt av ett likhetstecken och värdet |

I följande exempel visas en egenskapsdesk som innehåller tre programegenskaper: **prop1** med värdet `null` ; **prop2**, en tom sträng (""); och **prop3** med värdet "en sträng".

```mqtt
/?prop1&prop2=&prop3=a%20string
```

När en enhetsapp prenumererar på ett ämne med **QoS 2** IoT Hub högsta QoS-nivå 1 i **SUBACK-paketet.** Därefter skickar IoT Hub till enheten med hjälp av QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Hämta egenskaper för en enhetstvilling

Först prenumererar en enhet på `$iothub/twin/res/#` för att ta emot åtgärdens svar. Sedan skickar den ett tomt meddelande till ämnet `$iothub/twin/GET/?$rid={request id}` , med ett ifylld värde för id **för begäran.** Tjänsten skickar sedan ett svarsmeddelande som innehåller enhetstvillingens data om ämnet , med `$iothub/twin/res/{status}/?$rid={request id}` samma **begärande-ID** som begäran.

Id för begäran kan vara ett giltigt värde för ett värde för en meddelandeegenskap, enligt [IoT Hub utvecklarhandbok](iot-hub-devguide-messaging.md)för meddelanden, och statusen verifieras som ett heltal.

Svarstexten innehåller egenskapsavsnittet för enhetstvillingen, som du ser i följande svarsexempel:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Klart |
| 429 | För många begäranden (begränsade) [enligt IoT Hub begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Mer information finns i [utvecklarhandboken för enhetstvillingarna.](iot-hub-devguide-device-twins.md)

## <a name="update-device-twins-reported-properties"></a>Uppdatera enhetstvillingens rapporterade egenskaper

För att uppdatera rapporterade egenskaper utfärdar enheten en begäran om att IoT Hub via en publikation över ett anvisat MQTT-ämne. När du har bearbetat IoT Hub statusen för uppdateringen via en publikation till ett annat ämne. Det här avsnittet kan prenumereras av enheten för att meddela den om resultatet av sin tvillinguppdateringsbegäran. För att implementera den här typen av interaktion mellan begäran och svar i MQTT använder vi begreppet begärande-ID ( ) som tillhandahålls från början av enheten `$rid` i dess uppdateringsbegäran. Detta begärande-ID ingår också i svaret från IoT Hub så att enheten kan korrelera svaret med dess specifika tidigare begäran.

Följande sekvens beskriver hur en enhet uppdaterar de rapporterade egenskaperna i enhetstvillingen i IoT Hub:

1. En enhet måste först prenumerera på `$iothub/twin/res/#` ämnet för att få åtgärdens svar från IoT Hub.

2. En enhet skickar ett meddelande som innehåller enhetstvillingens uppdatering av `$iothub/twin/PATCH/properties/reported/?$rid={request id}` ämnet. Det här meddelandet innehåller ett värde **för begärande-ID.**

3. Tjänsten skickar sedan ett svarsmeddelande som innehåller det nya ETag-värdet för den rapporterade egenskapssamlingen i ämnet `$iothub/twin/res/{status}/?$rid={request id}` . Det här svarsmeddelandet använder **samma begärande-ID** som begäran.

Begärandemeddelandets brödtext innehåller ett JSON-dokument som innehåller nya värden för rapporterade egenskaper. Varje medlem i JSON-dokumentet uppdateras eller lägger till motsvarande medlem i enhetstvillingens dokument. En medlem som är `null` inställd på tar bort medlemmen från objektet som innehåller. Exempel:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 204 | Lyckades (inget innehåll returneras) |
| 400 | Felaktig begäran. Felaktig JSON |
| 429 | För många begäranden (begränsade) enligt [IoT Hub begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Python-kodfragmentet nedan visar uppdateringsprocessen för tvillingrapporterade egenskaper via MQTT (med Paho MQTT-klienten):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Vid lyckad uppdatering av tvillingrapporterade egenskaper ovan har `$iothub/twin/res/204/?$rid=1&$version=6` publiceringsmeddelandet från IoT Hub följande avsnitt: , där är statuskoden som anger att åtgärden lyckades, motsvarar det begärande-ID som tillhandahålls av enheten i koden och motsvarar versionen av avsnittet med rapporterade egenskaper för enhetstvillingarna efter `204` `$rid=1` `$version` uppdateringen.

Mer information finns i [utvecklarhandboken för enhetstvillingarna.](iot-hub-devguide-device-twins.md)

## <a name="receiving-desired-properties-update-notifications"></a>Ta emot uppdateringsmeddelanden för önskade egenskaper

När en enhet är ansluten IoT Hub meddelanden till ämnet , som innehåller innehållet i uppdateringen som `$iothub/twin/PATCH/properties/desired/?$version={new version}` utförs av lösningens backend-enhet. Exempel:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

När det gäller `null` egenskapsuppdateringar innebär värden att JSON-objektmedlemmen tas bort. Observera också att anger `$version` den nya versionen av det önskade egenskapsavsnittet för tvillingen.

> [!IMPORTANT]
> IoT Hub genererar endast ändringsmeddelanden när enheter är anslutna. Se till att implementera [enhetens återanslutningsflöde](iot-hub-devguide-device-twins.md#device-reconnection-flow) för att hålla önskade egenskaper synkroniserade mellan IoT Hub och enhetsappen.

Mer information finns i [utvecklarhandboken för enhetstvillingarna.](iot-hub-devguide-device-twins.md)

## <a name="respond-to-a-direct-method"></a>Svara på en direktmetod

Först måste en enhet prenumerera på `$iothub/methods/POST/#` . IoT Hub skickar metodbegäranden till ämnet `$iothub/methods/POST/{method name}/?$rid={request id}` , med antingen en giltig JSON eller en tom brödtext.

För att svara skickar enheten ett meddelande med en giltig JSON eller tom brödtext till ämnet `$iothub/methods/res/{status}/?$rid={request id}` . I det här meddelandet måste **begärande-ID:t** matcha det i begärandemeddelandet och **statusen** måste vara ett heltal.

Mer information finns i [utvecklarhandboken för direktmetod.](iot-hub-devguide-direct-methods.md)

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Som ett sista övervägande bör du granska Azure IoT-protokollgatewayen om du behöver anpassa MQTT-protokollbeteendet på [molnsidan.](iot-hub-protocol-gateway.md) Med den här programvaran kan du distribuera en högpresterande anpassad protokollgateway som gränssnitt direkt med IoT Hub. Med Azure IoT-protokollgatewayen kan du anpassa enhetsprotokollet för brownfield MQTT-distributioner eller andra anpassade protokoll. Den här metoden kräver dock att du kör och använder en anpassad protokollgateway.

## <a name="next-steps"></a>Nästa steg

Mer information om MQTT-protokollet finns i [MQTT-dokumentationen.](https://mqtt.org/)

Mer information om hur du planerar IoT Hub distribution finns i:

* [Azure Certified for IoT-enhetskatalog](https://devicecatalog.azure.com/)
* [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Jämför med Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalning, HÖG OCH DR](iot-hub-scaling.md)

Mer information om funktionerna i IoT Hub finns i:

* [IoT Hub utvecklarguide](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)
