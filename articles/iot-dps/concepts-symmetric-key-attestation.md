---
title: Azure-IoT Hub Device Provisioning Service – symmetrisk nyckel attestering
description: Den här artikeln innehåller en översikt över symmetrisk nyckel attestering med IoT Device Provisioning-tjänsten (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: devx-track-csharp
ms.openlocfilehash: 994c2c3124d6822f047af942268ad7a401d5a976
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531567"
---
# <a name="symmetric-key-attestation"></a>Symmetrisk nyckelattestering

I den här artikeln beskrivs processen för identitets attestering när du använder symmetriska nycklar med enhets etablerings tjänsten. 

Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med en enhets etablerings tjänst instans. Den här attesterings metoden representerar en "Hello World"-upplevelse för utvecklare som är nya för enhets etablering eller som inte har strikta säkerhets krav. Enhets attestering med ett [TPM](concepts-tpm-attestation.md) eller ett [X. 509-certifikat](concepts-x509-attestation.md) är säkrare och bör användas för mer långtgående säkerhets krav.

Symmetrisk nyckel registrering ger också ett bra sätt för äldre enheter, med begränsade säkerhetsfunktioner, för att starta molnet via Azure IoT. Mer information om symmetrisk nyckel attestering med äldre enheter finns i [så här använder du symmetriska nycklar med äldre enheter](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Skapa symmetrisk nyckel

Enhets etablerings tjänsten skapar som standard nya symmetriska nycklar med en standard längd på 64 byte när nya registreringar sparas med alternativet för **automatisk generering av nycklar** aktiverade.

![Generera symmetriska nycklar automatiskt](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Du kan också ange egna symmetriska nycklar för registrering genom att inaktivera det här alternativet. När du anger egna symmetriska nycklar måste nycklarna ha en nyckel längd mellan 16 byte och 64 byte. Dessutom måste symmetriska nycklar tillhandahållas i ett giltigt base64-format.



## <a name="detailed-attestation-process"></a>Detaljerad attesterings process

Symmetrisk nyckel attestering med enhets etablerings tjänsten utförs med samma [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure) som stöds av IoT Hub för att identifiera enheter. Dessa säkerhetstoken är [token för signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md). 

SAS-token har en hash- *signatur* som skapas med den symmetriska nyckeln. Signaturen återskapas av enhets etablerings tjänsten för att kontrol lera om en säkerhetstoken som visas under attesteringen är äkta eller inte.

SAS-token har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är komponenterna i varje token:

| Värde | Beskrivning |
| --- | --- |
| signatur |En HMAC-SHA256-signatur sträng. För enskilda registreringar skapas den här signaturen med hjälp av den symmetriska nyckeln (primär eller sekundär) för att utföra hashen. För registrerings grupper används en nyckel som härletts från registrerings grupp nyckeln för att utföra hashen. Hashen utförs på ett meddelande med formatet: `URL-encoded-resourceURI + "\n" + expiry` . **Viktigt**: nyckeln måste avkodas från base64 innan den används för att utföra HMAC-SHA256-beräkningen. Dessutom måste resultatet av signaturen vara URL-kodat. |
| ResourceURI |URI för den registrerings slut punkt som kan nås med denna token, med start med scope-ID för enhets etablerings tjänst instansen. Till exempel `{Scope ID}/registrations/{Registration ID}` |
| förfallo |UTF8-strängar för antalet sekunder sedan 00:00:00 UTC på 1 januari 1970. |
| {URL-kodad – resourceURI} |Gemen URL – kodning för den nedre fall resurs-URI: n |
| PolicyName |Namnet på den princip för delad åtkomst som denna token refererar till. Princip **namnet som används** när etableringen med symmetrisk nyckel attestering registreras. |

När en enhet besvaras med en enskild registrering använder enheten den symmetriska nyckel som definierats i den enskilda registrerings posten för att skapa den hashade signaturen för SAS-token.

Kod exempel som skapar en SAS-token finns i [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Att skapa säkerhetstoken för symmetrisk nyckel attestering stöds av Azure IoT C SDK. Ett exempel som använder Azure IoT C SDK för att intyga med en enskild registrering finns i [etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Grupp registreringar

De symmetriska nycklarna för grupp registreringar används inte direkt av enheter vid etablering. I stället enheter som tillhör en registrerings grupps etablering med hjälp av en härledd enhets nyckel. 

Först definieras ett unikt registrerings-ID för varje enhet som bestyrkas med en registrerings grupp. Giltiga tecken för registrerings-ID: t är gemena alfanumeriska tecken och bindestreck (-). Detta registrerings-ID ska vara något unikt som identifierar enheten. Till exempel kanske en äldre enhet inte stöder många säkerhetsfunktioner. Den äldre enheten kanske bara har en MAC-adress eller ett serie nummer tillgängligt för att unikt identifiera enheten. I så fall kan ett registrerings-ID bestå av MAC-adressen och serie numret som liknar följande:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Det här exakta exemplet används i artikeln [hur man etablerar äldre enheter med hjälp av symmetriska nycklar](how-to-legacy-device-symm-key.md) .

När ett registrerings-ID har definierats för enheten används den symmetriska nyckeln för registrerings gruppen för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) hash av registrerings-ID: t för att skapa en härledd enhets nyckel. Hashing av registrerings-ID: t kan utföras med följande C#-kod:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Den resulterande enhets nyckeln används sedan för att skapa en SAS-token som ska användas för attestering. Varje enhet i en registrerings grupp måste attesteras med hjälp av en säkerhetstoken som genereras från en unik härledd nyckel. Det går inte att använda symmetrisk nyckel för registrerings gruppen direkt för attestering.

#### <a name="installation-of-the-derived-device-key"></a>Installation av den härledda enhets nyckeln

Vi rekommenderar att enhets nycklarna härleds och installeras i fabriken. Den här metoden garanterar att grupp nyckeln aldrig ingår i någon program vara som distribueras till enheten. När enheten tilldelas en MAC-adress eller ett serie nummer kan nyckeln härledas och matas in i enheten, men tillverkaren väljer att lagra den.

Tänk på följande diagram som visar en tabell med enhets nycklar som har genererats i en fabrik genom att Hasha varje registrerings-ID för enheten med grupp registrerings nyckeln (**K**). 

![Enhets nycklar tilldelade från en fabrik](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identiteten för varje enhet representeras av registrerings-ID och härledd enhets nyckel som installeras på fabriken. Enhets nyckeln kopieras aldrig till en annan plats och grupp nyckeln lagras aldrig på en enhet.

Om enhets nycklarna inte är installerade i fabriken, ska en [modul för maskin varu säkerhet](concepts-service.md#hardware-security-module) användas för att lagra enhets identiteten på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för symmetrisk nyckel attestering kan du läsa mer i följande artiklar:

* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* [Lär dig mer om begreppen etablering](about-iot-dps.md#provisioning-process)
* [Kom igång med automatisk etablering](./quick-setup-auto-provision.md) 
