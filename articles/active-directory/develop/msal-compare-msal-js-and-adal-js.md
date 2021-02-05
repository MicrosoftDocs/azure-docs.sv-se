---
title: Skillnader mellan MSAL.js och ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library för Java Script (MSAL.js) och Azure AD Authentication Library för Java Script (ADAL.js) och hur du väljer vilket du vill använda.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a56685c830fc7aa717add3e826c68c04449e378
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580854"
---
# <a name="differences-between-msaljs-and-adaljs"></a>Skillnader mellan MSAL.js och ADAL.js

Både Microsoft Authentication Library för Java Script (MSAL.js) och Azure AD Authentication Library för Java Script (ADAL.js) används för att autentisera Azure AD-entiteter och begära token från Azure AD. Fram till nu har de flesta utvecklare arbetat med Azure AD för utvecklare (v 1.0) för att autentisera Azure AD-identiteter (arbets-och skol konton) genom att begära token med hjälp av ADAL. Nu kan du med hjälp av MSAL.js autentisera en bredare uppsättning Microsoft-identiteter (Azure AD-identiteter och Microsoft-konton, och sociala och lokala konton via Azure AD B2C) via Microsoft Identity Platform.

Den här artikeln beskriver hur du väljer mellan Microsoft Authentication Library för Java Script (MSAL.js) och Azure AD Authentication Library för Java Script (ADAL.js) och jämför de två biblioteken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Välja mellan ADAL.js och MSAL.js

I de flesta fall vill du använda Microsoft Identity Platform och MSAL.js, vilket är den senaste generationen av Microsoft Authentication Libraries. Med hjälp av MSAL.js får du token för användare som loggar in till ditt program med Azure AD (arbets-och skol konton), Microsoft (personliga) konton (MSA) eller Azure AD B2C.

Om du redan är bekant med v 1.0-slutpunkten (och ADAL.js) kanske du vill läsa [vad som är annorlunda om v 2.0-slutpunkten?](../azuread-dev/azure-ad-endpoint-comparison.md).

Du måste dock fortfarande använda ADAL.js om ditt program behöver logga in användare med tidigare versioner av [Active Directory Federation Services (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Viktiga skillnader i autentisering med MSAL.js

### <a name="core-api"></a>Core API

* ADAL.js använder [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) som representation av en instans av programmets anslutning till auktoriseringsservern eller identitets leverantören via en auktoritets-URL. I motsats till detta är MSAL.js API utformat runt användar agentens klient program (en form av ett offentligt klient program där klient koden körs i en användar agent, till exempel en webbläsare). Den ger `UserAgentApplication` klassen en instans av programmets autentiserings kontext med auktoriseringsservern. Mer information finns i [initiera med MSAL.js](msal-js-initializing-client-applications.md).

* I ADAL.js är metoderna för att hämta tokens kopplade till en enda utfärdare som anges i `AuthenticationContext` . I MSAL.js kan begär Anden om Hämta token ta olika auktoritets värden än vad som anges i `UserAgentApplication` . Detta gör att MSAL.js kan förvärva och cachelagra tokens separat för flera klienter och användar konton i samma program.

* Metoden för att hämta och förnya token tyst utan att be användarna att ange namn `acquireToken` i ADAL.js. I MSAL.js kallas den här metoden `acquireTokenSilent` för att vara mer beskrivande av den här funktionen.

### <a name="authority-value-common"></a>Auktoritets värde `common`

I v 1.0 `https://login.microsoftonline.com/common` gör det möjligt för användarna att logga in med alla Azure AD-konton (för alla organisationer) med hjälp av-utfärdaren.

I v 2.0 kan användare med hjälp av `https://login.microsoftonline.com/common` -utfärdaren logga in med ett Azure AD-organisations konto eller ett Microsoft personal-konto (MSA). Använd om du vill begränsa inloggningen till enbart Azure AD-konton (samma beteende som med ADAL.js) `https://login.microsoftonline.com/organizations` . Mer information finns i `authority` konfigurations alternativet i [initiera med MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Omfattningar för att förvärva token
* Omfattning i stället för resurs parameter i autentiseringsbegäranden för att hämta token

    v 2.0-protokollet använder omfång i stället för resurs i begär Anden. När ditt program behöver begära token med behörigheter för en resurs, till exempel MS Graph, är skillnaden i värden som skickas till biblioteks metoderna följande:

    v 1.0: Resource = https \: //Graph.Microsoft.com

    v 2.0: scope = https \: //Graph.Microsoft.com/user.Read

    Du kan begära scope för alla resurs-API: er med hjälp av URI: n för API: et i följande format: appidURI/scope, till exempel: https: \/ /mytenant.onmicrosoft.com/myapi/API.Read

    Endast för MS-Graph API mappas ett omfångs värde `user.read` till https: \/ /Graph.Microsoft.com/user.Read och kan användas utbytbart.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamiska omfattningar för stegvist godkännande.

    När du skapar program med v 1.0 behövde du registrera en fullständig uppsättning behörigheter (statiska omfattningar) som krävs av programmet för att användaren ska kunna godkänna inloggnings tiden. I v 2.0 kan du använda omfattnings parametern för att begära behörigheten vid den tidpunkt du vill. Dessa kallas dynamiska omfattningar. Detta gör det möjligt för användaren att tillhandahålla ett stegvist tillstånd för omfattningar. Så om du i början vill att användaren ska logga in på ditt program och du inte behöver någon typ av åtkomst kan du göra det. Om du senare behöver kunna läsa kalendern för användaren kan du begära att kalender omfånget används i acquireToken-metoderna och få användarens medgivande. Exempel:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Omfattningar för V 1.0 API: er

    När du hämtar token för V 1.0-API: er med hjälp av MSAL.js kan du begära alla statiska omfattningar som registrerats i API: et genom `.default` att lägga till i app-ID-URI: n för API: et som omfång. Exempel:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [jämförelse mellan v 1.0 och v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
