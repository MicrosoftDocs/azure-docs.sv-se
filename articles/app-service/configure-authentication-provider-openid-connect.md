---
title: Konfigurera en OpenID Connect-Provider (förhands granskning)
description: Lär dig hur du konfigurerar en OpenID Connect-provider som identitets leverantör för din App Service-eller Azure Functions-app.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983879"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Konfigurera din App Service- eller Azure Functions-app för inloggning med en OpenID Connect-provider (förhandsversion)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions för att använda en anpassad autentiseringsprovider som följer [OpenID Connect-specifikationen](https://openid.net/connect/). OpenID Connect (OIDC) är en bransch standard som används av många identitets leverantörer (IDP: er). Du behöver inte förstå informationen om specifikationen för att konfigurera appen så att den använder en adherent-IDP.

Du kan konfigurera appen så att den använder en eller flera OIDC-providrar. Varje måste ha ett unikt namn i konfigurationen och endast en kan fungera som standard mål för omdirigering.

> [!CAUTION]
> Om du aktiverar en OpenID Connect-Provider inaktive ras hanteringen av funktionen App Service autentisering/auktorisering för ditt program via vissa klienter, till exempel Azure Portal, Azure CLI och Azure PowerShell. Funktionen är beroende av en ny API-yta som, under för hands versionen, ännu inte har redovisat i alla hanterings upplevelser.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrera ditt program hos identitets leverantören

Leverantören kräver att du registrerar information om ditt program med den. Se de instruktioner som är relevanta för leverantören. Du måste samla in ett **klient-ID** och **klient hemlighet** för ditt program.

> [!IMPORTANT]
> Appens hemlighet är en viktig säkerhets autentiseringsuppgift. Dela inte den här hemligheten med någon eller distribuera den i ett klient program.
>

> [!NOTE]
> Vissa leverantörer kan behöva ytterligare steg för att konfigurera konfigurationen och använda de värden som de tillhandahåller. Apple tillhandahåller till exempel en privat nyckel som inte används som OIDC-klient hemlighet, och du måste i stället använda IT-båten ett JWT-program som behandlas som den hemlighet som du anger i appens konfiguration (se avsnittet "skapa klient hemlighet" i [inloggningen med Apple-dokumentationen](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens))
>

Lägg till klient hemligheten som en [program inställning](./configure-common.md#configure-app-settings) för appen med ett inställnings namn som du själv väljer. Anteckna namnet för senare.

Dessutom behöver du OpenID Connect-metadata för providern. Detta exponeras ofta via ett [dokument för metadata för metadata](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), som är providerns URL-suffix för utfärdare `/.well-known/openid-configuration` . Samla in denna konfigurations webb adress.

Om du inte kan använda ett dokument för metadata för konfiguration måste du samla in följande värden separat:

- Utfärdar-URL (visas ibland som `issuer` )
- [Slut punkten för OAuth 2,0-auktorisering](https://tools.ietf.org/html/rfc6749#section-3.1) (visas ibland som `authorization_endpoint` )
- [OAuth 2,0-token-slutpunkt](https://tools.ietf.org/html/rfc6749#section-3.2) (visas ibland som `token_endpoint` )
- URL: en för [webb nyckel uppsättnings dokumentet för OAuth 2,0 JSON-webb nyckel](https://tools.ietf.org/html/rfc8414#section-2) (visas ibland `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Lägg till leverantörs information i ditt program

> [!NOTE]
> Den obligatoriska konfigurationen är i ett nytt API-format som för närvarande endast stöds av [filbaserad konfiguration (för hands version)](.\app-service-authentication-how-to.md#config-file). Du måste följa stegen nedan för att använda en sådan fil.

Det här avsnittet beskriver hur du uppdaterar konfigurationen för att inkludera din nya IDP. En exempel konfiguration följer.

1. I `identityProviders` objektet lägger du till ett `openIdConnectProviders` objekt om det inte redan finns.
1. `openIdConnectProviders`Lägg till en nyckel för din nya Provider i objektet. Detta är ett eget namn som används för att referera till providern i resten av konfigurationen. Om du till exempel vill kräva att alla begär Anden ska autentiseras med den här providern anger du `globalValidation.unauthenticatedClientAction` till "RedirectToLoginPage" och anger `redirectToProvider` detta egna namn.
1. Tilldela ett objekt till nyckeln med ett `registration` objekt i det och eventuellt ett `login` objekt:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. I objektet registrering anger du `clientId` till det klient-ID som du har samlat in, anger `clientCredential.secretSettingName` namnet på den program inställning där du sparade klient hemligheten och skapar ett `openIdConnectConfiguration` objekt:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. I `openIdConnectConfiguration` objektet anger du OpenID Connect-metadata som du samlade in tidigare. Det finns två alternativ för detta, baserat på vilken information du samlat in:

    - Ange `wellKnownOpenIdConfiguration` egenskapen till URL: en för Configuration metadata som du samlat in tidigare.
    - Du kan också ange de fyra enskilda värdena som samlas in på följande sätt:
        - Ange `issuer` till utfärdar-URL
        - Ange `authorizationEndpoint` till behörighets slut punkten
        - Ange `tokenEndpoint` till token-slutpunkt
        - Ange `certificationUri` till URL: en för webb nyckel uppsättnings dokumentet för JSON

    Dessa två alternativ är ömsesidigt uteslutande.

När den här konfigurationen har ställts in är du redo att använda OpenID Connect-providern för autentisering i din app.

En exempel konfiguration kan se ut så här (med logga in med Apple som ett exempel, där APPLE_GENERATED_CLIENT_SECRET inställningen pekar på en genererad JWT enligt [Apples dokumentation](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

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
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
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
