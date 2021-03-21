---
title: Ny Appregistreringar upplevelse i Azure AD B2C
description: En introduktion till den nya appens registrerings upplevelse i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 430da23986fc36a0e94c049512ef716aff1fed5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660258"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Den nya Appregistreringar upplevelsen för Azure Active Directory B2C

Den nya **[Appregistreringar](https://aka.ms/b2cappregistrations)** upplevelsen för Azure Active Directory B2C (Azure AD B2C) är nu allmänt tillgänglig. Om du är mer van vid **program** användning för att registrera program för Azure AD B2C, som nämns här som "äldre erfarenhet", kommer den här guiden att hjälpa dig att komma igång med den nya upplevelsen.

## <a name="overview"></a>Översikt
Tidigare var du tvungen att hantera dina Azure AD B2C klientbaserade program separat från resten av dina appar med hjälp av den äldre miljön. Detta innebar att olika appar skapas på olika platser i Azure.

Den nya upplevelsen visar alla Azure AD B2C app-registreringar och Azure AD App-registreringar på ett och samma ställe och ger ett konsekvent sätt att hantera dem. Du behöver bara lära dig ett sätt att skapa en app för att hantera en app med Microsoft Graph behörigheter för resurs hantering.

Du kan uppnå den nya upplevelsen genom att gå till **Appregistreringar** i en Azure AD B2C klient från både **Azure AD B2C** eller **Azure Active Directory** tjänsterna i Azure Portal.

Azure AD B2C Appregistreringar-upplevelsen baseras på den allmänna [appens registrerings upplevelse](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) för alla Azure AD-klienter, men är skräddarsydda för Azure AD B2C klienter.

## <a name="whats-not-changing"></a>Vad ändras inte?
- Dina program och relaterade konfigurationer kan hittas i den nya upplevelsen. Du behöver inte registrera programmen igen och användarna av dina program behöver inte logga in igen.

> [!NOTE]
> Om du vill visa alla tidigare skapade program går du till bladet **Appregistreringar** och väljer fliken **alla program** . Då visas appar som skapats i den tidigare versionen, den nya upplevelsen och de som skapats i Azure AD-tjänsten.

## <a name="key-new-features"></a>Viktiga nya funktioner

-   En **enhetlig app-lista** visar alla dina program som autentiseras med Azure AD B2C och Azure AD på en lämplig plats. Dessutom kan du dra nytta av funktioner som redan är tillgängliga för Azure AD-program, inklusive datum för **skapad** , **certifikat & hemligheter** , Sök fältet och mycket mer.

-   Med **kombinerad app-registrering** kan du snabbt registrera en app, oavsett om det är en kundriktad app eller en app för att få åtkomst till Microsoft Graph.

- I fönstret **slut punkter** kan du snabbt identifiera relevanta slut punkter för ditt scenario, inklusive OpenID Connect-konfiguration, SAML-metadata, Microsoft Graph API och [OAuth 2,0-slutpunkter för användar flödet](tokens-overview.md#endpoints).

- **API-behörigheter** och **exponera ett API** ger mer omfattande omfång, behörighet och godkännande hantering. Nu kan du även tilldela MS Graph-och Azure AD Graph-behörigheter till en app.

-   **Ägare** och **manifest** är nu tillgängliga för appar som autentiseras med Azure AD B2C. Du kan lägga till ägare för dina registreringar och redigera program egenskaperna direkt [med hjälp av manifest redigeraren](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Nya konto typer som stöds

I den nya upplevelsen väljer du en Support konto typ från följande alternativ:
- Endast konton i den här organisationskatalogen
- Konton i valfri organisations katalog (alla Azure AD-kataloger – flera klient organisationer)
- Konton i valfri identitets leverantör eller organisations katalog (för autentisering av användare med användar flöden)

Om du vill förstå de olika konto typerna väljer du **Hjälp mig att välja** i skapande upplevelsen.

I den tidigare versionen har appar alltid skapats som kund program. För dessa appar anges konto typen till **konton i valfri identitets leverantör eller organisations katalog (för autentisering av användare med användar flöden)**.
> [!NOTE]
> Det här alternativet krävs för att kunna köra Azure AD B2C användar flöden för att autentisera användare för programmet. Lär dig [hur du registrerar ett program för användning med användar flöden.](tutorial-register-applications.md)

Du kan också använda det här alternativet om du vill använda Azure AD B2C som en SAML-tjänstprovider. [Läs mer](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>Program för DevOps-scenarier

Du kan använda de andra konto typerna för att skapa en app för att hantera dina DevOps-scenarier, t. ex. genom att använda Microsoft Graph för att överföra principer för identitets miljö eller etablera användare. Lär dig [hur du registrerar ett Microsoft Graph program för att hantera Azure AD B2C-resurser](microsoft-graph-get-started.md).

Du kanske inte ser alla Microsoft Graph behörigheter, eftersom många av de här behörigheterna inte gäller användare av Azure B2C-konsumenter. [Läs mer om att hantera användare med hjälp av Microsoft Graph](microsoft-graph-operations.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Administrativa medgivande och offline_access-och OpenID-scope
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

**OpenID** -omfattningen är nödvändig så att Azure AD B2C kan logga in användare i en app. **Offline_access** omfattning krävs för att utfärda uppdateringstoken för en användare. De här omfattningarna lades till tidigare och tilldelades administrativt godkännande som standard. Nu kan du enkelt lägga till behörigheter för de här omfången under skapande processen genom att se till att alternativet **bevilja administratörs medgivande till OpenID och offline_access behörigheter** är markerat. Annars kan Microsoft Graph behörigheter läggas till med administratörs medgivande i inställningarna för **API-behörigheter** för en befintlig app.

Läs mer om [behörigheter och medgivande](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plattformar/autentisering: svars webb adresser/omdirigerings-URI: er
I den tidigare versionen hanterades de olika plattforms typerna under **Egenskaper** som svars-URL: er för webbappar/API: er och omdirigerings-URI för interna klienter. "Interna klienter" kallas även "offentliga klienter" och innehåller appar för iOS-, macOS-, Android-och andra program typer för mobiler och skriv bord.

I den nya upplevelsen kallas svars-URL: er och omdirigerings-URI: er båda omdirigerings-URI: er och finns i avsnittet **autentisering** av en app. Appregistreringar är inte begränsade till antingen en webbapp eller ett internt program. Du kan använda samma app-registrering för alla dessa plattforms typer genom att registrera respektive omdirigerings-URI: er.

Omdirigerings-URI: er måste associeras med en app-typ, antingen webb eller offentlig (mobil och stationär dator). [Läs mer om omdirigerings-URI: er](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**IOS/MacOS-** och **Android** -plattformarna är en typ av offentlig klient. De ger ett enkelt sätt att konfigurera iOS/macOS-eller Android-appar med motsvarande omdirigerings-URI: er för användning med MSAL. Läs mer om [program konfigurations alternativ](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Program certifikat & hemligheter

I den nya upplevelsen i stället för **nycklar** använder du bladet **certifikat & hemligheter** för att hantera certifikat och hemligheter. Certifikat & hemligheter gör det möjligt för program att identifiera sig själva för Autentiseringstjänsten när de tar emot token på en plats för webb adresser (med ett HTTPS-schema). Vi rekommenderar att du använder ett certifikat i stället för en klient hemlighet för klientens autentiseringsuppgifter vid autentisering mot Azure AD. Certifikat kan inte användas för att autentisera mot Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Funktioner som inte är tillämpliga i Azure AD B2C-klienter
Följande funktioner i Azure AD App Registration är inte tillämpliga på eller tillgängliga i Azure AD B2C klienter:
- **Roller och administratörer** – för närvarande inte tillgängligt för Azure AD B2C.
- **Anpassning – UI** /UX-anpassning konfigureras i **företags anpassnings** miljön eller som en del av ett användar flöde. Lär dig hur du [anpassar användar gränssnittet i Azure Active Directory B2C](customize-ui-with-html.md).
- **Domän verifiering i Publisher** – appen är registrerad på *. onmicrosoft.com*, vilket inte är en verifierad domän. Dessutom används Publisher-domänen främst för att bevilja användar medgivande, som inte gäller för Azure AD B2C appar för användarautentisering. [Läs mer om Publisher-domän](../active-directory/develop/howto-configure-publisher-domain.md).
- **Token-konfiguration** – token är konfigurerad som en del av ett användar flöde i stället för en app.
- **Snabb** starten är för närvarande inte tillgänglig för Azure AD B2C klienter.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Begränsningar
Den nya upplevelsen har följande begränsningar:
- För tillfället skiljer Azure AD B2C inte mellan att kunna utfärda åtkomst eller ID-token för implicita flöden. båda typerna av tokens är tillgängliga för implicit tilldelnings flöde om alternativet **ID-token** är markerat på bladet **autentisering** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Det finns inte stöd för att ändra värdet för konton som stöds i användar gränssnittet. Du måste använda appens manifest, om du inte växlar mellan Azure AD-klienten och flera klienter.

## <a name="next-steps"></a>Nästa steg

För att komma igång med den nya appens registrerings upplevelse:
* Lär dig [hur du registrerar ett webb program](tutorial-register-applications.md).
* Lär dig [hur du registrerar ett webb-API](add-web-api-application.md).
* Lär dig [hur du registrerar ett internt klient program](add-native-application.md).
* Lär dig [hur du registrerar ett Microsoft Graph program för att hantera Azure AD B2C-resurser](microsoft-graph-get-started.md).
* Lär dig [hur du använder Azure AD B2C som en SAML-tjänstprovider.](identity-provider-adfs.md)
* Lär dig mer om [program typer](application-types.md).