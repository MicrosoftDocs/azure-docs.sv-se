---
title: Exchange-scenario för Microsoft Identity Platform-token med SAML och OIDC/OAuth i Azure Active Directory
description: Lär dig mer om vanliga scenarier för token Exchange när du arbetar med SAML och OIDC/OAuth i Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582357"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Microsoft Identity Platform token Exchange-scenarier med SAML och OIDC/OAuth

SAML och OpenID Connect (OIDC)/OAuth är populära protokoll som används för att implementera enkel Sign-On (SSO). Vissa appar kan bara implementera SAML och andra kan bara implementera OIDC/OAuth. Båda protokollen använder tokens för att kommunicera hemligheter. Mer information om SAML finns i [Single Sign-On SAML-protokollet](single-sign-on-saml-protocol.md). Mer information om OIDC/OAuth finns i [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](active-directory-v2-protocols.md).

Den här artikeln beskriver ett vanligt scenario där en app implementerar SAML men anropar Graph API, som använder OIDC/OAuth. Grundläggande vägledning finns för personer som arbetar med det här scenariot.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scenario: du har en SAML-token och vill anropa Graph API
Många appar implementeras med SAML. Graph API använder dock protokollen OIDC/OAuth. Det är möjligt, men inte trivial, att lägga till OIDC/OAuth-funktioner i en SAML-app. När OAuth-funktioner är tillgängliga i en app kan Graph API användas.

Den allmänna strategin är att lägga till OIDC/OAuth-stacken i din app. Med din app som implementerar båda standarderna kan du använda en sessions-cookie. Du utbyter inte en token uttryckligen. Du loggar en användare i med SAML, som genererar en sessions-cookie. När Graph API anropar ett OAuth-flöde använder du sessionens cookie för att autentisera. Den här strategin förutsätter att de villkorliga åtkomst kontrollerna godkänns och att användaren är auktoriserad.

> [!NOTE]
> Det rekommenderade biblioteket för att lägga till OIDC/OAuth-beteende är Microsoft Authentication Library (MSAL). Mer information om MSAL finns i [Översikt över Microsoft Authentication Library (MSAL)](msal-overview.md). Föregående bibliotek kallades Active Directory-autentiseringsbibliotek (ADAL), men rekommenderas inte eftersom MSAL ersätter det.

## <a name="next-steps"></a>Nästa steg
- [Autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
