---
title: Lär dig mer om Azure IoT Hub-säkerhet | Microsoft Docs
description: Guide för utvecklare – hur du styr åtkomsten till IoT Hub för enhets program och backend-appar. Innehåller information om säkerhetstoken och stöd för X. 509-certifikat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 3ddc8c78bac47ed85266037341328585e3c7cb1c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825128"
---
# <a name="control-access-to-iot-hub"></a>Styra åtkomst till IoT Hub

I den här artikeln beskrivs alternativen för att skydda IoT-hubben. IoT Hub använder *behörigheter* för att bevilja åtkomst till varje IoT Hub-slutpunkt. Behörigheter begränsar åtkomsten till en IoT-hubb baserat på funktioner.

Den här artikeln beskriver:

* De olika behörigheter som du kan tilldela till en enhet eller backend-app för att få åtkomst till din IoT-hubb.
* Autentiseringsprocessen och de token som används för att verifiera behörigheter.
* Hur du begränsar autentiseringsuppgifter för att begränsa åtkomsten till vissa resurser.
* IoT Hub stöd för X. 509-certifikat.
* Anpassade mekanismer för enhetsautentisering som använder befintliga enhets identitets register eller autentiseringsscheman.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Du måste ha rätt behörighet för att få åtkomst till någon av IoT Hub slut punkterna. En enhet måste t. ex. innehålla en token som innehåller autentiseringsuppgifter, tillsammans med varje meddelande som skickas till IoT Hub.

## <a name="access-control-and-permissions"></a>Åtkomst kontroll och behörigheter

Du kan bevilja [behörigheter](#iot-hub-permissions) på följande sätt:

* **Principer för delad åtkomst för IoT Hub-nivå**. Principer för delad åtkomst kan ge en kombination av [behörigheter](#iot-hub-permissions). Du kan definiera principer i [Azure Portal](https://portal.azure.com), program mässigt med hjälp av [IoT Hub resursens REST-API: er](/rest/api/iothub/iothubresource)eller med hjälp av [AZ IoT Hub policy](/cli/azure/iot/hub/policy) cli. En nyligen skapad IoT Hub har följande standard principer:
  
  | Princip för delad åtkomst | Behörigheter |
  | -------------------- | ----------- |
  | iothubowner | Alla behörigheter |
  | tjänst | **ServiceConnect** -behörigheter |
  | enhet | **DeviceConnect** -behörigheter |
  | registryRead | **RegistryRead** -behörigheter |
  | registryReadWrite | Behörigheter för **RegistryRead** och **RegistryWrite** |

* **Säkerhets referenser per enhet**. Varje IoT Hub innehåller ett [identitets register](iot-hub-devguide-identity-registry.md) för varje enhet i identitets registret, du kan konfigurera säkerhets referenser som beviljar **DeviceConnect** -behörigheter som är begränsade till motsvarande enhets slut punkter.

Till exempel i en typisk IoT-lösning:

* Enhets hanterings komponenten använder *registryReadWrite* -principen.
* Event Processing-komponenten använder *tjänst* principen.
* Komponenten kör tids enhets affärs logik använder *tjänst* principen.
* Enskilda enheter ansluter med autentiseringsuppgifter som lagras i IoT Hub: s identitets register.

> [!NOTE]
> Se [behörigheter](#iot-hub-permissions) för detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub beviljar åtkomst till slutpunkter genom att verifiera en token mot principerna för delad åtkomst och säkerhetsautentiseringsuppgifter för identitetsregistret.

Säkerhets referenser, till exempel symmetriska nycklar, skickas aldrig över kabeln.

> [!NOTE]
> Azure IoT Hub Resource providern skyddas via din Azure-prenumeration, liksom alla leverantörer i [Azure Resource Manager](../azure-resource-manager/management/overview.md).

För ytterligare information om hur du skapar och använder säkerhetstoken, se [IoT Hub säkerhetstoken](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokoll information

Varje protokoll som stöds, till exempel MQTT, AMQP och HTTPS, transporterar token på olika sätt.

När du använder MQTT har CONNECT-paketet deviceId som ClientId `{iothubhostname}/{deviceId}` i fältet username och en SAS-token i fältet lösen ord. `{iothubhostname}` bör vara den fullständiga CName-filen för IoT Hub (till exempel contoso.azure-devices.net).

När du använder [AMQP](https://www.amqp.org/)stöder IoT Hub [sasl Plain](https://tools.ietf.org/html/rfc4616) och [AMQP-baserad säkerhet](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Om du använder AMQP-anspråksbaserad-säkerhet anger standarden hur du överför dessa tokens.

För SASL PLAIN kan **användar namnet** vara:

* `{policyName}@sas.root.{iothubName}` Om du använder token på IoT Hub-nivå.
* `{deviceId}@sas.{iothubname}` Om du använder token med enhets omfång.

I båda fallen innehåller fältet lösen ord token, enligt beskrivningen i [IoT Hub säkerhetstoken](iot-hub-devguide-security.md#security-tokens).

HTTPS implementerar autentisering genom att inkludera en giltig token i begärans huvud för **auktorisering** .

#### <a name="example"></a>Exempel

Användar namn (DeviceId är Skift läges känsligt): `iothubname.azure-devices.net/DeviceId`

Lösen ord (du kan skapa en SAS-token med kommandot CLI-tillägg [AZ IoT Hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)eller [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) : er genererar automatiskt token när de ansluter till tjänsten. I vissa fall stöder inte Azure IoT-SDK: erna alla protokoll eller autentiseringsmetoder.

### <a name="special-considerations-for-sasl-plain"></a>Särskilda överväganden för SASL PLAIN

När du använder SASL PLAIN med AMQP kan en klient som ansluter till en IoT Hub använda en enda token för varje TCP-anslutning. När token går ut kopplar TCP-anslutningen från tjänsten och utlöser en åter anslutning. Det här beteendet, även om det inte är problematiskt för en backend-app, är skadligt för en enhets app av följande skäl:

* Gatewayer ansluter vanligt vis på uppdrag av många enheter. När du använder SASL PLAIN måste de skapa en separat TCP-anslutning för varje enhet som ansluter till en IoT-hubb. Det här scenariot ökar avsevärt användningen av strömförsörjnings-och nätverks resurser och ökar svars tiden för varje enhets anslutning.

* Resurs begränsade enheter påverkas negativt av den ökade användningen av resurser för att återansluta när varje token upphör att gälla.

## <a name="scope-iot-hub-level-credentials"></a>Omfång på IoT Hub-nivå-autentiseringsuppgifter

Du kan använda säkerhets principer på IoT Hub-nivå genom att skapa token med en begränsad resurs-URI. Slut punkten för att skicka meddelanden från enhet till moln från en enhet är till exempel **/Devices/{deviceId}/Messages/Events**. Du kan också använda en princip för delad åtkomst på IoT Hub-nivå med **DeviceConnect** -behörigheter för att signera en token vars resourceURI är **/Devices/{deviceId}**. Den här metoden skapar en token som endast kan användas för att skicka meddelanden på uppdrag av enhets- **deviceId**.

Den här mekanismen liknar principen för [Event Hubs utgivare](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)och gör att du kan implementera anpassade autentiseringsmetoder.

## <a name="security-tokens"></a>Säkerhetstoken

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i kabeln. Dessutom är säkerhetstoken begränsade inom giltighets tid och omfång. [Azure IoT SDK](iot-hub-devguide-sdks.md) : er genererar automatiskt tokens utan att kräva någon speciell konfiguration. Vissa scenarier kräver att du genererar och använder säkerhetstoken direkt. Sådana scenarier är:

* Direkt användning av MQTT-, AMQP-eller HTTPS-ytor.

* Implementeringen av mönstret token service, som förklaras i [anpassad enhetsautentisering](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub tillåter också att enheter autentiseras med IoT Hub att använda [X. 509-certifikat](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktur för säkerhetstoken

Du använder säkerhetstoken för att ge tidsbegränsad åtkomst till enheter och tjänster till vissa funktioner i IoT Hub. För att få behörighet att ansluta till IoT Hub måste enheter och tjänster skicka säkerhetstoken signerade med antingen en delad åtkomst eller symmetrisk nyckel. Dessa nycklar lagras med en enhets identitet i identitets registret.

En token som signerats med en delad åtkomst nyckel beviljar åtkomst till alla funktioner som är associerade med behörigheterna för den delade åtkomst principen. En token som är signerad med enhets identitetens symmetriska nyckel beviljar endast **DeviceConnect** -behörigheten för tillhör ande enhets identitet

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| signatur |En HMAC-SHA256 signatur sträng i formatet: `{URL-encoded-resourceURI} + "\n" + expiry` . **Viktigt**: nyckeln avkodas från base64 och används som nyckel för att utföra den HMAC-SHA256 beräkningen. |
| ResourceURI |URI-prefix (efter segment) för de slut punkter som kan nås med denna token, med början på värd namnet för IoT Hub (inget protokoll). Till exempel `myHub.azure-devices.net/devices/device1` |
| förfallo |UTF8-strängar för antalet sekunder sedan 00:00:00 UTC på 1 januari 1970. |
| {URL-kodad – resourceURI} |Gemen URL – kodning för den nedre fall resurs-URI: n |
| PolicyName |Namnet på den princip för delad åtkomst som denna token refererar till. Frånvarande om token refererar till enhets register uppgifter. |

**Anmärkning om prefix**: URI-prefixet beräknas av segment och inte av-tecknen. Till exempel `/a/b` är ett prefix för `/a/b/c` men inte för `/a/bc` .

Följande Node.js-kodfragment visar en funktion med namnet **generateSasToken** som beräknar token från indata `resourceUri, signingKey, policyName, expiresInMins` . I nästa avsnitt beskrivs hur du initierar de olika indatana för de olika användnings fallen för token.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Motsvarande python-kod för att generera en säkerhetstoken är som jämförelse:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Funktionen i C# för att skapa en säkerhetstoken är:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

För java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Eftersom giltighets tiden för token har verifierats på IoT Hub datorer, måste driften på datorns klocka som genererar token vara minimal.

### <a name="use-sas-tokens-in-a-device-app"></a>Använda SAS-token i en enhets app

Det finns två sätt att hämta **DeviceConnect** -behörigheter med IoT Hub med säkerhetstoken: Använd en [symmetrisk enhets nyckel från identitets registret](#use-a-symmetric-key-in-the-identity-registry)eller Använd en [delad åtkomst nyckel](#use-a-shared-access-policy).

Kom ihåg att alla funktioner som är tillgängliga från enheter exponeras genom design på slut punkter med prefix `/devices/{deviceId}` .

> [!IMPORTANT]
> Det enda sätt som IoT Hub autentiserar en speciell enhet med hjälp av den symmetriska nyckeln för enhets identiteten. I de fall då en princip för delad åtkomst används för att komma åt enhets funktioner måste lösningen beakta komponenten som utfärdar säkerhetstoken som en betrodd del komponent.

Slut punkterna för enhets slut punkter är (oberoende av protokollet):

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Skicka meddelanden från enheten till molnet. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ta emot meddelanden från molnet till enheten. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Använd en symmetrisk nyckel i identitets registret

När du använder en enhets identitets symmetriska nyckel för att generera en token `skn` utelämnas policyName ()-elementet för token.

En token som har skapats för åtkomst till alla enhets funktioner ska till exempel ha följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* signerings nyckel: symmetrisk nyckel för `{device id}` identiteten
* inget princip namn,
* förfallo tid.

Ett exempel som använder föregående Node.js-funktion är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, blir:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Det går att skapa en SAS-token med kommandot CLI-tillägg [AZ IoT Hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)eller [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Använd en princip för delad åtkomst

När du skapar en token från en princip för delad åtkomst anger du `skn` namnet på principen i fältet. Den här principen måste ge **DeviceConnect** -behörighet.

De två huvud scenarierna för att använda principer för delad åtkomst för att komma åt enhets funktioner är:

* [moln protokoll-gatewayer](iot-hub-devguide-endpoints.md),
* [token Services](iot-hub-devguide-security.md#custom-device-and-module-authentication) som används för att implementera anpassade autentiseringsscheman.

Eftersom principen för delad åtkomst kan ge åtkomst till att ansluta som vilken enhet som helst, är det viktigt att använda rätt resurs-URI när du skapar säkerhetstoken. Den här inställningen är särskilt viktig för token Services, som måste omfånget till en speciell enhet med hjälp av resurs-URI: n. Den här punkten är mindre relevant för protokoll-gatewayer eftersom de redan är underrättad trafik för alla enheter.

Till exempel skulle en token-tjänst som använder den förskapade principen för delad åtkomst som kallas **enhet** skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* signerings nyckel: en av nycklarna i `device` principen.
* princip namn: `device` ,
* förfallo tid.

Ett exempel som använder föregående Node.js-funktion är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, blir:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

En protokoll-Gateway kan använda samma token för alla enheter som helt enkelt anger resurs-URI: n till `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänst komponenter

Tjänst komponenter kan bara skapa säkerhetstoken med hjälp av principer för delad åtkomst som beviljar lämpliga behörigheter som förklaras tidigare.

Här är tjänst funktionerna som visas på slut punkterna:

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices` |Skapa, uppdatera, hämta och ta bort enhets identiteter. |
| `{iot hub host name}/messages/events` |Ta emot meddelanden från enheten till molnet. |
| `{iot hub host name}/servicebound/feedback` |Få feedback om meddelanden från molnet till enheten. |
| `{iot hub host name}/devicebound` |Skicka meddelanden från moln till enhet. |

Till exempel skulle en tjänst som genererar med den i förväg skapade principen för delad åtkomst som heter **registryRead** skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices` ,
* signerings nyckel: en av nycklarna i `registryRead` principen.
* princip namn: `registryRead` ,
* förfallo tid.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som skulle ge åtkomst för att läsa alla enhets identiteter, blir:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>X. 509-certifikat som stöds

Du kan använda valfritt X. 509-certifikat för att autentisera en enhet med IoT Hub genom att ladda upp antingen ett tumavtryck för certifikatet eller en certifikat utfärdare (CA) till Azure IoT Hub. Autentisering med Certificate tumavtrycken verifierar att det presenterade tumavtrycket matchar det konfigurerade tumavtrycket. Autentisering med certifikat utfärdare verifierar certifikat kedjan. Oavsett hur kräver TLS-handskakning att enheten har ett giltigt certifikat och en privat nyckel. Se TLS-specifikationen för mer information, till exempel: [RFC 5246 – Transport Layer Security (TLS)-protokoll Version 1,2](https://tools.ietf.org/html/rfc5246/).

Certifikat som stöds är:

* **Ett befintligt X. 509-certifikat**. En enhet har kanske redan ett X. 509-certifikat som är associerat med det. Enheten kan använda det här certifikatet för att autentisera med IoT Hub. Fungerar med antingen tumavtryck eller CA-autentisering. 

* **Ca-signerat X. 509-certifikat**. Om du vill identifiera en enhet och autentisera den med IoT Hub kan du använda ett X. 509-certifikat som skapas och signeras av en certifikat utfärdare (CA). Fungerar med antingen tumavtryck eller CA-autentisering.

* **Ett självgenererat och självsignerat X-509-certifikat**. En enhets tillverkare eller en intern distributions enhet kan generera dessa certifikat och lagra motsvarande privata nyckel (och certifikat) på enheten. Du kan använda verktyg som [openssl](https://www.openssl.org/) och [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) -verktyget för det här ändamålet. Fungerar bara med tumavtryck-autentisering.

En enhet kan antingen använda ett X. 509-certifikat eller en säkerhetstoken för autentisering, men inte båda. Med X. 509-certifikatautentisering kontrollerar du att du har en strategi för att hantera certifikat förnyelse när ett befintligt certifikat upphör att gälla.

Följande funktioner stöds inte för enheter som använder X. 509 CA-autentisering:

* HTTPS, MQTT över WebSockets och AMQP över WebSockets-protokoll.
* Fil överföringar (alla protokoll).

Mer information om autentisering med hjälp av certifikat utfärdare finns i [enhets autentisering med X. 509 CA-certifikat](iot-hub-x509ca-overview.md). Information om hur du överför och verifierar en certifikat utfärdare med IoT Hub finns i [Konfigurera X. 509 säkerhet i Azure IoT Hub](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrera ett X. 509-certifikat för en enhet

[Azure IoT service SDK för C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (version 1.0.8 +) stöder registrering av enheter som använder ett X. 509-certifikat för autentisering. Andra API: er, till exempel import/export av enheter, stöder också X. 509-certifikat.

Du kan också använda kommandot CLI-tillägg [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) för att konfigurera X. 509-certifikat för enheter.

### <a name="c-support"></a>C- \# Support

**RegistryManager** -klassen ger ett programmerings sätt att registrera en enhet. I synnerhet kan du med metoderna **AddDeviceAsync** och **UpdateDeviceAsync** registrera och uppdatera en enhet i IoT Hub identitets registret. Dessa två metoder tar en **enhets** instans som inmatad. **Enhets** klassen innehåller en egenskap för **autentisering** som gör att du kan ange primär och sekundär X. 509-certifikat tumavtrycken. Tumavtrycket representerar en SHA256-hash av X. 509-certifikatet (lagrad med binär DER-kodning). Du kan välja att ange ett primärt tumavtryck eller ett sekundärt tumavtryck eller båda. Primära och sekundära tumavtrycken stöds för hantering av certifikat förnyelse scenarier.

Här är ett exempel på ett C- \# kodfragment för att registrera en enhet med ett X. 509-certifikat tumavtryck:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Använd ett X. 509-certifikat under kör tids åtgärder

[Azure IoT-enhetens SDK för .net](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (version 1.0.11 +) stöder användningen av X. 509-certifikat.

### <a name="c-support"></a>C- \# Support

Klassen **DeviceAuthenticationWithX509Certificate** har stöd för att skapa **DeviceClient** -instanser med ett X. 509-certifikat. X. 509-certifikatet måste finnas i PFX-formatet (kallas även PKCS #12) som innehåller den privata nyckeln.

Här är ett exempel kods tycke:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Anpassad autentisering av enhet och modul

Du kan använda IoT Hub [identitets registret](iot-hub-devguide-identity-registry.md) för att konfigurera säkerhets referenser per enhet/modul och åtkomst kontroll med hjälp av [tokens](iot-hub-devguide-security.md#security-tokens). Om en IoT-lösning redan har ett anpassat identitets register och/eller autentiseringsschema, kan du överväga att skapa en *token-tjänst* för att integrera infrastrukturen med IoT Hub. På så sätt kan du använda andra IoT-funktioner i lösningen.

En token service är en anpassad moln tjänst. Den använder en IoT Hub *princip för delad åtkomst* med **DeviceConnect** -eller **ModuleConnect** -behörigheter för att skapa *enhets omfång* eller *modulbaserade* token. Dessa token gör det möjligt för en enhet och modul att ansluta till din IoT-hubb.

![Steg i mönstret för token-tjänsten](./media/iot-hub-devguide-security/tokenservice.png)

Här följer huvud stegen i mönstret token service:

1. Skapa en IoT Hub princip för delad åtkomst med **DeviceConnect** -eller **ModuleConnect** -behörigheter för din IoT Hub. Du kan skapa den här principen i [Azure Portal](https://portal.azure.com) eller program mässigt. Token-tjänsten använder den här principen för att signera de tokens som skapas.

2. När en enhet/modul behöver åtkomst till din IoT-hubb begär den en signerad token från din token-tjänst. Enheten kan autentisera med ditt anpassade identitets register/autentiseringsschema för att fastställa enhets-/modulens identitet som token-tjänsten använder för att skapa token.

3. Token-tjänsten returnerar en token. Token skapas med hjälp av `/devices/{deviceId}` eller `/devices/{deviceId}/module/{moduleId}` som `resourceURI` , med `deviceId` som den enhet som autentiseras eller `moduleId` som modulen autentiseras. Token-tjänsten använder principen för delad åtkomst för att skapa token.

4. Enheten/modulen använder token direkt med IoT Hub.

> [!NOTE]
> Du kan använda .NET-klassen [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) eller Java-klassen [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) för att skapa en token i din token-tjänst.

Token-tjänsten kan ange förfallo datum för token efter behov. När token går ut, severrar IoT Hub enhets-/modul anslutningen. Sedan måste enheten/modulen begära en ny token från token-tjänsten. En kort förfallo tid ökar belastningen på både enheten/modulen och token-tjänsten.

För att en enhet/modul ska kunna ansluta till navet måste du ändå lägga till den i IoT Hub identitets registret, även om den använder en token och inte en nyckel för att ansluta. Därför kan du fortsätta att använda åtkomst kontroll per enhet/per modul genom att aktivera eller inaktivera identiteter för enhet/modul i [identitets registret](iot-hub-devguide-identity-registry.md). Den här metoden minskar riskerna med att använda token med långa utgångs tider.

### <a name="comparison-with-a-custom-gateway"></a>Jämförelse med en anpassad Gateway

Mönstret token service är det rekommenderade sättet att implementera ett anpassat identitets register/autentiseringsschema med IoT Hub. Det här mönstret rekommenderas eftersom IoT Hub fortsätter att hantera merparten av lösnings trafiken. Men om det anpassade autentiseringsschemat är sammanflätat med protokollet kan du behöva en *anpassad Gateway* för att bearbeta all trafik. Ett exempel på ett sådant scenario är att använda [Transport Layer Security (TLS) och i förväg delade nycklar (PSKs)](https://tools.ietf.org/html/rfc4279). Mer information finns i artikeln om [protokoll-Gateway](iot-hub-protocol-gateway.md) .

## <a name="reference-topics"></a>Referens ämnen:

Följande referens avsnitt innehåller mer information om hur du styr åtkomsten till din IoT-hubb.

## <a name="iot-hub-permissions"></a>IoT Hub behörigheter

I följande tabell visas de behörigheter som du kan använda för att kontrol lera åtkomsten till IoT Hub.

| Behörighet | Obs! |
| --- | --- |
| **RegistryRead** |Ger Läs behörighet till identitets registret. Mer information finns i [identitets registret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av Server dels moln tjänster. |
| **RegistryReadWrite** |Ger Läs-och Skriv behörighet till identitets registret. Mer information finns i [identitets registret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av Server dels moln tjänster. |
| **ServiceConnect** |Beviljar åtkomst till moln tjänst – riktad kommunikation och övervakning av slut punkter. <br/>Ger behörighet att ta emot meddelanden från enheten till molnet, skicka meddelanden från moln till enhet och hämta motsvarande leverans bekräftelser. <br/>Ger behörighet att hämta leverans bekräftelser för fil överföringar. <br/>Ger behörighet att komma åt dubbla för att uppdatera Taggar och önskade egenskaper, Hämta rapporterade egenskaper och köra frågor. <br/>Den här behörigheten används av Server dels moln tjänster. |
| **DeviceConnect** |Ger åtkomst till enhets slut punkter. <br/>Ger behörighet att skicka meddelanden från enheten till molnet och ta emot meddelanden från molnet till enheten. <br/>Ger behörighet att utföra fil uppladdning från en enhet. <br/>Ger behörighet att ta emot enhets dubbla önskade egenskaps meddelanden och uppdatera enhetens dubbla rapporterade egenskaper. <br/>Ger behörighet att utföra fil överföringar. <br/>Den här behörigheten används av enheter. |

## <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsnings beteenden som gäller för tjänsten IoT Hub.

* [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er listar de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar IoT Hub med

* [IoT Hub frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråket som du kan använda för att hämta information från IoT Hub om din enhets dubblare och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

* [RFC 5246 – Transport Layer Security (TLS)-protokoll Version 1,2](https://tools.ietf.org/html/rfc5246/) innehåller mer information om TLS-autentisering.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kontrollerar åtkomst IoT Hub kan du vara intresse rad av följande avsnitt för IoT Hub-utvecklarverktyg:

* [Använd enheten för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub Självstudier:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Skicka meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Så här bearbetar du IoT Hub enhet-till-moln-meddelanden](tutorial-routing.md)
