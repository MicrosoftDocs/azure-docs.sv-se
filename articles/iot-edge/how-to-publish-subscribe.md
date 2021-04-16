---
title: Publicera och prenumerera med Azure IoT Edge | Microsoft Docs
description: Använda IoT Edge MQTT-meddelandekö för att publicera och prenumerera på meddelanden
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516186"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Publicera och prenumerera med Azure IoT Edge (förhandsversion)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Du kan använda MQTT-Azure IoT Edge för att publicera och prenumerera på meddelanden. Den här artikeln visar hur du ansluter till den här koordinatortjänsten, publicerar och prenumererar på meddelanden via användardefinierade ämnen och använder IoT Hub för meddelanden. Den IoT Edge MQTT-meddelandekö är inbyggd IoT Edge hubben. Mer information finns i [a brokering-funktionerna i IoT Edge hub](iot-edge-runtime.md).

> [!NOTE]
> IoT Edge MQTT-meddelandekö är för närvarande i offentlig förhandsversion.

## <a name="pre-requisites"></a>Förutsättningar

- Ett Azure-konto med en giltig prenumeration
- [Azure CLI](/cli/azure/) med `azure-iot` CLI-tillägget installerat. Mer information finns i [installationsstegen för Azure IoT-tillägg för Azure CLI.](/cli/azure/azure-cli-reference-for-iot)
- En **IoT Hub** av SKU: Antingen F1, S1, S2 eller S3.
- Ha en **IoT Edge enhet med version 1.2 eller senare.** Eftersom IoT Edge MQTT-meddelandekö för närvarande är i offentlig förhandsversion anger du följande miljövariabler till true på edgeHub-containern för att aktivera MQTT-koordinatorn:

   | Name | Värde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto-klienter** som är installerade IoT Edge enheten. I den här artikeln används populära Mosquitto-klienter [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) och [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Andra MQTT-klienter kan användas i stället. Kör följande kommando för att installera Mosquitto-klienter på en Ubuntu-enhet:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Installera inte Mosquitto-servern eftersom den kan orsaka blockering av MQTT-portarna (8883 och 1883) och orsaka konflikter IoT Edge hubben.

## <a name="connect-to-iot-edge-hub"></a>Ansluta till IoT Edge hub

Anslutning till IoT Edge-hubben följer samma steg som beskrivs i artikeln ansluta till IoT Hub med en allmän [MQTT-klient](../iot-hub/iot-hub-mqtt-support.md) eller i den konceptuella beskrivningen [av IoT Edge hub-artikeln](iot-edge-runtime.md). De här stegen är:

1. MQTT-klienten kan också upprätta en *säker anslutning* till IoT Edge med hjälp av Transport Layer Security (TLS)
2. MQTT-klienten *autentiserar sig* själv IoT Edge hubben
3. Den IoT Edge *hubben auktoriserar* MQTT-klienten enligt dess auktoriseringsprincip

### <a name="secure-connection-tls"></a>Säker anslutning (TLS)

Transport Layer Security (TLS) används för att upprätta en krypterad kommunikation mellan klienten och IoT Edge hubben.

Om du vill inaktivera TLS använder du port 1883 (MQTT) och binder edgeHub-containern till port 1883.

Om en klient ansluter på port 8883 (MQTTS) till MQTT-meddelandekö initieras en TLS-kanal för att aktivera TLS. Koordinatorn skickar sin certifikatkedja som klienten behöver verifiera. För att kunna verifiera certifikatkedjan måste rotcertifikatet för MQTT-koordinatorn installeras som ett betrott certifikat på klienten. Om rotcertifikatet inte är betrott avvisas klientbiblioteket av MQTT-koordinatorn med ett certifikatverifieringsfel. Stegen att följa för att installera rotcertifikatet för den asynbare meddelandekö på klienten är desamma som i [fallet med den transparenta gatewayen](how-to-create-transparent-gateway.md) och beskrivs i dokumentationen för att förbereda [en underordnad](how-to-connect-downstream-device.md#prepare-a-downstream-device) enhet.

### <a name="authentication"></a>Autentisering

För att en MQTT-klient ska kunna autentisera sig själv måste den först skicka ett CONNECT-paket till MQTT-meddelandekö för att initiera en anslutning i dess namn. Det här paketet innehåller tre delar av autentiseringsinformation: `client identifier` en , en och en `username` `password` :

- Fältet `client identifier` är namnet på enheten eller modulnamnet i IoT Hub. Den använder följande syntax:

  - För en enhet: `<device_name>`

  - För en modul: `<device_name>/<module_name>`

   För att kunna ansluta till MQTT-koordinatorn måste en enhet eller en modul registreras i IoT Hub.

   Den a broker tillåter inte anslutningar från flera klienter med samma autentiseringsuppgifter. Koordinatorn kopplar från den redan anslutna klienten om en andra klient ansluter med samma autentiseringsuppgifter.

- Fältet `username` härleds från enhets- eller modulnamnet och det IoTHub-namn som enheten tillhör med följande syntax:

  - För en enhet: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - För en modul: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Fältet `password` för CONNECT-paketet beror på autentiseringsläget:

  - När du [använder autentisering med symmetriska](how-to-authenticate-downstream-device.md#symmetric-key-authentication)nycklar `password` är fältet en SAS-token.
  - När du [använder själv signerad X.509-autentisering](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) `password` finns inte fältet. I det här autentiseringsläget krävs en TLS-kanal. Klienten måste ansluta till port 8883 för att upprätta en TLS-anslutning. Under TLS-handskakningen begär MQTT-koordinatorn ett klientcertifikat. Det här certifikatet används för att verifiera klientens identitet och fältet `password` behövs därför inte senare när CONNECT-paketet skickas. Att skicka både ett klientcertifikat och lösenordsfältet leder till ett fel och anslutningen stängs. MQTT-bibliotek och TLS-klientbibliotek har vanligtvis ett sätt att skicka ett klientcertifikat när du initierar en anslutning. Du kan se ett steg-för-steg-exempel i avsnittet [Använda X509-certifikat för klientautentisering.](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)

Moduler som distribueras IoT Edge använder autentisering med [symmetriska](how-to-authenticate-downstream-device.md#symmetric-key-authentication) nycklar och kan anropa API:et för den lokala [IoT Edge-arbetsbelastningen](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) för att programmatiskt hämta en SAS-token även när den är offline.

### <a name="authorization"></a>Auktorisering

När en MQTT-klient har autentiserats IoT Edge en hubb måste den ha behörighet att ansluta. När du är ansluten måste den ha behörighet att publicera eller prenumerera på specifika ämnen. Dessa auktoriseringar beviljas av den IoT Edge hubben baserat på dess auktoriseringsprincip. Auktoriseringsprincipen är en uppsättning instruktioner uttryckta som en JSON-struktur som skickas till IoT Edge-hubben via dess tvilling. Redigera en IoT Edge för att konfigurera auktoriseringsprincipen.

> [!NOTE]
> För den offentliga förhandsversionen stöder endast Azure CLI distributioner som innehåller auktoriseringsprinciper för MQTT-meddelandeköer. Den Azure Portal stöder för närvarande inte redigering av IoT Edge-hubbtvillingen och dess auktoriseringsprincip.

Varje auktoriseringsprincip består av en kombination `identities` av , `allow` eller `deny` `operations` effekter, , och `resources` :

- `identities` beskriva principens ämne. Den måste mappas till `client identifier` som skickas av klienter i sina CONNECT-paket.
- `allow` eller `deny` effekter definierar om åtgärder ska tillåtas eller nekas.
- `operations` definiera de åtgärder som ska auktorisera. `mqtt:connect`och `mqtt:publish` är de tre åtgärder som stöds i `mqtt:subscribe` dag.
- `resources` definiera -objektet för principen. Det kan vara ett ämne eller ett ämnesmönster som [definierats med jokertecken i MQTT.](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)

Nedan visas ett exempel på en auktoriseringsprincip som uttryckligen inte tillåter att "rogue_client"-klienten ansluter, tillåter alla Azure IoT-klienter att ansluta och tillåter att "sensor_1" publicerar till ämnet `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Några saker att tänka på när du skriver auktoriseringsprincipen:

- Det kräver `$edgeHub` tvillingschema version 1.2
- Som standard nekas alla åtgärder.
- Auktoriseringsutsatser utvärderas i den ordning som de visas i JSON-definitionen. Den börjar med att titta `identities` på och väljer sedan de första tillåt- eller neka-uttrycken som matchar begäran. Om det uppstår konflikter mellan allow- och deny-uttryck vinner neka-instruktionen.
- Flera variabler (till exempel ersättningar) kan användas i auktoriseringsprincipen:

  - `{{iot:identity}}` representerar identiteten för den aktuella anslutna klienten. Till exempel en enhetsidentitet som `myDevice` eller en modulidentitet som `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` representerar identiteten för den anslutna enheten. Till exempel en enhetsidentitet som `myDevice` eller enhetsidentiteten där en modul körs som `myEdgeDevice` .
  - `{{iot:module_id}}` representerar identiteten för den anslutna modulen. Den här variabeln är tom för anslutna enheter eller en modulidentitet som `SampleModule` .
  - `{{iot:this_device_id}}` representerar identiteten för den IoT Edge som kör auktoriseringsprincipen. Till exempel `myIoTEdgeDevice`.

Auktoriseringar för IoT Hub-ämnen hanteras något annorlunda än användardefinierade ämnen. Här är de viktigaste punkterna att komma ihåg:

- Azure IoT-enheter eller -moduler behöver en explicit auktoriseringsregel för att ansluta IoT Edge MQTT-koordinator. En standardprincip för anslutningsauktorisering finns nedan.
- Azure IoT-enheter eller -moduler kan som standard komma åt sina egna IoT Hub-ämnen utan någon explicit auktoriseringsregel. Auktoriseringar kommer dock från överordnade/underordnade relationer i det fallet och dessa relationer måste anges. IoT Edge konfigureras automatiskt som underordnade till sina IoT Edge-enheter, men enheter måste uttryckligen anges som underordnade till deras IoT Edge gateway.

Här är en standardauktoriseringsprincip som kan användas för att göra det möjligt för alla Azure IoT-enheter eller -moduler att **ansluta till** den autjämnare:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Nu när du förstår hur du ansluter till MQTT-koordinatorn för IoT Edge ska vi se hur den kan användas för att publicera och prenumerera meddelanden först i användardefinierade ämnen, sedan i IoT Hub-ämnen och slutligen till en annan MQTT-koordinator.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publicera och prenumerera på användardefinierade ämnen

I den här artikeln använder du en klient med **namnet sub_client** som prenumererar på ett ämne och en annan klient med **namnet pub_client** som publicerar till ett ämne. Vi använder autentisering med [symmetrisk](how-to-authenticate-downstream-device.md#symmetric-key-authentication) nyckel, men samma sak kan göras med själv signerad [X.509-autentisering](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) eller [X.509 CA-signerad autentisering.](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)

### <a name="create-publisher-and-subscriber-clients"></a>Skapa utgivar- och prenumerantklienter

Skapa två IoT-enheter IoT Hub och hämta deras lösenord. Använda Azure CLI från terminalen för att:

1. Skapa två IoT-enheter i IoT Hub överordnade dem till din IoT Edge enhet:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. Hämta sina lösenord genom att generera en SAS-token:

   - För en enhet:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     där 3600 är varaktigheten för SAS-token i sekunder (till exempel 3 600 = 1 timme).

   - För en modul:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     där 3600 är varaktigheten för SAS-token i sekunder (till exempel 3600 = 1 timme).

3. Kopiera SAS-token, vilket är det värde som motsvarar "sas"-nyckeln från utdata. Här är ett exempel på utdata från Azure CLI-kommandot ovan:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Auktorisera utgivare och prenumerantklienter

För att auktorisera utgivaren och prenumeranten redigerar du IoT Edge-hubbtvillingen i en IoT Edge-distribution som innehåller följande auktoriseringsprincip.

>[!NOTE]
>För närvarande kan distributioner som innehåller MQTT-auktoriseringsegenskaper endast tillämpas IoT Edge enheter med hjälp av Azure CLI.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Autentisering med symmetriska nycklar utan TLS

#### <a name="subscribe"></a>Prenumerera

Anslut din **sub_client** MQTT-klient till MQTT-koordinatorn och prenumerera på genom att köra följande kommando på `test_topic` din IoT Edge enhet:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

där `<edge_device_address>`  =  `localhost` i det här exemplet eftersom klienten körs på samma enhet som IoT Edge.

Observera att port 1883 (MQTT), utan TLS, används i det här första exemplet. Ett annat exempel med port 8883 (MQTTS), med TLS aktiverat, visas i nästa avsnitt.

Nu **sub_client** MQTT-klienten och väntar på inkommande meddelanden på `test_topic` .

#### <a name="publish"></a>Publicera

Anslut **din pub_client** MQTT-klient till MQTT-koordinatorn och publicera ett meddelande på samma sätt som ovan genom att köra följande kommando på din `test_topic` IoT Edge enhet från en annan terminal:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

där `<edge_device_address>`  =  `localhost` i det här exemplet eftersom klienten körs på samma enhet som IoT Edge.

När kommandot körs får **sub_client** MQTT-klienten meddelandet "hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Autentisering med symmetriska nycklar med TLS

Om du vill aktivera TLS måste porten ändras från 1883 (MQTT) till 8883 (MQTTS) och klienterna måste ha rotcertifikatet för MQTT-koordinatorn för att kunna verifiera certifikatkedjan som skickas av MQTT-koordinatorn. Detta kan göras genom att följa stegen i avsnittet [Säker anslutning (TLS).](#secure-connection-tls)

Eftersom klienterna körs på samma enhet som MQTT-koordinatorn i exemplet ovan gäller samma steg för att aktivera TLS genom att bara ändra portnumret från 1883 (MQTT) till 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publicera och prenumerera på IoT Hub avsnitt

Med [AZURE IoT-enhets-SDK:er](https://github.com/Azure/azure-iot-sdks) kan klienter redan utföra IoT Hub åtgärder, men de tillåter inte publicering/prenumeration på användardefinierade ämnen. IoT Hub kan utföras med hjälp av valfri MQTT-klienter som använder publicerings- och prenumerationssemantik så länge IoT Hub-primitiva protokoll respekteras. Vi går igenom de specifika för att förstå hur dessa protokoll fungerar.

### <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

Att skicka telemetridata till IoT Hub liknar publicering i ett användardefinierat ämne, men med ett specifikt IoT Hub ämne:

- För en enhet skickas telemetri om ämnet: `devices/<device_name>/messages/events/`
- För en modul skickas telemetri om ämnet: `devices/<device_name>/<module_name>/messages/events/`

Skapa dessutom en väg som för att skicka telemetri från den IoT Edge `FROM /messages/* INTO $upstream` MQTT-meddelandekö till IoT Hub. Mer information om routning finns i [Deklarera vägar](module-composition.md#declare-routes).

### <a name="get-twin"></a>Hämta tvilling

Att hämta enhets-/modultvillingen är inte ett typiskt MQTT-mönster. Klienten måste utfärda en begäran för den tvilling som IoT Hub ska fungera.

För att kunna ta emot tvillingar måste klienten prenumerera på en IoT Hub specifika ämnet `$iothub/twin/res/#` . Det här ämnesnamnet ärvs IoT Hub och alla klienter måste prenumerera på samma ämne. Det innebär inte att enheter eller moduler tar emot varandras tvilling. IoT Hub och IoT Edge hubben vet vilken tvilling som ska levereras var, även om alla enheter lyssnar på samma ämnesnamn.

När prenumerationen har gjorts måste klienten fråga efter tvillingen genom att publicera ett meddelande till IoT Hub specifika ämne `$iothub/twin/GET/?rid=<request_id>/#` där är en godtycklig  `<request_id>` identifierare. IoT Hub skickar sedan sitt svar med begärda data om `$iothub/twin/res/200/?rid=<request_id>` ämnet , som klienten prenumererar på. Det är så en klient kan koppla sina begäranden med svaren.

### <a name="receive-twin-patches"></a>Ta emot tvillingkorrigeringar

För att kunna ta emot tvillingkorrigeringar måste en klient prenumerera på ett särskilt IoTHub-ämne. `$iothub/twin/PATCH/properties/desired/#` När prenumerationen har gjorts tar klienten emot de tvillingkorrigeringar som skickas av IoT Hub om det här ämnet.

### <a name="receive-direct-methods"></a>Ta emot direktmetoder

Att ta emot en direktmetod liknar att ta emot fullständiga tvillingar med tillägget att klienten måste bekräfta att den har tagit emot anropet. Först prenumererar klienten på det särskilda IoT Hub-ämnet `$iothub/methods/POST/#` . När en direktmetod tas emot i det här ämnet måste klienten extrahera begärandeidentifieraren från det underavsnitt där direktmetoden tas emot och slutligen publicera ett bekräftelsemeddelande i det särskilda `rid` ämnet för IoT `$iothub/methods/res/200/<request_id>` Hub.

### <a name="send-direct-methods"></a>Skicka direktmetoder

Att skicka en direktmetod är ett HTTP-anrop och går därför inte igenom MQTT-koordinatorn. Information om hur du skickar en direktmetod till IoT Hub finns [i Förstå och anropa direktmetoder](../iot-hub/iot-hub-devguide-direct-methods.md). Om du vill skicka en direktmetod lokalt till en annan modul kan du se det här exemplet på anrop av direktmetod för [Azure IoT C# SDK.](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597)

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publicera och prenumerera mellan MQTT-meddelandeköer

För att ansluta två MQTT-meddelandeköer IoT Edge hubben en MQTT-brygga. En MQTT-brygga används ofta för att ansluta en MQTT-koordinator som körs till en annan MQTT-koordinator. Det är vanligtvis bara en delmängd av den lokala trafiken som skickas till en annan koordinator.

> [!NOTE]
> Den IoT Edge hubbbryggan kan för närvarande endast användas mellan kapslade IoT Edge enheter. Den kan inte användas för att skicka data till IoT Hub eftersom IoT Hub inte är en fullständig MQTT-koordinator. Mer information om stöd för MQTT-koordinatorfunktioner i IoT Hub finns i Kommunicera med [din IoT-hubb med hjälp av MQTT-protokollet](../iot-hub/iot-hub-mqtt-support.md). Mer information om hur du IoT Edge enheter finns i [Ansluta en underordnad IoT Edge till en Azure IoT Edge gateway.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

I en kapslad konfiguration fungerar IoT Edge Hub MQTT-bryggan som en klient för den överordnade MQTT-koordinatorn, så auktoriseringsregler måste anges på den överordnade EdgeHub så att den underordnade EdgeHub kan publicera och prenumerera på specifika användardefinierade ämnen som bryggan är konfigurerad för.

MQTT IoT Edge bryggan konfigureras via en JSON-struktur som skickas till IoT Edge via dess tvilling. Redigera en IoT Edge för att konfigurera dess MQTT-brygga.

> [!NOTE]
> För den offentliga förhandsversionen stöder endast Azure CLI distributioner som innehåller MQTT-bryggkonfigurationer. Den Azure Portal stöder för närvarande inte redigering av IoT Edge-hubbtvillingen och dess MQTT-bryggkonfiguration.

MQTT-bryggan kan konfigureras för att ansluta en MQTT-IoT Edge MQTT-koordinator till flera externa a brokers. För varje extern koordinator krävs följande inställningar:

- `endpoint` är adressen till den fjärranslutna MQTT-koordinatorn att ansluta till. Endast överordnade IoT Edge enheter stöds för närvarande och definieras av variabeln `$upstream` .
- `settings` definierar vilka ämnen som ska överbryggas för en slutpunkt. Det kan finnas flera inställningar per slutpunkt och följande värden används för att konfigurera den:
  - `direction`: `in` antingen för att prenumerera på fjärrutjämnare ämnen eller `out` publicera till fjärrutjämning ämnen
  - `topic`: mönster för kärnämne som ska matchas. [Jokertecken i MQTT kan](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) användas för att definiera det här mönstret. Olika prefix kan tillämpas på det här ämnesmönstret på den lokala koordinatorn och den fjärranslutna meddelandeköen.
  - `outPrefix`: Prefix som tillämpas på mönstret på `topic` den fjärranslutna koordinatorn.
  - `inPrefix`: Prefix som tillämpas på mönstret på `topic` den lokala koordinatorn.

Nedan visas ett exempel på en IoT Edge MQTT-bryggkonfiguration som publicerar alla meddelanden som tas emot på ämnen för en överordnad IoT Edge-enhet till en underordnad IoT Edge-enhet i samma avsnitt och publicerar om alla meddelanden som skickas på ämnen för en underordnad IoT Edge-enhet till en överordnad IoT Edge-enhet i ämnen `alerts/#` `/local/telemetry/#` `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
Andra kommentarer om MQTT IoT Edge bryggan för IoT Edge:
- MQTT-protokollet används automatiskt som uppströmsprotokoll när MQTT-koordinatorn används och den IoT Edge används i en kapslad konfiguration, till exempel med en `parent_hostname` angiven. Mer information om överordnade protokoll finns i [Molnkommunikation.](iot-edge-runtime.md#cloud-communication) Mer information om kapslade konfigurationer finns i [Ansluta en underordnad IoT Edge enhet till en Azure IoT Edge gateway.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

## <a name="next-steps"></a>Nästa steg

[Förstå IoT Edge hubben](iot-edge-runtime.md#iot-edge-hub)
