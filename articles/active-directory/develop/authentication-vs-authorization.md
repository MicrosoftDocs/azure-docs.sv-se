---
title: Autentisering mot auktorisering | Azure
titleSuffix: Microsoft identity platform
description: Lär dig grunderna för autentisering och auktorisering i Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581915"
---
# <a name="authentication-vs-authorization"></a>Autentisering kontra auktorisering

Den här artikeln definierar autentisering och auktorisering. Du får också en kort beskrivning av hur du kan använda Microsoft Identity Platform för att autentisera och auktorisera användare i dina webbappar, webb-API: er eller appar som anropar skyddade webb-API: er. Om du ser en term som du inte är bekant med kan du prova vår [ord lista](developer-glossary.md) eller våra [Microsoft Identity Platform-videor](identity-videos.md), som beskriver grundläggande begrepp.

## <a name="authentication"></a>Autentisering

*Autentisering* är en process för att bevisa att du är den som du säger. Den förkortas ibland till *authn*. Microsoft Identity Platform använder [OpenID Connect](https://openid.net/connect/) -protokollet för att hantera autentisering.

## <a name="authorization"></a>Auktorisering

*Auktorisering* innebär att ge en autentiserad part behörighet att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktoriseringen för kortas ibland till *AuthZ*. Microsoft Identity Platform använder [OAuth 2,0](https://oauth.net/2/) -protokollet för hantering av auktorisering.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autentisering och auktorisering med hjälp av Microsoft Identity Platform

Att skapa appar som var och en upprätthåller sina egna användar namn och lösen ords uppgifter innebär en hög administrativ börda när du lägger till eller tar bort användare i flera appar. I stället kan dina appar delegera det ansvaret till en central identitets leverantör.

Azure Active Directory (Azure AD) är en centraliserad identitets leverantör i molnet. Att delegera autentisering och auktorisering till det möjliggör scenarier som:

- Principer för villkorlig åtkomst som kräver att en användare befinner sig på en angiven plats.
- Användningen av [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), som ibland kallas TVÅFAKTORAUTENTISERING eller 2fa.
- Göra det möjligt för en användare att logga in en gång och sedan loggas in automatiskt till alla webbappar som delar samma centrala katalog. Den här funktionen kallas *enkel inloggning (SSO)*.

Microsofts identitets plattform fören klar auktorisering och autentisering för programutvecklare genom att tillhandahålla identitet som en tjänst. Det stöder bransch standard protokoll och bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt börja koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, Hämta token för att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), få åtkomst till Microsoft API: er eller komma åt andra API: er som utvecklare har skapat.

I den här videon förklaras Microsofts identitets plattform och grunderna för modern autentisering: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Här är en jämförelse av de protokoll som används av Microsoft Identity Platform:

* **OAuth kontra OpenID Connect**: plattformen använder OAuth för auktorisering och OpenID Connect (OIDC) för autentisering. OpenID Connect är byggt på OAuth 2,0, så terminologin och flödet liknar de två. Du kan även både autentisera en användare (via OpenID Connect) och få åtkomst behörighet till en skyddad resurs som användaren äger (via OAuth 2,0) i en begäran. Mer information finns i [OAuth 2,0-och OpenID Connect-protokoll](active-directory-v2-protocols.md) och [OpenID Connect-protokoll](v2-protocols-oidc.md).
* **OAuth kontra SAML**: plattformen använder OAuth 2,0 för auktorisering och SAML för autentisering. Mer information om hur du använder dessa protokoll tillsammans för att både autentisera en användare och få åtkomst till en skyddad resurs finns i [Microsoft Identity Platform och OAuth 2,0 SAML Bearer-försäkrat flöde](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect jämfört med SAML**: plattformen använder både OpenID Connect och SAML för att autentisera en användare och aktivera enkel inloggning. SAML-autentisering används ofta med identitets leverantörer som Active Directory Federation Services (AD FS) (AD FS) federerade till Azure AD, så den används ofta i företags program. OpenID Connect används ofta för appar som är helt i molnet, till exempel mobilappar, webbplatser och webb-API: er.

## <a name="next-steps"></a>Nästa steg

För andra ämnen som beskriver grundläggande autentisering och auktorisering:

* Information om hur åtkomsttoken, uppdaterings-token och ID-token används vid auktorisering och autentisering finns i [säkerhetstoken](security-tokens.md).
* Information om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform finns i [program modell](application-model.md).