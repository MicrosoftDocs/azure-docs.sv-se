---
title: Konfigurera inloggning med Apple (för hands version)
description: Lär dig hur du konfigurerar inloggning med Apple som en identitets leverantör för din App Service-eller Azure Functions-app.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96603364"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Konfigurera App Service-eller Azure Functions-appen för att logga in med en inloggning med Apple Provider (för hands version)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions att använda logga in med Apple som en autentiseringsprovider. 

För att slutföra proceduren i den här artikeln måste du ha registrerat dig i Apple Developer-programmet. Om du vill registrera dig i programmet Apple Developer går du till [Developer.Apple.com/programs/Enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Genom att aktivera inloggning med Apple inaktive ras hanteringen av funktionen App Service autentisering/auktorisering för ditt program via vissa klienter, till exempel Azure Portal, Azure CLI och Azure PowerShell. Funktionen är beroende av en ny API-yta som, under för hands versionen, ännu inte har redovisat i alla hanterings upplevelser.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Skapa ett program i Apple Developer-portalen
Du måste skapa ett app-ID och ett tjänst-ID i Apple Developer-portalen.

1. På Apple Developer-portalen går du till **certifikat, identifierare, & profiler**.
2. På fliken **identifierare** väljer du knappen **(+)** .
3. På sidan **Registrera en ny identifierare** väljer du **app-ID** och väljer **Fortsätt**. (App-ID: n innehåller ett eller flera tjänst-ID: n.) ![Registrera ett nytt app-ID i Apple Developer-portalen](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. På sidan **Registrera ett app-ID** anger du en beskrivning och ett paket-ID och väljer **Logga in med Apple** från listan funktioner. Välj sedan **Fortsätt**. Anteckna ditt **app-ID-prefix (Team-ID)** från det här steget. du behöver det senare.
![Konfigurera ett nytt app-ID i Apple Developer-portalen](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Granska appens registrerings information och välj **Registrera**.
6. På fliken **identifierare** igen väljer du knappen **(+)** .
![Skapa ett nytt tjänst-ID i Apple Developer-portalen](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. På sidan **Registrera en ny identifierare** väljer du **Services ID** och väljer **Fortsätt**.
![Registrera en ny tjänst identifierare i Apple Developer-portalen](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. På sidan **Registrera ett tjänste-ID** anger du en beskrivning och en identifierare. Beskrivningen är vad som visas för användaren på godkännande skärmen. Identifieraren är ditt klient-ID som används för att konfigurera Apple-providern med din app service. Välj sedan **Konfigurera**.
![Ange en beskrivning och en identifierare](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. I popup-fönstret anger du det primära app-ID: t till det app-ID som du skapade tidigare. Ange programmets domän i avsnittet domän. Använd URL: en för retur-URL: en `<app-url>/.auth/login/apple/callback` . Till exempel `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Välj sedan **Lägg till** och **Spara**.
![Ange domänen och retur-URL: en för registreringen](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Granska tjänst registrerings informationen och välj **Spara**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Generera klient hemligheten
Apple kräver att appar utvecklare skapar och signerar en JWT-token som klientens hemliga värde. För att generera den här hemligheten genererar först och laddar ned en privat Elliptic-formad kurva från Apple Developer-portalen. Använd sedan den nyckeln för att [signera en JWT](#sign-the-client-secret-jwt) med en [angiven nytto Last](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Skapa och hämta den privata nyckeln
1. På fliken **nycklar** i Apple Developer-portalen väljer du **skapa en nyckel** eller väljer knappen **(+)** .
2. På sidan **Registrera en ny nyckel** ger du nyckeln ett namn, markerar kryss rutan bredvid **Logga in med Apple** och väljer **Konfigurera**.
3. På sidan **Konfigurera nyckel** länkar du nyckeln till det primära app-ID som du skapade tidigare och väljer **Spara**.
4. Slutför skapandet av nyckeln genom att bekräfta informationen och välja **Fortsätt** och sedan granska informationen och välja **Registrera**.
5. Hämta nyckeln på sidan **Hämta din nyckel** . Den laddas ned som en `.p8` (PKCS # 8) fil – du ska använda fil innehållet för att signera ditt klient hemlighet JWT.

### <a name="structure-the-client-secret-jwt"></a>Strukturera klient hemligheten JWT
Apple kräver att klient hemligheten är base64-kodningen för en JWT-token. Den avkodade JWT-token ska ha en nytto last som är strukturerad enligt detta exempel:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: Apple-klient-ID (även tjänst-ID)
- **ISS**: ditt Apple Developer-Team-ID
- **AUD**: Apple tar emot token, så de är mål gruppen
- **exp**: högst sex månader efter **NBF**

Den base64-kodade versionen av ovanstående nytto Last ser ut så här: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Obs! Apple accepterar inte klientens hemliga JWTs med ett förfallo datum mer än sex månader efter att datumet för skapande (eller NBF) skapats. Det innebär att du måste rotera klient hemligheten minst var sjätte månad._

Mer information om att skapa och validera token finns i [Apples dokumentation om utvecklare](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Signera klient hemligheten JWT
Du använder `.p8` filen som du laddade ned tidigare för att signera klient hemligheten JWT. Den här filen är en [PCKS # 8-fil](https://en.wikipedia.org/wiki/PKCS_8) som innehåller den privata signerings nyckeln i PEM-format. Det finns många bibliotek som kan skapa och signera JWT åt dig. 

Det finns olika typer av bibliotek med öppen källkod som är tillgängliga online för att skapa och signera JWT-token. Mer information om hur du skapar JWT-token finns i jwt.io. Ett sätt att skapa klient hemligheten är till exempel genom att importera [paketet Microsoft. IdentityModel. tokens NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) och köra en liten mängd C#-kod som visas nedan.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: ditt Apple Developer-Team-ID
- **clientId**: Apple-klient-ID (även tjänst-ID)
- **p8key**: PEM format nyckel – du kan hämta nyckeln genom att öppna `.p8` filen i en text redigerare och kopiera allt mellan `-----BEGIN PRIVATE KEY-----` och `-----END PRIVATE KEY-----` utan rad brytningar
- **keyId**: ID för den nedladdade nyckeln

Den här token returnerade klientens hemliga värde som du använder för att konfigurera Apple-providern.

> [!IMPORTANT]
> Klient hemligheten är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.
>

Lägg till klient hemligheten som en [program inställning](./configure-common.md#configure-app-settings) för appen med ett inställnings namn som du själv väljer. Anteckna namnet för senare.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Lägg till leverantörs information i ditt program

> [!NOTE]
> Den obligatoriska konfigurationen är i ett nytt API-format som för närvarande endast stöds av [filbaserad konfiguration (för hands version)](.\app-service-authentication-how-to.md#config-file). Du måste följa stegen nedan för att använda en sådan fil.

Det här avsnittet beskriver hur du uppdaterar konfigurationen för att inkludera din nya IDP. En exempel konfiguration följer.

1. I `identityProviders` objektet lägger du till ett `apple` objekt om det inte redan finns.
2. Tilldela ett objekt till nyckeln med ett `registration` objekt i det och eventuellt ett `login` objekt:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. I `registration` objektet anger du `clientId` till det klient-ID som du har samlat in.
    
    b. I `registration` objektet anger du `clientSecretSettingName` namnet på den program inställning där du sparade klient hemligheten.
    
    c. I `login` objektet kan du välja att ange `scopes` matrisen för att inkludera en lista över omfattningar som används vid autentisering med Apple, till exempel "namn" och "e-post". Om omfattningar konfigureras, begärs de uttryckligen på medgivande skärmen när användarna loggar in för första gången.

När den här konfigurationen har ställts in är du redo att använda din Apple-Provider för autentisering i din app.

En fullständig konfiguration kan se ut som i följande exempel (där APPLE_GENERATED_CLIENT_SECRET inställningen pekar på en program inställning som innehåller en genererad JWT):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
