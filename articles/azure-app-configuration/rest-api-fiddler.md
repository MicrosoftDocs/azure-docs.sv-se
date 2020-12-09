---
title: Azure Active Directory REST API-test med Fiddler
description: Använd Fiddler för att testa Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932616"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testa Azure App konfigurations REST API med Fiddler

Om du vill testa REST API med [Fiddler](https://www.telerik.com/fiddler)måste du inkludera de HTTP-huvuden som krävs för [autentisering](./rest-api-authentication-hmac.md) i dina begär Anden. Så här konfigurerar du Fiddler för att testa REST API och genererar sedan automatiskt:

1. Se till att TLS 1,2 är ett tillåtet protokoll:
    1. Gå till **verktyg**  >  **alternativ**  >  **https**).
    1. Kontrol lera att **DEKRYPTERA HTTPS-trafik** är markerad.
    1. I listan över protokoll lägger du till **TLS 1.2** om det inte finns.
1. Öppna **Fiddler Script Editor** eller tryck på **CTRL-R** i Fiddler
1. Lägg till följande kod inuti `Handlers` klassen innan `OnBeforeRequest` funktionen

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Lägg till följande kod i slutet av `OnBeforeRequest` funktionen. Uppdatera åtkomst nyckeln enligt vad som anges i kommentaren.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Använd [Fiddler-Composer](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) för att generera och skicka en begäran
