---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att tänka på när du använder Azure AD B2C med Microsoft Authentication Library för Android (MSAL. Android
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: f87f2e79bd9439fddb52fad82c7ab4712fc68fb9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680373"
---
# <a name="use-msal-for-android-with-b2c"></a>Använda MSAL för Android med B2C

Microsoft Authentication Library (MSAL) gör det möjligt för programutvecklare att autentisera användare med sociala och lokala identiteter genom att använda [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml). Azure AD B2C är en identitets hanterings tjänst. Använd den för att anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurera kända utfärdade och omdirigerings-URI

I MSAL för Android konfigureras B2C-principer (användar resor) som enskilda utfärdare.

Ett B2C-program som har två principer:
- Registrering/inloggning
    * Erinra `B2C_1_SISOPolicy`
- Redigera profil
    * Erinra `B2C_1_EditProfile`

Konfigurations filen för appen deklarerar två `authorities` . En för varje princip. `type`Egenskapen för varje myndighet är `B2C` .

>Obs: `account_mode` måste anges till **Multiple** för B2C-program. Läs dokumentationen om du vill ha mer information om [flera offentliga klient program för konton](./single-multi-account.md#multiple-account-public-client-application).

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "account_mode" : "MULTIPLE",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri`Måste vara registrerad i appens konfiguration och även `AndroidManifest.xml` stöd för omdirigering under [tilldelnings flödet för auktoriseringskod](../../active-directory-b2c/authorization-code-flow.md).

## <a name="initialize-ipublicclientapplication"></a>Initiera IPublicClientApplication

`IPublicClientApplication` skapas av en fabriks metod som gör att program konfigurationen kan parsas asynkront.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Hämta en token interaktivt

Du kan hämta en token interaktivt med MSAL genom att bygga en `AcquireTokenParameters` instans och tillhandahålla den till- `acquireToken` metoden. Token-begäran nedan använder `default` utfärdaren.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Förnya en token tyst

Om du vill hämta en token i bakgrunden med MSAL skapar du en `AcquireTokenSilentParameters` instans och anger den till- `acquireTokenSilentAsync` metoden. Till skillnad från `acquireToken` metoden `authority` måste måste anges för att hämta en token i bakgrunden.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Ange en princip

Eftersom principerna i B2C representeras som separata myndigheter, så kan du anropa en annan princip än standardvärdet genom att ange en `fromAuthority` sats vid konstruktion `acquireToken` eller `acquireTokenSilent` parametrar.  Till exempel:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Hantera principer för lösen ords ändring

Användar flödet för registrering av lokalt konto eller inloggnings användare visar ett **bortglömt lösen ord?** . När du klickar på den här länken utlöses inget användar flöde för lösen ords återställning automatiskt.

I stället returneras fel koden `AADB2C90118` till din app. Din app ska hantera den här felkoden genom att köra ett speciellt användar flöde som återställer lösen ordet.

Om du vill fånga en felkod för lösen ords återställning kan du använda följande implementering i `AuthenticationCallback` :

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Använd IAuthenticationResult

Ett lyckat nyckel hämtnings resultat i ett `IAuthenticationResult` objekt. Den innehåller åtkomsttoken, användar anspråk och metadata.

### <a name="get-the-access-token-and-related-properties"></a>Hämta åtkomsttoken och relaterade egenskaper

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Hämta det auktoriserade kontot

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken-anspråk

Anspråk som returneras i IdToken fylls i av Security Token Service (STS), inte av MSAL. Beroende på vilken identitets leverantör (IdP) som används kan vissa anspråk saknas. Vissa IDP: er tillhandahåller för närvarande inte `preferred_username` anspråket. Eftersom det här anspråket används av MSAL för cachelagring, används plats hållarens värde `MISSING FROM THE TOKEN RESPONSE` i sitt ställe. Mer information om B2C IdToken-anspråk finns i [Översikt över tokens i Azure Active Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Hantera konton och principer

B2C behandlar varje princip som en separat myndighet. Därför är åtkomst-token, Refresh tokens och ID-token som returneras från varje princip inte utbytbara. Det innebär att varje princip returnerar ett separat `IAccount` objekt vars token inte kan användas för att anropa andra principer.

Varje princip lägger till en `IAccount` i cacheminnet för varje användare. Om en användare loggar in i ett program och anropar två principer har de två `IAccount` s. Om du vill ta bort den här användaren från cachen måste du anropa `removeAccount()` för varje princip.

När du förnyar token för en princip med `acquireTokenSilent` , ange samma `IAccount` som returnerades från tidigare anrop till principen  `AcquireTokenSilentParameters` . Att tillhandahålla ett konto som returneras av en annan princip resulterar i ett fel.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Active Directory B2C (Azure AD B2C) i [Vad är Azure Active Directory B2C?](../../active-directory-b2c/overview.md)