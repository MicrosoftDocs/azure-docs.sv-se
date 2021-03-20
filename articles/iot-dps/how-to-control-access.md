---
title: Säkerhets slut punkter i IoT Device Provisioning-tjänsten | Microsoft Docs
description: Begrepp – hur du styr åtkomsten till IoT Device Provisioning-tjänsten (DPS) för backend-appar. Innehåller information om säkerhetstoken.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954332"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrollera åtkomst till Azure IoT Hub Device Provisioning Service

I den här artikeln beskrivs alternativen för att skydda IoT Device Provisioning-tjänsten. Etablerings tjänsten använder *behörigheter* för att bevilja åtkomst till varje slut punkt. Behörigheter begränsar åtkomsten till en tjänst instans baserat på funktioner.

I den här artikeln beskrivs:

* De olika behörigheter som du kan tilldela till en backend-app för att få åtkomst till etablerings tjänsten.
* Autentiseringsprocessen och de token som används för att verifiera behörigheter.

### <a name="when-to-use"></a>När du ska använda detta

Du måste ha rätt behörighet för att få åtkomst till alla etablerings tjänstens slut punkter. En backend-app måste till exempel innehålla en token som innehåller autentiseringsuppgifter för varje meddelande som skickas till tjänsten.

## <a name="access-control-and-permissions"></a>Åtkomst kontroll och behörigheter

Du kan bevilja [behörigheter](#device-provisioning-service-permissions) på följande sätt:

* **Auktoriseringsprinciper för delad åtkomst**. Principer för delad åtkomst kan ge en kombination av [behörigheter](#device-provisioning-service-permissions). Du kan definiera principer i [Azure Portal][lnk-management-portal]eller program mässigt med hjälp av [REST-API: er för enhets etablerings tjänsten][lnk-resource-provider-apis]. En nyligen skapad etablerings tjänst har följande standard princip:

* **provisioningserviceowner**: princip med alla behörigheter.

> [!NOTE]
> Se [behörigheter](#device-provisioning-service-permissions) för detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub Device Provisioning Service beviljar åtkomst till slut punkter genom att verifiera en token mot principerna för delad åtkomst. Säkerhets referenser, till exempel symmetriska nycklar, skickas aldrig över kabeln.

> [!NOTE]
> Resurs leverantören för enhets etablerings tjänsten skyddas via din Azure-prenumeration, som alla leverantörer i [Azure Resource Manager][lnk-azure-resource-manager].

Mer information om hur du skapar och använder säkerhetstoken finns i nästa avsnitt.

HTTP är det enda protokoll som stöds och implementerar autentisering genom att inkludera en giltig token i begärans huvud för **auktorisering** .

#### <a name="example"></a>Exempel
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [SDK: erna för Azure IoT Device Provisioning-tjänsten][lnk-sdks] genererar automatiskt tokens när de ansluter till tjänsten.

## <a name="security-tokens"></a>Säkerhetstoken

Enhets etablerings tjänsten använder säkerhetstoken för att autentisera tjänster för att undvika att skicka nycklar i kabeln. Dessutom är säkerhetstoken begränsade inom giltighets tid och omfång. SDK: er för [Azure IoT Device Provisioning-tjänsten][lnk-sdks] genererar automatiskt tokens utan att kräva någon speciell konfiguration. Vissa scenarier kräver att du genererar och använder säkerhetstoken direkt. Sådana scenarier omfattar direkt användning av HTTP-ytan.

### <a name="security-token-structure"></a>Struktur för säkerhetstoken

Du använder säkerhetstoken för att bevilja tidsbegränsad åtkomst för tjänster till vissa funktioner i IoT Device Provisioning-tjänsten. För att få behörighet att ansluta till etablerings tjänsten måste tjänsterna skicka säkerhetstoken signerade med antingen delad åtkomst eller symmetrisk nyckel.

En token som signerats med en delad åtkomst nyckel beviljar åtkomst till alla funktioner som är associerade med behörigheterna för den delade åtkomst principen. 

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| signatur |En HMAC-SHA256 signatur sträng i formatet: `{URL-encoded-resourceURI} + "\n" + expiry` . **Viktigt**: nyckeln avkodas från base64 och används som nyckel för att utföra den HMAC-SHA256 beräkningen.|
| förfallo |UTF8-strängar för antalet sekunder sedan 00:00:00 UTC på 1 januari 1970. |
| {URL-kodad – resourceURI} | Gemen URL-kodning för den nedre fall resurs-URI: n. URI-prefix (efter segment) för de slut punkter som kan nås med denna token, med början på värd namnet för IoT Device Provisioning-tjänsten (inget protokoll). Till exempel `mydps.azure-devices-provisioning.net`. |
| PolicyName |Namnet på den princip för delad åtkomst som denna token refererar till. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Motsvarande python-kod för att generera en säkerhetstoken är som jämförelse:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Eftersom token för en giltighets tid verifieras på IoT Device Provisioning service-datorer, måste driften på datorns klocka som genererar token vara minimal.

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänst komponenter

Tjänst komponenter kan bara skapa säkerhetstoken med hjälp av principer för delad åtkomst som beviljar lämpliga behörigheter som förklaras tidigare.

Här är tjänst funktionerna som visas på slut punkterna:

| Slutpunkt | Funktioner |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Tillhandahåller enhets registrerings åtgärder med Device Provisioning-tjänsten. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Tillhandahåller åtgärder för att hantera enhets registrerings grupper. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Tillhandahåller åtgärder för att hämta och hantera statusen för enhets registreringar. |


Till exempel skulle en tjänst som genererats med en i förväg skapad princip för delad åtkomst som heter **enrollmentread** skapa en token med följande parametrar:

* resurs-URI: `{mydps}.azure-devices-provisioning.net` ,
* signerings nyckel: en av nycklarna i `enrollmentread` principen.
* princip namn: `enrollmentread` ,
* förfallo tid. backad

![Skapa en princip för delad åtkomst för din enhets etablerings tjänst instans i portalen][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som skulle ge åtkomst för att läsa alla registrerings poster, blir:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referens ämnen:

Följande referens avsnitt innehåller mer information om hur du styr åtkomsten till din IoT Device Provisioning-tjänst.

### <a name="device-provisioning-service-permissions"></a>Behörigheter för enhets etablerings tjänst

I följande tabell visas de behörigheter som du kan använda för att kontrol lera åtkomsten till din IoT Device Provisioning-tjänst.

| Behörighet | Kommentarer |
| --- | --- |
| **ServiceConfig** |Beviljar åtkomst för att ändra tjänst konfigurationerna. <br/>Den här behörigheten används av Server dels moln tjänster. |
| **EnrollmentRead** |Ger Läs behörighet till enhets registreringar och registrerings grupper. <br/>Den här behörigheten används av Server dels moln tjänster. |
| **EnrollmentWrite** |Ger skriv åtkomst till enhets registreringar och registrerings grupper. <br/>Den här behörigheten används av Server dels moln tjänster. |
| **RegistrationStatusRead** |Ger Läs behörighet till status för enhets registrering. <br/>Den här behörigheten används av Server dels moln tjänster. |
| **RegistrationStatusWrite**  |Ger behörighet att ta bort åtkomst till status för enhets registrering. <br/>Den här behörigheten används av Server dels moln tjänster. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/