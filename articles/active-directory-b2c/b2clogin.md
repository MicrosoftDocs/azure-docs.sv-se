---
title: 'Migrera program och API: er till b2clogin.com'
titleSuffix: Azure AD B2C
description: 'Läs om hur du använder b2clogin.com i dina omdirigerings-URL: er för Azure Active Directory B2C.'
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109070"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ange omdirigerings-URL: er till b2clogin.com för Azure Active Directory B2C

När du konfigurerar en identitets leverantör för registrering och inloggning i ditt Azure Active Directory B2C (Azure AD B2C) måste du ange en omdirigerings-URL. Du bör inte längre referera till *login.microsoftonline.com* i dina program och API: er för att autentisera användare med Azure AD B2C. Använd i stället *b2clogin.com* för alla nya program och migrera befintliga program från *login.microsoftonline.com* till *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Utfasning av login.microsoftonline.com

**Uppdatering från oktober 2020:** Vi utökar en respitperiod för klienter som inte kan uppfylla det ursprungligen presenterade utfasnings datumet 04 december 2020. Pensionering av login.microsoftonline.com kommer nu inte att ske tidigare än **14 januari 2021.**

**Bakgrund**: den 04 december 2019 [presenterade](https://azure.microsoft.com/updates/b2c-deprecate-msol/) vi ursprungligen den schemalagda indragningen av login.microsoftonline.com-support i Azure AD B2C den 04 december 2020. Detta tillhandahöll befintliga klienter ett (1) år att migrera till b2clogin.com. Nya klienter som skapats efter 04 december 2019 accepterar inte förfrågningar från login.microsoftonline.com. Alla funktioner förblir desamma på b2clogin.com-slutpunkten.

Utfasningen av login.microsoftonline.com påverkar inte Azure Active Directory klienter. Endast Azure Active Directory B2C klienter påverkas av den här ändringen.

## <a name="what-endpoints-does-this-apply-to"></a>Vilka slut punkter gäller för
Över gången till b2clogin.com gäller endast för autentiserings slut punkter som använder Azure AD B2C-principer (användar flöden eller anpassade principer) för att autentisera användare. Dessa slut punkter har en `<policy-name>` parameter som anger den princip Azure AD B2C ska använda. [Läs mer om Azure AD B2C-principer](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Dessa slut punkter kan se ut så här:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Alternativt kan du `<policy-name>` skicka den som en frågeparameter:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Slut punkter som använder parametern "princip" måste uppdateras samt [omdirigerings-URL: er för identitetsprovider](#change-identity-provider-redirect-urls).

Vissa Azure AD B2C-kunder använder delade funktioner i Azure AD Enterprise-klienter som OAuth 2,0-klient autentiseringsuppgifter för att tilldela ett flöde. Dessa funktioner nås med hjälp av Azure ADs login.microsoftonline.com-slutpunkter, *som inte innehåller någon princip parameter*. __Dessa slut punkter påverkas inte__.

## <a name="benefits-of-b2clogincom"></a>Fördelar med b2clogin.com

När du använder *b2clogin.com* som omdirigerings-URL:

* Förbrukat utrymme i cookie-huvudet av Microsoft-tjänster minskas.
* Dina omdirigerings-URL: er behöver inte längre innehålla en referens till Microsoft.
* Java Script-kod på klient sidan stöds (för närvarande i för [hands version](javascript-and-page-layout.md)) på anpassade sidor. På grund av säkerhets begränsningar tas JavaScript-kod och HTML-formulär element bort från anpassade sidor om du använder *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Översikt över nödvändiga ändringar

Det finns flera ändringar som du kan behöva göra för att migrera dina program till *b2clogin.com*:

* Ändra omdirigerings-URL: en i din Identitetsproviders program till Reference *b2clogin.com*.
* Uppdatera dina Azure AD B2C program så att de använder *b2clogin.com* i sina användar flöden och token slut punkts referenser. Detta kan inkludera uppdatering av ditt användnings bibliotek som Microsoft Authentication Library (MSAL).
* Uppdatera eventuella **tillåtna ursprung** som du har DEFINIERAT i CORS-inställningarna för [anpassning av användar gränssnitt](customize-ui-with-html.md).

En gammal slut punkt kan se ut så här:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

En motsvarande uppdaterad slut punkt skulle se ut så här:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Ändra omdirigerings-URL: er för identitetsprovider

På varje identitets leverantörs webbplats där du har skapat ett program, ändra alla betrodda URL: er som ska omdirigeras till `your-tenant-name.b2clogin.com` i stället för *login.microsoftonline.com*.

Det finns två format som du kan använda för dina b2clogin.com-omdirigerings-URL: er. Det första ger fördelen att du inte har "Microsoft" visas var som helst i URL: en genom att använda klient-ID: t (en GUID) i stället för domän namnet för din klient organisation:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Det andra alternativet använder klient domän namnet i form av `your-tenant-name.onmicrosoft.com` . Exempel:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

För båda formaten:

* Ersätt `{your-tenant-name}` med namnet på din Azure AD B2C-klient.
* Ta bort `/te` om den finns i URL: en.

## <a name="update-your-applications-and-apis"></a>Uppdatera dina program och API: er

Koden i dina Azure AD B2C-aktiverade program och API: er kan referera till `login.microsoftonline.com` på flera platser. Din kod kan till exempel innehålla referenser till användar flöden och token-slutpunkter. Uppdatera följande referens till i stället `your-tenant-name.b2clogin.com` :

* Behörighets slut punkt
* Token-slutpunkt
* Token utfärdare

Till exempel skulle auktoritets slut punkten för Contosos registrerings-/inloggnings princip bli:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Information om hur du migrerar OWIN-baserade webb program till b2clogin.com finns i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).

Information om hur du migrerar Azure API Management API: er som skyddas av Azure AD B2C finns i avsnittet [migrera till b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) för att [skydda ett Azure API Management-API med Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority-egenskap

Om du använder [MSAL.net][msal-dotnet] v2 eller tidigare anger du egenskapen **ValidateAuthority** till `false` på klientens instansiering för att tillåta omdirigering till *b2clogin.com*. Det krävs inget värde för att ange det här värdet `false` för MSAL.net v3 och senare.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL för Java Script validateAuthority-egenskap

Om du använder [MSAL för Java Script][msal-js] v 1.2.2 eller tidigare anger du egenskapen **validateAuthority** till `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Om du ställer in `validateAuthority: true` i MSAL.js 1.3.0 + (standard) måste du också ange en giltig token-utfärdare med `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Nästa steg

Information om hur du migrerar OWIN-baserade webb program till b2clogin.com finns i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).

Information om hur du migrerar Azure API Management API: er som skyddas av Azure AD B2C finns i avsnittet [migrera till b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) för att [skydda ett Azure API Management-API med Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
