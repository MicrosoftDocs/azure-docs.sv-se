---
title: Förstå Azure IoT Hub säkerhets | Microsoft Docs
description: Utvecklarguide – hur du styr åtkomsten till IoT Hub för enhetsappar och backend-appar. Innehåller information om säkerhetstoken och stöd för X.509-certifikat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 7f919069005e8fcb813baf2521c8cb20cffafc88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870363"
---
# <a name="control-access-to-iot-hub"></a>Styra åtkomst till IoT Hub

I den här artikeln beskrivs alternativen för att skydda din IoT-hubb. IoT Hub använder behörigheter *för att* bevilja åtkomst till varje IoT Hub-slutpunkt. Behörigheter begränsar åtkomsten till en IoT-hubb baserat på funktioner.

Den här artikeln introducerar:

* De olika behörigheter som du kan bevilja till en enhet eller backend-app för att få åtkomst till din IoT-hubb.
* Autentiseringsprocessen och de token som används för att verifiera behörigheter.
* Så här begränsar du autentiseringsuppgifter för att begränsa åtkomsten till specifika resurser.
* IoT Hub stöd för X.509-certifikat.
* Anpassade metoder för enhetsautentisering som använder befintliga enhetsidentitetsregister eller autentiseringsscheman.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Du måste ha rätt behörigheter för att komma åt IoT Hub slutpunkter. En enhet måste till exempel innehålla en token som innehåller säkerhetsautentiseringsuppgifter tillsammans med varje meddelande som den skickar till IoT Hub.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan bevilja [behörigheter](#iot-hub-permissions) på följande sätt:

* **Principer för delad åtkomst på IoT-hubbnivå**. Principer för delad åtkomst kan bevilja valfri kombination av [behörigheter.](#iot-hub-permissions) Du kan definiera principer i [Azure Portal](https://portal.azure.com), programmässigt med hjälp [av REST-API:IoT Hub](/rest/api/iothub/iothubresource)för resurs eller med [hjälp av cli för az iot hub-princip.](/cli/azure/iot/hub/policy) En nyligen skapad IoT-hubb har följande standardprinciper:
  
  | Princip för delad åtkomst | Behörigheter |
  | -------------------- | ----------- |
  | iothubowner | Alla behörigheter |
  | tjänst | **ServiceConnect-behörigheter** |
  | enhet | **DeviceConnect-behörigheter** |
  | registryRead | **RegistryRead-behörigheter** |
  | registryReadWrite | **RegistryRead-** **och RegistryWrite-behörigheter** |

* **Säkerhetsautentiseringsuppgifter per enhet.** Varje IoT Hub innehåller [ett](iot-hub-devguide-identity-registry.md) identitetsregister För varje enhet i det här identitetsregistret kan du konfigurera säkerhetsautentiseringsuppgifter som beviljar **DeviceConnect-behörigheter** som är begränsade till motsvarande enhetsslutpunkter.

I en typisk IoT-lösning kan du till exempel:

* Komponenten för enhetshantering använder *registryReadWrite-principen.*
* Komponenten för händelseprocessorn använder *tjänstprincipen.*
* Affärslogikkomponenten för körningsenheten använder *tjänstprincipen.*
* Enskilda enheter ansluter med autentiseringsuppgifter som lagras i IoT-hubbens identitetsregister.

> [!NOTE]
> Se [behörigheter](#iot-hub-permissions) för detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub beviljar åtkomst till slutpunkter genom att verifiera en token mot principerna för delad åtkomst och säkerhetsautentiseringsuppgifter för identitetsregistret.

Säkerhetsautentiseringsuppgifter, till exempel symmetriska nycklar, skickas aldrig via kabel.

> [!NOTE]
> Resursprovidern Azure IoT Hub skyddas via din Azure-prenumeration, precis som alla leverantörer i [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Mer information om hur du skapar och använder säkerhetstoken finns i [IoT Hub säkerhetstoken.](iot-hub-devguide-security.md#security-tokens)

### <a name="protocol-specifics"></a>Protokollspecifik information

Varje protokoll som stöds, till exempel MQTT, AMQP och HTTPS, transporterar token på olika sätt.

När du använder MQTT har CONNECT-paketet deviceId som ClientId i fältet Användarnamn och `{iothubhostname}/{deviceId}` en SAS-token i fältet Lösenord. `{iothubhostname}` bör vara det fullständiga CName för IoT-hubben (till exempel contoso.azure-devices.net).

När du [använder AMQP](https://www.amqp.org/)IoT Hub stöd [SASL PLAIN](https://tools.ietf.org/html/rfc4616) [och AMQP-anspråksbaserad säkerhet.](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

Om du använder AMQP-anspråksbaserad säkerhet anger standarden hur dessa token ska överföras.

Till SASL PLAIN **användarnamnet** vara:

* `{policyName}@sas.root.{iothubName}` om du använder token på IoT-hubbnivå.
* `{deviceId}@sas.{iothubname}` om du använder token med enhetsomfång.

I båda fallen innehåller fältet lösenord token, enligt beskrivningen i IoT Hub [säkerhetstoken.](iot-hub-devguide-security.md#security-tokens)

HTTPS implementerar autentisering genom att  inkludera en giltig token i rubriken Auktoriseringsbegäran.

#### <a name="example"></a>Exempel

Användarnamn (DeviceId är case-sensitive): `iothubname.azure-devices.net/DeviceId`

Lösenord (du kan generera en SAS-token med CLI-tilläggskommandot [az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token), [eller Azure IoT Tools för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT-SDK:erna](iot-hub-devguide-sdks.md) genererar automatiskt token vid anslutning till tjänsten. I vissa fall har Azure IoT-SDK:erna inte stöd för alla protokoll eller alla autentiseringsmetoder.

### <a name="special-considerations-for-sasl-plain"></a>Särskilda överväganden för SASL PLAIN

När du SASL PLAIN med AMQP kan en klient som ansluter till en IoT-hubb använda en enda token för varje TCP-anslutning. När token upphör att gälla kopplas TCP-anslutningen från tjänsten och utlöser en återanslutning. Det här beteendet, även om det inte är problematiskt för en serverapp, är skadligt för en enhetsapp av följande skäl:

* Gatewayer ansluter vanligtvis för många enheters räkning. När du SASL PLAIN måste de skapa en distinkt TCP-anslutning för varje enhet som ansluter till en IoT-hubb. Det här scenariot ökar avsevärt förbrukningen av ström- och nätverksresurser och ökar svarstiden för varje enhetsanslutning.

* Resursbegränsade enheter påverkas negativt av den ökade användningen av resurser som ska återanslutas efter varje tokens upphörande.

## <a name="scope-iot-hub-level-credentials"></a>Omfång för autentiseringsuppgifter på IoT-hubbnivå

Du kan begränsa IoT-säkerhetsprinciper på hubbnivå genom att skapa token med en begränsad resurs-URI. Slutpunkten för att skicka "enhet till molnet"-meddelanden från en enhet är till exempel **/devices/{deviceId}/messages/events**. Du kan också använda en princip för delad åtkomst på IoT-hubbnivå med **DeviceConnect-behörigheter** för att signera en token vars resurs-URI är **/devices/{deviceId}**. Den här metoden skapar en token som bara kan användas för att skicka meddelanden för enhetens **deviceId.**

Den här mekanismen liknar den [Event Hubs utgivarprincipen](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)och gör att du kan implementera anpassade autentiseringsmetoder.

## <a name="security-tokens"></a>Säkerhetstoken

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar via kabel. Dessutom är säkerhetstoken begränsade i tidsenlighet och omfattning. [Azure IoT-SDK:er](iot-hub-devguide-sdks.md) genererar automatiskt token utan att någon särskild konfiguration krävs. Vissa scenarier kräver att du genererar och använder säkerhetstoken direkt. Exempel på sådana scenarier är:

* Direkt användning av MQTT-, AMQP- eller HTTPS-ytan.

* Implementeringen av tokentjänstmönstret, enligt förklaringen i [Anpassad enhetsautentisering](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub tillåter också att enheter autentiseras med hjälp IoT Hub [X.509-certifikat.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Säkerhetstokenstruktur

Du använder säkerhetstoken för att ge tidsbe gräns för åtkomst till enheter och tjänster till specifika funktioner i IoT Hub. För att få behörighet att ansluta till IoT Hub måste enheter och tjänster skicka säkerhetstoken signerade med antingen en delad eller symmetrisk nyckel. Dessa nycklar lagras med en enhetsidentitet i identitetsregistret.

En token som signerats med en delad åtkomstnyckel ger åtkomst till alla funktioner som är associerade med behörigheter för principen för delad åtkomst. En token som signerats med en enhetsidentitets symmetriska nyckel ger endast **DeviceConnect-behörighet** för den associerade enhetsidentiteten.

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signature} |En HMAC-SHA256-signatursträng i formuläret: `{URL-encoded-resourceURI} + "\n" + expiry` . **Viktigt!** Nyckeln avkodas från base64 och används som nyckel för att utföra HMAC-SHA256-beräkningen. |
| {resourceURI} |URI-prefix (efter segment) för de slutpunkter som kan nås med denna token, från och med värdnamnet för IoT-hubben (inget protokoll). Till exempel `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-strängar för antal sekunder sedan epoken 00:00:00 UTC den 1 januari 1970. |
| {URL-encoded-resourceURI} |Gemen URL-kodning av resurs-URI med gemener |
| {policyName} |Namnet på principen för delad åtkomst som denna token refererar till. Saknas om token refererar till autentiseringsuppgifter för enhetsregistret. |

**Obs! Prefixet**: URI-prefixet beräknas efter segment och inte efter tecken. Till exempel `/a/b` är ett prefix för men inte för `/a/b/c` `/a/bc` .

Följande kodfragment Node.js en funktion med namnet **generateSasToken** som beräknar token från indata `resourceUri, signingKey, policyName, expiresInMins` . I nästa avsnitt beskrivs hur du initierar de olika indata för olika tokenanvändningsfall.

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

Som jämförelse är motsvarande Python-kod för att generera en säkerhetstoken:

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

Funktionen i C# för att generera en säkerhetstoken är:

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

För Java:
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
> Eftersom tokens giltighet verifieras på IoT Hub datorer måste fördrstiden på klockan för den dator som genererar token vara minimal.

### <a name="use-sas-tokens-in-a-device-app"></a>Använda SAS-token i en enhetsapp

Det finns två sätt att hämta **DeviceConnect-behörigheter** med IoT Hub med säkerhetstoken: använda en [symmetrisk](#use-a-symmetric-key-in-the-identity-registry)enhetsnyckel från identitetsregistret eller använda en delad [åtkomstnyckel](#use-a-shared-access-policy).

Kom ihåg att alla funktioner som är tillgängliga från enheter exponeras som design på slutpunkter med prefixet `/devices/{deviceId}` .

> [!IMPORTANT]
> Det enda sättet IoT Hub autentiserar en specifik enhet är att använda den symmetriska enhetsidentitetsnyckeln. Om en princip för delad åtkomst används för att få åtkomst till enhetsfunktioner måste lösningen betrakta komponenten som utfärdar säkerhetstoken som en betrodd delkomponent.

De enhetsriktade slutpunkterna är (oavsett protokoll):

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Skicka meddelanden från enhet till moln. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ta emot meddelanden från moln till enhet. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Använda en symmetrisk nyckel i identitetsregistret

När du använder en enhetsidentitets symmetriska nyckel för att generera en token utelämnas elementet policyName ( `skn` ) för token.

Till exempel bör en token som skapats för att få åtkomst till alla enhetsfunktioner ha följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* signeringsnyckel: valfri symmetrisk nyckel för `{device id}` identiteten,
* inget principnamn,
* valfri förfallotid.

Ett exempel som använder den Node.js funktionen är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, blir:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Det går att generera en [SAS-token med CLI-tilläggskommandot az iot hub generate-sas-token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token), eller [Azure IoT Tools för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Använda en princip för delad åtkomst

När du skapar en token från en princip för delad åtkomst `skn` anger du namnet på principen i fältet. Den här principen måste ge **DeviceConnect-behörighet.**

De två huvudscenarierna för att använda principer för delad åtkomst för att få åtkomst till enhetsfunktioner är:

* [molnprotokollgatewayer](iot-hub-devguide-endpoints.md),
* [tokentjänster som](iot-hub-devguide-security.md#custom-device-and-module-authentication) används för att implementera anpassade autentiseringsscheman.

Eftersom principen för delad åtkomst potentiellt kan bevilja åtkomst för att ansluta som vilken enhet som helst, är det viktigt att använda rätt resurs-URI när du skapar säkerhetstoken. Den här inställningen är särskilt viktig för tokentjänster som måste begränsa token till en specifik enhet med hjälp av resurs-URI:t. Den här punkten är mindre relevant för protokollgatewayer eftersom de redan medlar trafik för alla enheter.

Till exempel skulle en tokentjänst med den i  förväg skapade principen för delad åtkomst som kallas enhet skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* signeringsnyckel: en av nycklarna för `device` principen,
* principnamn: `device` ,
* valfri förfallotid.

Ett exempel som använder den Node.js funktionen är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, blir:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

En protokollgateway kan använda samma token för alla enheter genom att helt enkelt ange resurs-URI:t till `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänstkomponenter

Tjänstkomponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som beviljar lämpliga behörigheter enligt tidigare förklaring.

Här är de tjänstfunktioner som exponeras på slutpunkterna:

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices` |Skapa, uppdatera, hämta och ta bort enhetsidentiteter. |
| `{iot hub host name}/messages/events` |Ta emot meddelanden från enhet till moln. |
| `{iot hub host name}/servicebound/feedback` |Få feedback för meddelanden från moln till enhet. |
| `{iot hub host name}/devicebound` |Skicka meddelanden från moln till enhet. |

Till exempel skulle en tjänst som genererar med hjälp av den i förväg skapade principen för delad åtkomst med namnet **registryRead** skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices` ,
* signeringsnyckel: en av `registryRead` principnycklarna,
* principnamn: `registryRead` ,
* valfri förfallotid.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som skulle ge åtkomst till att läsa alla enhetsidentiteter, skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>X.509-certifikat som stöds

Du kan använda val annat X.509-certifikat för att autentisera en enhet med IoT Hub genom att antingen ladda upp ett tumavtryck för certifikatet eller en certifikatutfärdare (CA) till Azure IoT Hub. Autentisering med tumavtryck för certifikat verifierar att det visade tumavtrycket matchar det konfigurerade tumavtrycket. Autentisering med certifikatutfärdare verifierar certifikatkedjan. Oavsett vilket kräver TLS-handskakning att enheten har ett giltigt certifikat och en privat nyckel. Se TLS-specifikationen för mer information, till exempel: [RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/).

Certifikat som stöds är:

* **Ett befintligt X.509-certifikat**. En enhet kanske redan har ett kopplat X.509-certifikat. Enheten kan använda det här certifikatet för att autentisera med IoT Hub. Fungerar med antingen tumavtryck eller CA-autentisering. 

* **CA-signerat X.509-certifikat**. Om du vill identifiera en enhet och autentisera den med IoT Hub kan du använda ett X.509-certifikat som genererats och signerats av en certifikatutfärdare (CA). Fungerar med antingen tumavtryck eller CA-autentisering.

* **Ett självgenererat och själv signerat X-509-certifikat.** En enhetstillverkare eller en internt distribuerare kan generera dessa certifikat och lagra motsvarande privata nyckel (och certifikat) på enheten. Du kan använda verktyg som [OpenSSL och](https://www.openssl.org/) [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) för detta ändamål. Fungerar bara med tumavtrycksautentisering.

En enhet kan antingen använda ett X.509-certifikat eller en säkerhetstoken för autentisering, men inte båda. Med X.509-certifikatautentisering kontrollerar du att du har en strategi för att hantera certifikatsverifiering när ett befintligt certifikat upphör att gälla.

Följande funktioner för enheter som använder X.509-certifikatutfärdarautentisering (CA) är ännu inte allmänt tillgängliga och förhandsgranskningsläget [måste vara aktiverat:](iot-hub-preview-mode.md)

* HTTPS, MQTT över WebSockets och AMQP över WebSockets-protokoll.
* Filuppladdningar (alla protokoll).

Mer information om autentisering med certifikatutfärdare finns i [Enhetsautentisering med X.509 CA-certifikat.](iot-hub-x509ca-overview.md) Information om hur du laddar upp och verifierar en certifikatutfärdare med din IoT-hubb finns i Konfigurera [X.509-säkerhet i Din Azure IoT Hub.](iot-hub-security-x509-get-started.md)

### <a name="register-an-x509-certificate-for-a-device"></a>Registrera ett X.509-certifikat för en enhet

[Azure IoT Service SDK för C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (version 1.0.8+) stöder registrering av en enhet som använder ett X.509-certifikat för autentisering. Andra API:er, till exempel import/export av enheter, stöder också X.509-certifikat.

Du kan också använda CLI-tilläggskommandot [az iot hub device-identity för](/cli/azure/iot/hub/device-identity) att konfigurera X.509-certifikat för enheter.

### <a name="c-support"></a>\#C-stöd

**RegistryManager-klassen** är ett programmatiskt sätt att registrera en enhet. I synnerhet gör **metoderna AddDeviceAsync** och **UpdateDeviceAsync** att du kan registrera och uppdatera en enhet i IoT Hub identitetsregistret. Dessa två metoder använder en **enhetsinstans** som indata. Klassen **Enhet** innehåller en **autentiseringsegenskap** som gör att du kan ange primära och sekundära X.509-certifikattumavtryck. Tumavtrycket representerar en SHA256-hash för X.509-certifikatet (lagras med binär DER-kodning). Du kan välja att ange ett primärt tumavtryck eller ett sekundärt tumavtryck eller båda. Primära och sekundära tumavtryck stöds för att hantera scenarier för certifikatsrover.

Här är ett \# C-exempelkodfragment för att registrera en enhet med ett tumavtryck för X.509-certifikat:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Använda ett X.509-certifikat under körningsåtgärder

[Sdk för Azure IoT-enheter för .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (version 1.0.11+) stöder användning av X.509-certifikat.

### <a name="c-support"></a>\#C-stöd

Klassen **DeviceAuthenticationWithX509Certificate** stöder skapandet av **DeviceClient-instanser** med hjälp av ett X.509-certifikat. X.509-certifikatet måste vara i PFX-formatet (kallas PKCS #12) som innehåller den privata nyckeln.

Här är ett exempel på ett kodfragment:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Anpassad enhets- och modulautentisering

Du kan använda IoT Hub [identitetsregistret för att](iot-hub-devguide-identity-registry.md) konfigurera säkerhetsautentiseringsuppgifter per enhet/modul och åtkomstkontroll med hjälp av [token](iot-hub-devguide-security.md#security-tokens). Om en IoT-lösning redan har ett anpassat identitetsregister och/eller autentiseringsschema bör du överväga att skapa en *tokentjänst* för att integrera den här infrastrukturen med IoT Hub. På så sätt kan du använda andra IoT-funktioner i din lösning.

En tokentjänst är en anpassad molntjänst. Den använder en IoT Hub *princip* för delad åtkomst med **DeviceConnect-** eller **ModuleConnect-behörigheter** för att skapa enhetsbeomfångs- eller *modulomfångsbaserade* token.  Med dessa token kan en enhet och modul ansluta till din IoT-hubb.

![Steg för tokentjänstmönstret](./media/iot-hub-devguide-security/tokenservice.png)

Här är de viktigaste stegen i tokentjänstmönstret:

1. Skapa en IoT Hub princip för delad åtkomst med **DeviceConnect-** eller **ModuleConnect-behörigheter** för din IoT-hubb. Du kan skapa den här principen [i Azure Portal](https://portal.azure.com) eller programmässigt. Tokentjänsten använder den här principen för att signera de token som skapas.

2. När en enhet/modul behöver åtkomst till din IoT-hubb begär den en signerad token från din tokentjänst. Enheten kan autentisera med ditt anpassade identitetsregister/autentiseringsschema för att fastställa den enhets-/modulidentitet som tokentjänsten använder för att skapa token.

3. Tokentjänsten returnerar en token. Token skapas med hjälp av `/devices/{deviceId}` eller som , med som den enhet som `/devices/{deviceId}/module/{moduleId}` `resourceURI` `deviceId` autentiseras eller som den modul som `moduleId` autentiseras. Tokentjänsten använder principen för delad åtkomst för att konstruera token.

4. Enheten/modulen använder token direkt med IoT-hubben.

> [!NOTE]
> Du kan använda .NET-klassen [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) eller Java-klassen [IotHubServiceSasToken för](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) att skapa en token i din tokentjänst.

Tokentjänsten kan ställa in tokenförfallotid som du vill. När token upphör att gälla tar IoT-hubben över enhets-/modulanslutningen. Sedan måste enheten/modulen begära en ny token från tokentjänsten. En kort förfallotid ökar belastningen på både enheten/modulen och tokentjänsten.

För att en enhet/modul ska kunna ansluta till hubben måste du fortfarande lägga till den i IoT Hub-identitetsregistret– även om den använder en token och inte en nyckel för att ansluta. Därför kan du fortsätta att använda åtkomstkontroll per enhet/per modul genom att aktivera eller inaktivera enhets-/modulidentiteter i [identitetsregistret](iot-hub-devguide-identity-registry.md). Den här metoden minskar riskerna med att använda token med långa förfallotider.

### <a name="comparison-with-a-custom-gateway"></a>Jämförelse med en anpassad gateway

Mönstret för tokentjänsten är det rekommenderade sättet att implementera ett anpassat identitetsregister/autentiseringsschema med IoT Hub. Det här mönstret rekommenderas eftersom IoT Hub fortsätter att hantera merparten av lösningstrafiken. Men om det anpassade autentiseringsschemat är så sammanflätat med protokollet kan du behöva en anpassad *gateway* för att bearbeta all trafik. Ett exempel på ett sådant scenario är [att använda Transport Layer Security (TLS) och i förväg delade nycklar (PSK)](https://tools.ietf.org/html/rfc4279). Mer information finns i artikeln [protokollgateway.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Referensavsnitt:

Följande referensavsnitt innehåller mer information om hur du styr åtkomsten till din IoT-hubb.

## <a name="iot-hub-permissions"></a>IoT Hub behörigheter

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till din IoT-hubb.

| Behörighet | Kommentarer |
| --- | --- |
| **RegistryRead** |Ger läsbehörighet till identitetsregistret. Mer information finns i [Identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistryReadWrite** |Ger läs- och skrivåtkomst till identitetsregistret. Mer information finns i [Identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **ServiceConnect** |Ger åtkomst till molntjänstriktade kommunikations- och övervakningsslutpunkter. <br/>Ger behörighet att ta emot enhet-till-moln-meddelanden, skicka meddelanden från moln till enhet och hämta motsvarande leveransbekräftelser. <br/>Ger behörighet att hämta leveransbekräftelser för filuppladdningar. <br/>Ger behörighet att komma åt tvillingar för att uppdatera taggar och önskade egenskaper, hämta rapporterade egenskaper och köra frågor. <br/>Den här behörigheten används av backend-molntjänster. |
| **DeviceConnect** |Ger åtkomst till enhetsriktade slutpunkter. <br/>Ger behörighet att skicka enhet-till-moln-meddelanden och ta emot meddelanden från molnet till enheten. <br/>Ger behörighet att utföra filuppladdning från en enhet. <br/>Ger behörighet att ta emot aviseringar om önskad egenskap för enhetstvilling och uppdatera rapporterade egenskaper för enhetstvillingen. <br/>Ger behörighet att utföra filuppladdningar. <br/>Den här behörigheten används av enheter. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensämnen i IoT Hub utvecklarhandbok är:

* [IoT Hub slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter beskriver](iot-hub-devguide-quotas-throttling.md) de kvoter och begränsningsbeteenden som gäller för IoT Hub tjänsten.

* [Azure IoT-enhets- och tjänst-SDK:er](iot-hub-devguide-sdks.md) visar de olika språk-SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub frågespråk](iot-hub-devguide-query-language.md) beskriver det frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingarna och jobben.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information IoT Hub stöd för MQTT-protokollet.

* [RFC 5246 – TLS-protokoll version 1.2 (Transport Layer Security)](https://tools.ietf.org/html/rfc5246/) innehåller mer information om TLS-autentisering.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kontrollerar IoT Hub kan du vara intresserad av följande avsnitt IoT Hub utvecklarguide:

* [Använda enhetstvillingarna för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)
* [Anropa en direktmetod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill prova några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub självstudier:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Så här skickar du meddelanden från moln till enhet med IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Så här bearbetar IoT Hub meddelanden från enheten till molnet](tutorial-routing.md)
