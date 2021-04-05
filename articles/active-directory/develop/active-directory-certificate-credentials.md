---
title: Autentiseringsuppgifter för Microsoft Identity Platform-certifikat
titleSuffix: Microsoft identity platform
description: I den här artikeln beskrivs registreringen och användningen av autentiseringsuppgifter för programautentisering.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582034"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Autentiseringsuppgifter för certifikat för Microsoft Identity Platform Application Authentication

Microsoft Identity Platform gör det möjligt för ett program att använda sina egna autentiseringsuppgifter för autentisering överallt där en klient hemlighet kan användas, till exempel i OAuth 2,0- [klientens autentiseringsuppgifter för tilldelnings](v2-oauth2-client-creds-grant-flow.md) flöde och OBO-flödet. [](v2-oauth2-on-behalf-of-flow.md)

En typ av autentiseringsuppgift som ett program kan använda för autentisering är en [JSON Web token](./security-tokens.md#json-web-tokens-and-claims) (JWT) som är signerad med ett certifikat som programmet äger.

## <a name="assertion-format"></a>Intygs format

Om du vill beräkna försäkran kan du använda ett av många JWT-bibliotek på det språk som din val- [MSAL stöder med `.WithCertificate()` ](msal-net-client-assertions.md). Informationen utförs av token i dess [huvud](#header), [anspråk](#claims-payload)och [signatur](#signature).

### <a name="header"></a>Huvud

| Parameter |  Markera om |
| --- | --- |
| `alg` | Ska vara **RS256** |
| `typ` | Bör vara **JWT** |
| `x5t` | X. 509-certifikatets hash-värde (även kallat certifikatets SHA-1- *tumavtryck*) är kodat som ett Base64url-sträng värde. Till exempel, med ett X. 509 certifikat-hash för `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hex), `x5t` skulle anspråket vara `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Anspråk (nytto Last)

Anspråkstyp | Värde | Beskrivning
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Anspråket "AUD" (Audience) identifierar mottagarna som JWT är avsett för (här Azure AD) se [RFC 7519, avsnitt 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  I det här fallet är mottagaren inloggnings Server (login.microsoftonline.com).
exp | 1601519414 | Anspråket "EXP" (förfallo tid) anger förfallo tid för eller efter vilken JWT inte får godkännas för bearbetning. Se [RFC 7519, section 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Detta gör att kontrollen kan användas tills dess, så håll den kort 5-10 minuter efter `nbf` högst.  Azure AD har ingen begränsning för den `exp` aktuella tiden. 
ISS | ClientID | Anspråket "ISS" (utfärdare) identifierar det huvud konto som utfärdade JWT, i det här fallet klient programmet.  Använd ID för GUID-program.
jti | (ett GUID) | Anspråket "JTI" (JWT ID) tillhandahåller en unik identifierare för JWT. Identifier-värdet måste tilldelas på ett sätt som garanterar att det är en försumbar sannolikhet att samma värde har tilldelats av misstag till ett annat data objekt. om programmet använder flera utfärdare, måste kollisioner förhindras mellan värden som skapas av olika utfärdare. Värdet "JTI" är en Skift läges känslig sträng. [RFC 7519, avsnitt 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Anspråket "NBF" (inte före) anger hur lång tid som JWT inte får godkännas för bearbetning. [RFC 7519, avsnitt 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Det är lämpligt att använda den aktuella tiden. 
Build | ClientID | Anspråket "sub" (subject) identifierar ämnet för JWT, i det här fallet även ditt program. Använd samma värde som `iss` . 

### <a name="signature"></a>Signatur

Signaturen beräknas genom att använda certifikatet enligt beskrivningen i [JSON Web token RFC7519-specifikationen](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Exempel på en avkodad JWT-kontroll

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exempel på en kodad JWT-kontroll

Följande sträng är ett exempel på kodad kontroll. Om du ser noggrant kan du se tre avsnitt avgränsade med punkter ( `.` ):

* Det första avsnittet kodar *rubriken*
* Det andra avsnittet kodar *anspråken* (nytto lasten)
* Det sista avsnittet är den *signatur* som beräknas med certifikaten från innehållet i de första två avsnitten

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrera ditt certifikat med Microsoft Identity Platform

Du kan associera certifikatets autentiseringsuppgifter med klient programmet i Microsoft Identity Platform via Azure Portal med någon av följande metoder:

### <a name="uploading-the-certificate-file"></a>Laddar upp certifikat filen

I Azure App-registreringen för klient programmet:
1. Välj **certifikat & hemligheter**.
2. Klicka på **överför certifikat** och välj den certifikat fil som ska laddas upp.
3. Klicka på **Lägg till**.
  När certifikatet har laddats upp visas tumavtryck, start datum och förfallo värden.

### <a name="updating-the-application-manifest"></a>Uppdaterar applikations manifestet

När du har skaffat ett certifikat beräknar du dessa värden:

- `$base64Thumbprint` – Base64-kodat värde för certifikatets hash
- `$base64Value` – Base64-kodat värde för certifikatets rå data

Ange ett GUID för att identifiera nyckeln i applikations manifestet ( `$keyId` ).

I Azure App-registreringen för klient programmet:
1. Välj **manifest** för att öppna applikations manifestet.
2. Ersätt egenskapen *autentiseringsuppgifter* med den nya certifikat informationen med hjälp av följande schema.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Spara ändringarna i applikations manifestet och ladda upp manifestet till Microsoft Identity Platform.

   `keyCredentials`Egenskapen har flera värden, så du kan ladda upp flera certifikat för bättre nyckel hantering.
   
## <a name="using-a-client-assertion"></a>Använda en klient kontroll

Klientens kontroll kan användas överallt där en klient hemlighet används.  I till exempel [flödet för auktoriseringskod](v2-oauth2-auth-code-flow.md)kan du skicka in en `client_secret` för att bevisa att begäran kommer från din app. Du kan ersätta detta med `client_assertion` `client_assertion_type` parametrarna och. 

| Parameter | Värde | Beskrivning|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Detta är ett fast värde som anger att du använder autentiseringsuppgifter för certifikatet. |
|`client_assertion`| JWT |Detta är den JWT som skapats ovan. |

## <a name="next-steps"></a>Nästa steg

[MSAL.net-biblioteket hanterar det här scenariot](msal-net-client-assertions.md) i en enda rad med kod.

[Program i .net Core daemon-konsolen som använder kod exemplet för Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) på GitHub visar hur ett program använder sina egna autentiseringsuppgifter för autentisering. Det visar också hur du kan [skapa ett självsignerat certifikat](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) med hjälp av `New-SelfSignedCertificate` PowerShell-cmdleten. Du kan också använda skript för att [Skapa appar](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) i exempel lagrings platsen för att skapa certifikat, beräkna tumavtryck och så vidare.
