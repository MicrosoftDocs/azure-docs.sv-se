---
title: Enkel inloggning (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att skapa funktioner för enkel inloggning med hjälp av Microsoft Authentication Library för Java Script (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84690786"
---
# <a name="single-sign-on-with-msaljs"></a>Enkel inloggning med MSAL.js

Enkel Sign-On (SSO) gör att användarna kan ange sina autentiseringsuppgifter en gång för att logga in och upprätta en session som kan återanvändas över flera program utan att du behöver autentisera igen. Detta ger en sömlös upplevelse för användaren och minskar de upprepade frågorna om autentiseringsuppgifter.

Azure AD tillhandahåller SSO-funktioner till program genom att ställa in en sessions-cookie när användaren autentiseras första gången. MSAL.jss biblioteket gör det möjligt för program att utnyttja detta på några få sätt.

## <a name="sso-between-browser-tabs"></a>Enkel inloggning mellan flikar i webbläsare

När programmet är öppet på flera flikar och du först loggar in användaren på en flik, är användaren också inloggad på de andra flikarna utan att bli uppmanad. MSAL.js cachelagrar ID-token för användaren i webbläsaren `localStorage` och kommer att signera användaren i programmet på de andra öppna flikarna.

Som standard använder MSAL.js `sessionStorage` som inte tillåter att sessionen delas mellan flikar. För att få en enkel inloggning mellan flikarna, se till att ange `cacheLocation` i MSAL.js `localStorage` som visas nedan.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Enkel inloggning mellan appar

När en användare autentiseras anges en sessions-cookie i Azure AD-domänen i webbläsaren. MSAL.js använder denna sessions-cookie för att tillhandahålla enkel inloggning för användaren mellan olika program. MSAL.js cachelagrar också ID-tokens och åtkomsttoken för användaren i webbläsarens lagrings plats per program domän. Det innebär att SSO-beteendet varierar i olika fall:  

### <a name="applications-on-the-same-domain"></a>Program i samma domän

När program finns på samma domän kan användaren logga in i en app en gång och sedan autentisera till de andra apparna utan att du behöver göra något. MSAL.js utnyttjar de token som cachelagrats för användaren på domänen för att tillhandahålla SSO.

### <a name="applications-on-different-domain"></a>Program i en annan domän

När program finns på olika domäner kan inte de token som cachelagras i domän A nås av MSAL.js i domän B.

Det innebär att när användare som är inloggade på domän A navigerar till ett program i domän B, kommer de att omdirigeras eller uppmanas med Azure AD-sidan. Eftersom Azure AD fortfarande har cookien för användarsessionen, kommer den att logga in användaren och de behöver inte ange autentiseringsuppgifterna på nytt. Om användaren har flera användar konton i sessionen med Azure AD uppmanas användaren att välja det relevanta kontot för att logga in med.

### <a name="automatically-select-account-on-azure-ad"></a>Välj automatiskt konto i Azure AD

I vissa fall har programmet åtkomst till användarens autentiserings kontext och vill undvika att fråga om val av Azure AD-konto när flera konton är inloggade.  Detta kan göras på olika sätt:

**Använd sessions-ID (SID)**

Sessions-ID är ett [valfritt anspråk](active-directory-optional-claims.md) som kan konfigureras i ID-token. Detta påstående gör att programmet kan identifiera användarens Azure AD-session oberoende av användarens konto namn eller användar namn. Du kan skicka SID i parametrarna för begäran till `acquireTokenSilent` anropet. Detta gör att Azure AD kan kringgå val av konto. SID är kopplat till sessionens cookie och kommer inte att korsa webbläsarens kontexter.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID kan endast användas med begär Anden om obevakade autentiseringar som görs via `acquireTokenSilent` anrop i MSAL.js.
Du kan hitta stegen för att konfigurera valfria anspråk i program manifestet [här](active-directory-optional-claims.md).

**Använda inloggnings tips**

Om du inte har konfigurerat sid-anspråk eller om du behöver kringgå konto urvals frågan i interaktiva autentiseringsbegäranden, kan du göra det genom att ange en `login_hint` i parametrarna för begäran och eventuellt en `domain_hint` som `extraQueryParameters` i MSAL.js interaktiva metoder ( `loginPopup` , `loginRedirect` `acquireTokenPopup` och `acquireTokenRedirect` ). Exempel:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Du kan hämta värdena för login_hint och domain_hint genom att läsa de anspråk som returneras i ID-token för användaren.

* **loginHint** ska anges till `preferred_username` Claim i ID-token.

* **domain_hint** behöver bara skickas när du använder/vanliga-utfärdaren. Domän tipset bestäms av klient-ID (tid).  Om `tid` anspråket i ID-token är `9188040d-6c67-4c5b-b112-36a304b66dad` det är konsumenterna. Annars är det organisationer.

Läs [här](v2-oauth2-implicit-grant-flow.md) om du vill ha mer information om värdena för inloggnings tips och domän tips.

> [!Note]
> Du kan inte skicka SID och login_hint på samma gång. Detta leder till ett felsvar.

## <a name="sso-without-msaljs-login"></a>SSO utan MSAL.js inloggning

Enligt design kräver MSAL.js att en inloggnings metod anropas för att upprätta en användar kontext innan tokens hämtas för API: er. Eftersom inloggnings metoder är interaktiva ser användaren en fråga.

Det finns vissa fall där program har åtkomst till den autentiserade användarens kontext-eller ID-token genom autentisering som initierats i ett annat program och vill använda SSO för att hämta tokens utan att först logga in via MSAL.js.

Ett exempel på detta är: en användare är inloggad i ett överordnat webb program som är värd för ett annat JavaScript-program som körs som tillägg eller plugin-program.

SSO-upplevelsen i det här scenariot kan uppnås på följande sätt:

Skicka `sid` om det är tillgängligt (eller `login_hint` valfritt `domain_hint` ) som begär parametrar till MSAL.js- `acquireTokenSilent` anropet enligt följande:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO i ADAL.js att MSAL.js uppdateringen

MSAL.js ger till exempel paritet med ADAL.js för Azure AD-autentisering. Om du vill göra migreringen från ADAL.js till MSAL.js enkel och för att undvika att användarna måste logga in igen, läser biblioteket ID-token som representerar användarens session i ADAL.js cache och använder sömlöst tecken i användaren i MSAL.js.  

Om du vill dra nytta av funktionen för enkel inloggning (SSO) när du uppdaterar från ADAL.js måste du se till att biblioteken används `localStorage` för cachelagring av tokens. Ange `cacheLocation` till `localStorage` i både MSAL.js-och ADAL.js-konfigurationen vid initieringen enligt följande:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

När detta har kon figurer ATS kommer MSAL.js att kunna läsa cachelagrat tillstånd för den autentiserade användaren i ADAL.js och använda det för att ge SSO i MSAL.js.

## <a name="next-steps"></a>Nästa steg

Läs mer om de [enkla inloggnings-och token-](active-directory-configurable-token-lifetimes.md) värdena i Azure AD.
