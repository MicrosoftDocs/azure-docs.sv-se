---
title: Använda MSAL.js med Azure AD B2C
titleSuffix: Microsoft identity platform
description: 'Microsoft Authentication Library för Java Script (MSAL.js) gör det möjligt för program att arbeta med Azure AD B2C och hämta token för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, webb-API: er från andra eller ditt eget webb-API.'
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063730"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Använd Microsoft Authentication Library för Java Script för att arbeta med Azure AD B2C

[Microsoft Authentication Library för Java Script (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) gör att JavaScript-utvecklare kan autentisera användare med sociala och lokala identiteter med [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

Genom att använda Azure AD B2C som identitets hanterings tjänst kan du anpassa och styra hur dina kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Med Azure AD B2C kan du också varumärke och anpassa det användar gränssnitt som programmet visar under autentiseringsprocessen.

Följande avsnitt visar hur du:

- Skydda ett Node.js webb-API
- Stöd inloggning i ett enda webb-program (SPA) och anrop till *det* skyddade webb-API: et
- Aktivera stöd för lösen ords återställning

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har gjort det skapar du en [Azure AD B2C-klient](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>Node.js-webb-API

Följande steg visar hur ett **webb-API** kan använda Azure AD B2C för att skydda sig själv och exponera valda omfång för ett klient program.

MSAL.js för noden håller på att utvecklas. Mer information finns i [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) över GitHub. Vi rekommenderar för närvarande att använda [Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), ett autentiseringspaket för Node.js som utvecklas och stöds av Microsoft.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att skydda ditt webb-API med Azure AD B2C måste du först registrera det. Se [Registrera ditt program](../../active-directory-b2c/add-web-api-application.md) för detaljerade anvisningar.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Ladda ned exempel programmet

Hämta exemplet som en zip-fil eller klona den från GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

1. Öppna *config.js* filen i exemplet.

2. Konfigurera exemplet med de programautentiseringsuppgifter som du fick tidigare när du registrerade ditt program. Ändra följande rader med kod genom att ersätta värdena med klient organisationens namn, klient-ID och princip namn.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Mer information finns i det här [Node.js B2C-API-exemplet](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>Java Script SPA

Följande steg visar hur ett program med en **enda sida** kan använda Azure AD B2C för att registrera dig, logga in och anropa ett skyddat webb-API.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att implementera autentisering måste du först registrera ditt program. Se [Registrera ditt program](../../active-directory-b2c/tutorial-register-applications.md) för detaljerade anvisningar.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Ladda ned exempel programmet

Ladda ned kod exemplet [. ZIP-arkiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona GitHub-lagringsplatsen:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

Det finns två intressanta punkter när du konfigurerar ditt program:

- Konfigurera API-slutpunkt och exponerade omfång
- Konfigurera autentiseringsmetoder och token-scope

1. Öppna *apiConfig.js* -filen i exemplet.

2. Konfigurera exemplet med de parametrar som du fick tidigare när du registrerade ditt webb-API. Ändra följande rader med kod genom att ersätta värdena med adressen för ditt webb-API och exponerade omfång.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Öppna *authConfig.js* -filen i exemplet.

1. Konfigurera exemplet med de parametrar som du fick tidigare när du registrerade ditt program på en sida. Ändra följande rader med kod genom att ersätta värdena med dina ClientId, utfärda metadata och token för token-begäran.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Mer information finns i [Java Script B2C-exempelprogram med en sida](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Stöd för lösen ords återställning

I det här avsnittet ska du utöka ditt program på en sida för att använda det Azure AD B2C användar flödet för lösen ords återställning. Även om MSAL.js inte har stöd för flera användar flöden eller anpassade principer internt, kan du använda biblioteket för att hantera vanliga användnings fall som lösen ords återställning.

Följande steg förutsätter att du redan har följt stegen i avsnittet föregående [Spa för Java Script](#javascript-spa) .

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Steg 1: definiera auktoritets strängen för användar flödet för lösen ords återställning

1. Börja med att skapa ett objekt där du lagrar dina auktoritets-URI: er:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Initiera sedan MSAL-objektet med `signInSignUp` principen som standard (se föregående kodfragment). När en användare försöker logga in visas följande skärm bild:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Inloggnings skärmen visas av Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Steg 2: fånga och hantera autentiseringsfel i inloggnings metoden

När användaren väljer **glömt lösen ord**, genererar programmet ett fel som du bör fånga i din kod och sedan hanterar genom att presentera lämpligt användar flöde. I det här fallet `b2c_1_reset` flödet för lösen ords återställning.

1. Utöka din inloggnings metod enligt följande:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Föregående kodfragment visar hur du visar skärmen för lösen ords återställning efter att du har fångat felet med koden `AADB2C90118` .

    När du har återställt lösen ordet återgår användaren tillbaka till programmet för att logga in igen.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Flödes skärmarna för lösen ords återställning visade Azure AD B2C" border="false":::

    Mer information om felkoder och hantering av undantag finns i [MSAL-fel och undantags koder](msal-error-handling-js.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om dessa Azure AD B2C koncept:

- [Användarflöden](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Anpassade principer](../../active-directory-b2c/custom-policy-get-started.md)
- [UX-anpassning](../../active-directory-b2c/configure-user-input.md)
