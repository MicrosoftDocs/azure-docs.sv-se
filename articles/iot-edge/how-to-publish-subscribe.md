---
title: Publicera och prenumerera med Azure IoT Edge | Microsoft Docs
description: Använd IoT Edge MQTT Broker för att publicera och prenumerera på meddelanden
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 13bfd7c602389ff286a80f625829da5924a73bdf
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621903"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Publicera och prenumerera med Azure IoT Edge

Du kan använda Azure IoT Edge MQTT Broker för att publicera och prenumerera på meddelanden. Den här artikeln visar hur du ansluter till den här Service Broker, publicerar och prenumererar på meddelanden över användardefinierade ämnen och använder IoT Hub-översättande primitiver. IoT Edge MQTT-Broker är inbyggd IoT Edge Hub. Mer information finns i [Utjämnings funktionerna i IoT Edge Hub](iot-edge-runtime.md).

> [!NOTE]
> IoT Edge MQTT-Broker finns för närvarande i en offentlig för hands version.

## <a name="pre-requisites"></a>Förutsättningar

- Ett Azure-konto med en giltig prenumeration
- [Azure CLI](/cli/azure/) med `azure-iot` CLI-tillägget installerat. Mer information finns i [installations stegen för Azure IoT Extension för Azure Azure CLI](/cli/azure/azure-cli-reference-for-iot).
- En **IoT Hub** SKU antingen F1, S1, S2 eller S3.
- Ha en **IoT Edge-enhet med version 1,2 eller senare**. Eftersom IoT Edge MQTT-Broker för närvarande finns i en offentlig för hands version kan du ange följande miljövariabler som true på behållaren edgeHub för att aktivera MQTT-Broker:

   | Name | Värde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Mosquitto-klienter** som är installerade på den IoT Edge enheten. I den här artikeln används populära Mosquitto-klienter [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) och [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). Andra MQTT-klienter kan användas i stället. Kör följande kommando för att installera Mosquitto-klienter på en Ubuntu-enhet:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Installera inte Mosquitto-servern eftersom den kan orsaka blockering av MQTT-portarna (8883 och 1883) och vara i konflikt med IoT Edge hubben.

## <a name="connect-to-iot-edge-hub"></a>Anslut till IoT Edge hubb

Anslutning till IoT Edge Hub följer samma steg som beskrivs i avsnittet [ansluta till IoT Hub med en allmän MQTT-klient artikel](../iot-hub/iot-hub-mqtt-support.md) eller i den [konceptuella beskrivningen av artikeln om IoT Edge Hub](iot-edge-runtime.md). De här stegen är:

1. Om du vill kan MQTT-klienten *upprätta en säker anslutning* med IoT Edge hub med Transport Layer Security (TLS)
2. MQTT-klienten *autentiseras* till IoT Edge hubb
3. IoT Edge Hub *godkänner* MQTT-klienten per princip för auktorisering

### <a name="secure-connection-tls"></a>Säker anslutning (TLS)

Transport Layer Security (TLS) används för att upprätta en krypterad kommunikation mellan klienten och IoT Edge Hub.

Om du vill inaktivera TLS använder du Port 1883 (MQTT) och binder edgeHub-behållaren till port 1883.

För att aktivera TLS, kommer en TLS-kanal att initieras om en klient ansluter på port 8883 (MQTTS) till MQTT-Broker. Broker skickar den certifikat kedja som klienten behöver verifiera. För att kunna verifiera certifikat kedjan måste rot certifikatet för MQTT-Broker installeras som ett betrott certifikat på klienten. Om rot certifikatet inte är betrott avvisas klient biblioteket av MQTT-koordinatorn med ett certifikat verifierings fel. Stegen för att installera det här rot certifikatet för Service Broker på klienten är desamma som i det [transparenta Gateway](how-to-create-transparent-gateway.md) -fallet och beskrivs i [förbereda en underordnad enhets](how-to-connect-downstream-device.md#prepare-a-downstream-device) dokumentation.

### <a name="authentication"></a>Autentisering

För att en MQTT-klient ska kunna autentisera sig själv måste den först skicka ett CONNECT-paket till MQTT-koordinatorn för att initiera en anslutning i sitt namn. Det här paketet innehåller tre delar av autentiseringsinformation: a `client identifier` , a `username` och a `password` :

- `client identifier`Fältet är namnet på enhets-eller modulens namn i IoT Hub. Den använder följande syntax:

  - För en enhet: `<device_name>`

  - För en modul: `<device_name>/<module_name>`

   För att kunna ansluta till MQTT-Broker måste en enhet eller modul registreras i IoT Hub.

   Service Broker tillåter inte anslutningar från flera klienter med samma autentiseringsuppgifter. Koordinatorn kopplar från den redan anslutna klienten om en andra klient ansluter med samma autentiseringsuppgifter.

- `username`Fältet härleds från enhets-eller modulens namn och IoTHub namn som enheten tillhör med följande syntax:

  - För en enhet: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - För en modul: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`Fältet i Connect-paketet är beroende av autentiseringsläget:

  - När du använder [autentisering med symmetriska nycklar](how-to-authenticate-downstream-device.md#symmetric-key-authentication) `password` är fältet en SAS-token.
  - När du använder [självsignerad X. 509-autentisering](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)finns `password` inte fältet. I det här autentiseringsläget krävs en TLS-kanal. Klienten måste ansluta till port 8883 för att upprätta en TLS-anslutning. Under TLS-handskakningen begär MQTT-koordinatorn ett klient certifikat. Det här certifikatet används för att verifiera klientens identitet och därför `password` behövs inte fältet senare när anslutnings paketet skickas. Att skicka både ett klient certifikat och fältet lösen ord leder till ett fel och anslutningen stängs. MQTT-bibliotek och TLS-klientcertifikat har vanligt vis ett sätt att skicka ett klient certifikat när en anslutning initieras. Du kan se ett steg-för-steg-exempel i avsnittet [använda X509-certifikat för klientautentisering](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

Moduler som distribueras med IoT Edge använda [symmetriska nycklar-autentisering](how-to-authenticate-downstream-device.md#symmetric-key-authentication) och anropar den [IoT Edge lokala API: et för arbets belastningen](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) för att få en SAS-token, även när du är offline.

### <a name="authorization"></a>Auktorisering

När en MQTT-klient autentiseras för IoT Edge hubb måste den ha behörighet att ansluta. När den är ansluten måste den ha behörighet att publicera eller prenumerera på specifika ämnen. Dessa auktoriseringar beviljas av IoT Edge Hub baserat på dess auktoriseringsprincip. Auktoriseringsprincipen är en uppsättning instruktioner som uttryckts som en JSON-struktur som skickas till IoT Edge Hub via dess dubbla. Redigera en IoT Edge hubben för att konfigurera auktoriseringsprincipen.

> [!NOTE]
> För den allmänt tillgängliga för hands versionen är redigeringen av Auktoriseringsprinciper för MQTT-Broker endast tillgänglig via Visual Studio, Visual Studio Code eller Azure CLI. Azure Portal stöder för närvarande inte redigering av IoT Edge Hub, dubbel och dess auktoriseringsprincip.

Varje policy för Auktoriseringsprinciper består av en kombination av `identities` , `allow` eller `deny` effekter, `operations` och `resources` :

- `identities` Beskriv principens ämne. Den måste mappas till den som `client identifier` skickas av klienter i deras Connect-paket.
- `allow` eller `deny` effekter definierar om åtgärder ska tillåtas eller nekas.
- `operations` definiera vilka åtgärder som ska auktoriseras. `mqtt:connect``mqtt:publish`och `mqtt:subscribe` är de tre åtgärder som stöds idag.
- `resources` definiera principens objekt. Det kan vara ett ämne eller ett ämnes mönster som definierats med [jokertecken i MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Nedan visas ett exempel på en auktoriseringsprincip som uttryckligen inte tillåter att "rogue_client"-klienten ansluter till, tillåter att alla Azure IoT-klienter ansluter och tillåter "sensor_1" att publicera i avsnittet `events/alerts` .

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

- Den kräver `$edgeHub` dubbla schema version 1,2
- Som standard nekas alla åtgärder.
- Authorization-instruktioner utvärderas i den ordning som de visas i JSON-definitionen. Den börjar med att titta på `identities` och väljer sedan de första Allow-eller DENY-instruktioner som matchar begäran. Om det uppstår konflikter mellan Allow-och Deny-instruktioner, neka-instruktionen WINS.
- Flera variabler (till exempel ersättningar) kan användas i auktoriseringsprincipen:
    - `{{iot:identity}}` representerar identiteten för den för tillfället anslutna klienten. Till exempel en enhets identitet som `myDevice` eller en modul identitet som `myEdgeDevice/SampleModule` .
    - `{{iot:device_id}}` representerar identiteten för den anslutna enhet som är ansluten. Till exempel en enhets identitet som `myDevice` eller enhets identiteten där en modul körs `myEdgeDevice` .
    - `{{iot:module_id}}` representerar identiteten för den för tillfället anslutna modulen. Den här variabeln är tom för anslutna enheter eller en modul identitet som `SampleModule` .
    - `{{iot:this_device_id}}` representerar identiteten för den IoT Edge enhet som kör auktoriseringsprincipen. Ett exempel är `myIoTEdgeDevice`.

Auktoriseringar för avsnitt om IoT Hub hanteras något annorlunda än användardefinierade ämnen. Här är de viktiga punkter som du bör komma ihåg:

- Azure IoT-enheter eller-moduler behöver en uttrycklig auktoriseringsregel för att ansluta till IoT Edge Hub MQTT Broker. En auktoriseringsprincip för standard anslutning anges nedan.
- Azure IoT-enheter eller-moduler kan komma åt sina egna IoT Hub-ämnen som standard utan någon explicit auktoriseringsregel. Dock måste auktoriseringen härröra från överordnade/underordnade relationer i detta fall och dessa relationer måste anges. IoT Edge moduler anges automatiskt som underordnade till deras IoT Edge enhet, men enheterna måste uttryckligen anges som underordnade till deras IoT Edge Gateway.

Här är en standard princip för auktorisering som kan användas för att aktivera alla Azure IoT-enheter eller-moduler för att **ansluta** till Service Broker:

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

Nu när du förstår hur du ansluter till den IoT Edge MQTT-Broker kan vi se hur den kan användas för att publicera och prenumerera på meddelanden först i användardefinierade ämnen, sedan på IoT Hub-ämnen och slutligen till en annan MQTT-Broker.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Publicera och prenumerera på användardefinierade ämnen

I den här artikeln använder du en klient med namnet **sub_client** som prenumererar på ett ämne och en annan klient som heter **pub_client** som publiceras i ett ämne. Vi använder autentisering med [symmetrisk nyckel](how-to-authenticate-downstream-device.md#symmetric-key-authentication) men samma kan göras med den [självsignerade 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) -autentiseringen i x. eller [x. 509](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Skapa utgivare och prenumerant klienter

Skapa två IoT-enheter i IoT Hub och hämta sina lösen ord. Använda Azure CLI från terminalen för att:

1. Skapa två IoT-enheter i IoT Hub överordna dem till IoT Edge enhet:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Hämta sina lösen ord genom att skapa en SAS-token:

    - För en enhet:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       där 3600 är varaktigheten för SAS-token i sekunder (till exempel 3600 = 1 timme).
    
    - För en modul:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       där 3600 är varaktigheten för SAS-token i sekunder (till exempel 3600 = 1 timme).

3. Kopiera SAS-token, vilket är värdet som motsvarar nyckeln "SAS" från utdata. Här är ett exempel på utdata från Azure CLI-kommandot ovan:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Auktorisera utgivare och prenumerant klienter

Om du vill auktorisera utgivaren och prenumeranten redigerar du IoT Edge Hub, dubbelt genom att skapa en IoT Edge distribution antingen via Azure CLI, Visual Studio eller Visual Studio Code för att inkludera följande auktoriseringsprincip:

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

### <a name="symmetric-keys-authentication-without-tls"></a>Symmetriska nycklar-autentisering utan TLS

#### <a name="subscribe"></a>Prenumerera

Anslut din **sub_client** MQTT-klient till MQTT-Broker och prenumerera på `test_topic` genom att köra följande kommando på IoT Edge enheten:

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

var `<edge_device_address>`  =  `localhost` i det här exemplet eftersom klienten körs på samma enhet som IoT Edge.

Observera att port 1883 (MQTT), utan TLS, används i det första exemplet. Ett annat exempel med port 8883 (MQTTS), med TLS aktiverat, visas i nästa avsnitt.

Den **sub_client** MQTT-klienten har startats och väntar på inkommande meddelanden `test_topic` .

#### <a name="publish"></a>Publicera

Anslut din **pub_client** MQTT-klient till MQTT-Broker och publicera ett meddelande på samma `test_topic` sätt som ovan genom att köra följande kommando på din IoT Edge-enhet från en annan terminal:

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

var `<edge_device_address>`  =  `localhost` i det här exemplet eftersom klienten körs på samma enhet som IoT Edge.

Kommandot körs, **sub_client** MQTT-klienten tar emot meddelandet "Hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Symmetriska nycklar autentisering med TLS

Om du vill aktivera TLS måste porten ändras från 1883 (MQTT) till 8883 (MQTTS) och klienterna måste ha rot certifikatet för MQTT-Broker för att kunna verifiera certifikat kedjan som skickas av MQTT-Broker. Det kan du göra genom att följa stegen i avsnittet [säker anslutning (TLS)](#secure-connection-tls).

Eftersom klienterna körs på samma enhet som MQTT-koordinatorn i exemplet ovan gäller samma steg för att aktivera TLS genom att ändra port numret från 1883 (MQTT) till 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Publicera och prenumerera på IoT Hub ämnen

SDK: er för [Azure IoT-enheter](https://github.com/Azure/azure-iot-sdks) låter redan klienter utföra IoT Hub åtgärder, men de tillåter inte publicering/prenumerationer på användardefinierade ämnen. IoT Hub-åtgärder kan utföras med hjälp av alla MQTT-klienter med hjälp av publicerings-och prenumereras-semantik så länge som IoT Hub-protokoll iakttas. Vi går igenom de olika stegen för att förstå hur dessa protokoll fungerar.

### <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

Att skicka telemetridata till IoT Hub liknar att publicera i ett användardefinierat ämne, men med hjälp av ett viss IoT Hub-ämne:

- För en enhet skickas telemetri på följande avsnitt: `devices/<device_name>/messages/events`
- För en modul skickas telemetri i avsnittet: `devices/<device_name>/<module_name>/messages/events`

Skapa dessutom en väg, till exempel `FROM /messages/* INTO $upstream` för att skicka telemetri från IoT Edge MQTT-Broker till IoT Hub. Mer information om routning finns i [deklarera vägar](module-composition.md#declare-routes).

### <a name="get-twin"></a>Få dubbla

Hämtning av enheten/modulen är inte ett typiskt MQTT-mönster. Klienten måste utfärda en begäran för den dubbla som IoT Hub ska fungera.

För att kunna ta emot dubbla måste klienten prenumerera på ett IoT Hub-särskilt ämne `$iothub/twin/res/#` . Det här ämnes namnet ärvs från IoT Hub och alla klienter måste prenumerera på samma ämne. Det innebär inte att enheter eller moduler får den dubbla av varandra. IoT Hub och IoT Edge Hub vet vilka dubbla som ska levereras, även om alla enheter lyssnar på samma ämnes namn. 

När prenumerationen har gjorts måste klienten be om den dubbla genom att publicera ett meddelande till ett IoT Hub-enskilt ämne `$iothub/twin/GET/?rid=<request_id>/#` där  `<request_id>` är en godtycklig identifierare. IoT Hub skickar sedan sitt svar med de begärda data i avsnittet `$iothub/twin/res/200/?rid=<request_id>` som klienten prenumererar på. Så här kan en klient koppla sina begär anden till svaren.

### <a name="receive-twin-patches"></a>Få dubbla korrigeringar

För att få dubbla korrigeringar måste en klient prenumerera på särskilda IoTHub-avsnitt `$iothub/twin/PATCH/properties/desired/#` . När prenumerationen har gjorts får klienten de dubbla korrigeringarna som skickas av IoT Hub i det här avsnittet. 

### <a name="receive-direct-methods"></a>Ta emot direkta metoder

Att ta emot en direkt metod liknar att ta emot fullständiga dubbla med det tillägg som klienten behöver för att bekräfta att klienten har tagit emot anropet. Första klienten prenumererar på Special ämnet för IoT Hub `$iothub/methods/POST/#` . När en direkt metod tas emot i det här avsnittet måste klienten extrahera begärande-ID: n `rid` från det underavsnitt där direkt metoden tas emot och slutligen publicera ett bekräftelse meddelande på IoT Hub Special-avsnittet `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Skicka direkta metoder

Att skicka en direkt metod är ett HTTP-anrop och går därför inte igenom MQTT-koordinatorn. Information om hur du skickar en direkt metod till IoT Hub finns i [förstå och anropa direkta metoder](../iot-hub/iot-hub-devguide-direct-methods.md). Om du vill skicka en direkt metod lokalt till en annan modul kan du läsa detta [exempel på Azure IoT C# SDK Direct-metoden](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Publicera och prenumerera mellan MQTT-mäklare

För att ansluta två MQTT-utjämnare innehåller IoT Edge hubben en MQTT-brygga. En MQTT-brygga används ofta för att ansluta en MQTT-Broker som körs till en annan MQTT-Broker. Endast en del av den lokala trafiken skickas till en annan Broker.

> [!NOTE]
> IoT Edge Hub-bryggan kan för närvarande endast användas mellan kapslade IoT Edge enheter. Det kan inte användas för att skicka data till IoT Hub eftersom IoT Hub inte är en komplett MQTT-Broker. Mer information om stöd för IoT Hub MQTT Broker-funktioner finns i [kommunicera med IoT Hub med hjälp av MQTT-protokollet](../iot-hub/iot-hub-mqtt-support.md). Om du vill veta mer om att kapsla IoT Edge enheter kan du läsa [Anslut en underordnad IoT Edge enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

I en kapslad konfiguration fungerar IoT Edge Hub MQTT-bryggan som en klient med överordnad MQTT Broker, så auktoriseringsregler måste anges för den överordnade EdgeHub så att den underordnade EdgeHub kan publicera och prenumerera på specifika användardefinierade ämnen som bryggan har kon figurer ATS för.

IoT Edge MQTT-bryggan konfigureras via en JSON-struktur som skickas till IoT Edge Hub via dess dubbla. Redigera en IoT Edge hubben för att konfigurera dess MQTT-brygga.

> [!NOTE]
> För den allmänt tillgängliga för hands versionen är konfigurationen av MQTT-bryggan endast tillgänglig via Visual Studio, Visual Studio Code eller Azure CLI. Azure Portal stöder för närvarande inte redigering av IoT Edge Hub, dubbla och dess konfiguration av MQTT-bryggan.

MQTT-bryggan kan konfigureras för att ansluta en IoT Edge hubb MQTT-Broker till flera externa-mäklare. För varje extern Broker krävs följande inställningar:

- `endpoint` är adressen till den fjärranslutna MQTT-koordinatorn. Endast överordnade IoT Edge enheter stöds för närvarande och definieras av variabeln `$upstream` .
- `settings` definierar vilka ämnen som ska bryggas för en slut punkt. Det kan finnas flera inställningar per slut punkt och följande värden används för att konfigurera den:
    - `direction`: antingen `in` prenumererar du på ämnena i den fjärranslutna Service Broker eller publicerar dem i avsnittet om `out` fjärrbroker
    - `topic`: ett kärn ämnes mönster som ska matchas. [Jokertecken i MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) kan användas för att definiera det här mönstret. Olika prefix kan tillämpas på det här avsnitts mönstret i den lokala Service Broker-och fjärranslutna koordinatorn.
    - `outPrefix`: Prefix som tillämpas på `topic` mönstret på fjärrservern.
    - `inPrefix`: Prefix som tillämpas på `topic` mönstret i den lokala utjämningen.

Nedan visas ett exempel på en IoT Edge MQTT brygga-konfiguration som återpublicerar alla meddelanden som tas emot på ämnen `alerts/#` i en överordnad IoT Edge-enhet till en underordnad IoT Edge enhet i samma ämnen och publicerar om alla meddelanden som skickas på ämnen `/local/telemetry/#` av en underordnad IoT Edge enhet till en överordnad IoT Edge enhet i ämnen `/remote/messages/#` .

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
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Andra anteckningar på IoT Edge Hub MQTT-bryggan:
- MQTT-protokollet kommer automatiskt att användas som överordnat protokoll när MQTT-Broker används och IoT Edge används i en kapslad konfiguration, till exempel med en `parent_hostname` angiven. Läs mer om överordnade protokoll i [moln kommunikation](iot-edge-runtime.md#cloud-communication). Mer information om kapslade konfigurationer finns i [ansluta en underordnad IoT Edge enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Nästa steg

[Förstå IoT Edge Hub](iot-edge-runtime.md#iot-edge-hub)