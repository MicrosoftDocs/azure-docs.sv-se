---
title: 'Självstudie: aktivera autentisering i en app med en enda sida'
titleSuffix: Azure AD B2C
description: I den här självstudien får du lära dig hur du använder Azure Active Directory B2C för att tillhandahålla användar inloggning för ett JavaScript-baserat program med en sida (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96171236"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Självstudie: aktivera autentisering i ett program med en sida med Azure AD B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att registrera och logga in användare i ett enda webb program (SPA) med hjälp av antingen:
* [OAuth 2,0 Authorization Code Flow](./authorization-code-flow.md) (med [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* [OAuth-flöde för implicit beviljande av OAuth 2,0](./implicit-flow-single-page-application.md) (med [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

I den här självstudien är den första i en serie i två delar:

> [!div class="checklist"]
> * Lägg till en svars-URL i ett program som är registrerat i Azure AD B2C klient organisationen
> * Hämta ett kod exempel från GitHub
> * Ändra exempel programmets kod så att den fungerar med din klient
> * Registrera dig med ditt användar flöde för registrering/inloggning

[Nästa självstudie](tutorial-single-page-app-webapi.md) i serien möjliggör webb-API-delen av kod exemplet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande Azure AD B2C resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Programmet är registrerat](tutorial-register-spa.md) i din klient
* [Användar flöden som skapats](tutorial-create-user-flows.md) i din klient organisation

Dessutom behöver du följande i din lokala utvecklings miljö:

* [Visual Studio Code](https://code.visualstudio.com/) eller en annan kod redigerare
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den [andra själv studie kursen](./tutorial-register-spa.md) som du avslutade som en del av förutsättningarna registrerade du ett program med en sida i Azure AD B2C. Om du vill aktivera kommunikation med kod exemplet i den här självstudien lägger du till en svars-URL (kallas även en omdirigerings-URI) till program registreringen.

Om du vill uppdatera ett program i din Azure AD B2C klient kan du använda vår nya enhetliga **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](./app-registrations-training-guide.md)

#### <a name="app-registrations-auth-code-flow"></a>[Appregistreringar (kod flöde för autentisering)](#tab/app-reg-auth/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**, Välj fliken **ägda program** och välj sedan *spaapp1* -programmet.
1. Under **en Enkels Ides applikation** väljer du länken **Lägg till URI** och sedan ange `http://localhost:6420` .
1. Välj **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID: t (klient)** för användning i ett senare steg när du uppdaterar koden i webb programmet med en sida.

#### <a name="app-registrations-implicit-flow"></a>[Appregistreringar (implicit flöde)](#tab/app-reg-implicit/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar**, Välj fliken **ägda program** och välj sedan *spaapp1* -programmet.
1. Under **en Enkels Ides applikation** väljer du länken **Lägg till URI** och sedan ange `http://localhost:6420` .
1. Under **implicit beviljande** väljer du kryss rutorna för **åtkomsttoken** och **ID-token** om de inte redan är markerade och väljer sedan **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID: t (klient)** för användning i ett senare steg när du uppdaterar koden i webb programmet med en sida.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Azure AD B2C**.
1. Välj **program (bakåtkompatibelt)** och välj sedan *spaapp1* -programmet.
1. Under **Svars-URL** lägger du till `http://localhost:6420`.
1. Välj **Spara**.
1. På sidan Egenskaper registrerar du **program-ID: t**. Du använder app-ID i ett senare steg när du uppdaterar koden i webb programmet med en sida.

* * *

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kod exempel som du hämtar från GitHub för att arbeta med din B2C-klient. Exemplet visar hur ett program med en enda sida kan använda Azure AD B2C för användarens registrering och inloggning, och anropa ett skyddat webb-API (du aktiverar webb-API: et i nästa självstudie i serien).

* Exempel på kod flöde med MSAL.js 2. x:

    [Hämta en zip-fil](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) eller klona exemplet från GitHub:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* MSAL.js 1. x implicit flödes exempel:

    [Hämta en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har hämtat exemplet uppdaterar du koden med ditt Azure AD B2C klient namn och det program-ID som du registrerade i ett tidigare steg.

#### <a name="auth-code-flow-sample"></a>[Exempel på kod flöde för autentisering](#tab/config-auth/)

1. Öppna *authConfig.js* -filen i *app* -mappen.
1. I `msalConfig` objektet söker du efter tilldelningen för `clientId` och ersätter den med det **program-ID** som du registrerade i ett tidigare steg.
1. Öppna filen `policies.js`.
1. Hitta posterna under `names` och ersätt deras tilldelning med namnet på de användar flöden som du skapade i ett tidigare steg, till exempel `B2C_1_signupsignin1` .
1. Hitta posterna under `authorities` och ersätt dem efter behov med namnen på de användar flöden som du skapade i ett tidigare steg, till exempel `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Sök efter tilldelningen `authorityDomain` och ersätt den med `<your-tenant-name>.b2clogin.com` .
1. Öppna filen `apiConfig.js`.
1. Hitta tilldelningen för `b2cScopes` och ersätt URL: en med den omfattnings-URL som du skapade för webb-API: t `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Hitta tilldelningen för `webApi` och ersätt den aktuella URL: en med URL: en där du har distribuerat ditt webb-API i steg 4, till exempel `webApi: http://localhost:5000/hello` .

#### <a name="implicit-flow-sample"></a>[Exempel på implicit flöde](#tab/config-implicit/)

1. Öppna *authConfig.js* -filen i mappen *JavaScriptSPA*
1. I `msalConfig` objektet söker du efter tilldelningen för `clientId` och ersätter den med det **program-ID** som du registrerade i ett tidigare steg.
1. Öppna filen `policies.js`.
1. Hitta posterna under `names` och ersätt deras tilldelning med namnet på de användar flöden som du skapade i ett tidigare steg, till exempel `B2C_1_signupsignin1` .
1. Hitta posterna under `authorities` och ersätt dem efter behov med namnen på de användar flöden som du skapade i ett tidigare steg, till exempel `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Öppna filen `apiConfig.js`.
1. Hitta tilldelningen för `b2cScopes` och ersätt URL: en med den omfattnings-URL som du skapade för webb-API: t `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Hitta tilldelningen för `webApi` och ersätt den aktuella URL: en med URL: en där du har distribuerat ditt webb-API i steg 4, till exempel `webApi: http://localhost:5000/hello` .

* * *

Den resulterande koden bör se ut ungefär så här:

#### <a name="auth-code-flow-sample"></a>[Exempel på kod flöde för autentisering](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Exempel på implicit flöde](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsol fönster och navigera till den katalog som innehåller exemplet. 

    - För MSAL.js 2. x-Authorization Code Flow-exempel:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - För MSAL.js 1. x implicit flödes exempel: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Kör följande kommandon:

    ```console
    npm install && npm update
    npm start
    ```

    Konsol fönstret visar port numret för den lokala Node.js-servern som körs:

    ```console
    Listening on port 6420...
    ```
1. Bläddra till `http://localhost:6420` för att Visa webb programmet som körs på den lokala datorn.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbläsare som visar en Enkels Ides applikation som körs lokalt":::

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

Det här exempel programmet stöder registrering, inloggning och lösen ords återställning. I den här självstudien får du registrera dig med en e-postadress.

1. Välj **Logga** in för att starta det *B2C_1_signupsignin1* användar flöde du angav i ett tidigare steg.
1. Azure AD B2C visar en inloggnings sida som innehåller en registrerings länk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu** .
1. Arbets flödet för registrering visar en sida där du kan samla in och verifiera användarens identitet med hjälp av en e-postadress. Arbets flödet för registrering samlar även in användarens lösen ord och de begärda attribut som definierats i användar flödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ställ in ett lösenord. Ange värden för de begärda attributen.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Sidan registrera visas i Azure AD B2C användar flöde":::

1. Välj **skapa** för att skapa ett lokalt konto i katalogen Azure AD B2C.

När du väljer **skapa**, visar programmet namnet på den inloggade användaren.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="En webbläsare som visar ett program med en sida med inloggad användare":::

Om du vill testa inloggningen väljer du knappen **Logga ut** . Välj sedan **Logga in** och logga in med den e-postadress och det lösen ord som du angav när du registrerade dig.

### <a name="what-about-calling-the-api"></a>Vad händer med att anropa API: et?

Om du väljer **anrops-API** -knappen när du har loggat in visas sidan användar flöde för registrering/inloggning i stället för resultatet av API-anropet. Detta är förväntat eftersom du ännu inte har konfigurerat API-delen av programmet för att kommunicera med ett webb-API-program *registrerat i Azure AD B2C klient organisationen.*

I det här läget försöker programmet fortfarande kommunicera med det API som är registrerat i demo klient organisationen (fabrikamb2c.onmicrosoft.com) och eftersom du inte autentiseras med den klient organisationen visas sidan för registrering/inloggning.

Gå vidare till nästa självstudie i serien i om du vill aktivera det skyddade API: et (se [Nästa steg](#next-steps) -avsnitt).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat ett program med en enda sida som fungerar med ett användar flöde i din Azure AD B2C-klient för att ge dig möjlighet att registrera och logga in. Du har slutfört de här stegen:

> [!div class="checklist"]
> * En svars-URL lades till i ett program som är registrerat i Azure AD B2C klient organisationen
> * Ett kod exempel har hämtats från GitHub
> * Ändrade exempel programmets kod så att den fungerar med din klient
> * Registrerat med ditt användar flöde för registrering/inloggning

Gå vidare till nästa självstudie i serien för att bevilja åtkomst till ett skyddat webb-API från SPA:

> [!div class="nextstepaction"]
> [Självstudie: skydda och bevilja åtkomst till webb-API från ett program med en sida >](tutorial-single-page-app-webapi.md)