---
title: Utveckling av Microsoft Identity Platform – Azure
description: Lär dig mer om Microsoft Identity Platform, en utveckling av identitets tjänsten för Azure Active Directory (Azure AD) och Developer Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96608222"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Utvecklingen av Microsofts identitetsplattform

[Microsoft Identity Platform](../develop/index.yml) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Det gör det möjligt för utvecklare att skapa program som loggar in användare, Hämta token för att anropa API: er, till exempel Microsoft Graph eller API: er som utvecklare har skapat. Det består av en autentiseringstjänst, bibliotek med öppen källkod, program registrering och konfiguration (via en utvecklings Portal och ett program-API), fullständig dokumentation om utvecklare, snabb starts exempel, kod exempel, självstudier, instruktions guider och annat innehåll i utvecklare. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect.

Många utvecklare har tidigare arbetat med Azure AD v 1.0-plattformen för att autentisera arbets-och skol konton (etablerade av Azure AD) genom att begära token från Azure AD v 1.0-slutpunkten, med hjälp av Azure AD Authentication Library (ADAL), Azure Portal för program registrering och konfiguration och Microsoft Graph API för program konfiguration.

Med den enhetliga Microsoft Identity Platform (v 2.0) kan du skriva kod en gång och autentisera alla Microsoft-identiteter i ditt program. För flera plattformar rekommenderar vi att du använder Microsoft Authentication Library (MSAL) med öppen källkod för användning mot identitets Plattformens slut punkter. MSAL är enkelt att använda, ger utmärkta funktioner för enkel inloggning (SSO) för dina användare, hjälper dig att uppnå hög tillförlitlighet och prestanda och utvecklas med Microsoft Secure Development Lifecycle (SDL). När du anropar API: er kan du konfigurera ditt program så att det utnyttjar det stegvisa godkännandet, vilket gör att du kan fördröja begäran om godkännande för mer invasiva omfattningar tills programmets användning garanterar detta vid körning.  MSAL har också stöd för Azure Active Directory B2C, så dina kunder använder sina prioriterade sociala, företags eller lokala konto identiteter för att få enkel inloggning till dina program och API: er.

Med Microsoft Identity Platform kan du utöka din räckvidd till följande typer av användare:

- Arbets-och skol konton (etablerade Azure AD-konton)
- Personliga konton (t. ex. Outlook.com eller Hotmail.com)
- Dina kunder som tar sin egen e-post eller sociala identitet (till exempel LinkedIn, Facebook, Google) via MSAL och Azure AD B2C

Du kan använda Azure Portal för att registrera och konfigurera ditt program och använda Microsoft Graph API för program konfiguration.

Uppdatera ditt program i din egen takt. Program som skapats med ADAL-bibliotek fortsätter att stödjas. Blandade program portföljer, som består av program som skapats med ADAL och program som skapats med MSAL-bibliotek, stöds också. Det innebär att program som använder de senaste ADAL och de senaste MSAL levererar SSO i portföljen, som tillhandahålls av det delade token cache mellan dessa bibliotek. Program som uppdateras från ADAL till MSAL upprätthåller användar inloggnings status vid uppgraderingen.

## <a name="microsoft-identity-platform-experience"></a>Microsoft Identity-plattformsgränssnittet

I diagrammet nedan visas Microsoft Identity-gränssnittet på hög nivå, bland annat appregistreringsfunktioner, SDK:er, slutpunkter och identiteter som stöds.

![Microsoft Identity-plattformen i dag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Program registrerings upplevelse

Den Azure Portal **[Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908)** upplevelsen är en portal upplevelse för att hantera alla program som du har integrerat med Microsoft Identity Platform. Om du har använt program registrerings portalen kan du börja använda Azure Portal appens registrerings upplevelse i stället.

För integration med Azure AD B2C (vid autentisering av sociala eller lokala identiteter) måste du registrera ditt program i en Azure AD B2C-klient. Den här upplevelsen är också en del av Azure Portal.

Använd [Application API](/graph/api/resources/application) för att program mässigt konfigurera dina program som är integrerade med Microsoft Identity Platform för att autentisera alla Microsoft-identiteter.

### <a name="msal-libraries"></a>MSAL-bibliotek

Du kan använda MSAL-biblioteket för att bygga program som autentiserar alla Microsoft-identiteter. MSAL-biblioteken i .NET och Java Script är allmänt tillgängliga. MSAL-bibliotek för iOS och Android är i för hands version och lämpade för användning i en produktions miljö. Vi ger samma produktions nivå stöd för MSAL-bibliotek i för hands versionen som vi gör för versioner av MSAL och ADAL som är allmänt tillgängliga.

Du kan också använda MSAL-biblioteken för att integrera ditt program med Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft Identity Platform-slutpunkt

Slut punkten för Microsoft Identity Platform (v 2.0) är OIDC-certifierad. Det fungerar med Microsoft Authentication libraries (MSAL) eller andra standardkompatibla bibliotek. Den implementerar människo läsliga områden, i enlighet med bransch standarder.

## <a name="next-steps"></a>Nästa steg

Läs mer i [dokumentationen för Microsoft Identity Platform](../develop/index.yml).
