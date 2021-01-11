---
title: Autentiseringsprotokoll för Microsoft Identity Platform
description: En översikt över de autentiseringsprotokoll som stöds av Microsoft Identity Platform
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: ffd9b415d3b2a8e1aa716caa0e8289c6ae127b71
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065090"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Autentiseringsprotokoll för Microsoft Identity Platform

Microsoft Identity Platform stöder flera av de vanligaste protokollen för autentisering och auktorisering. Ämnena i det här avsnittet beskriver de protokoll som stöds och deras implementering i Microsoft Identity Platform. I avsnitten ingår en granskning av de anspråks typer som stöds, en introduktion till användningen av federationsmetadata, detaljerad OAuth 2,0. och ett fel söknings avsnitt med SAML 2,0-protokoll referens dokumentation.

## <a name="authentication-protocols-articles-and-reference"></a>Artiklar och referens för autentiseringsprotokoll

* [Viktig information om förnyelse av signerings nyckel i Microsoft Identity Platform](active-directory-signing-key-rollover.md) – lär dig mer om Microsofts identitets plattforms förnyelse av takt för signerings nyckel, ändringar som du kan göra för att uppdatera nyckeln automatiskt och diskussion om hur du uppdaterar de vanligaste program scenarierna.
* [Token och anspråks typer som stöds](id-tokens.md) – Läs mer om anspråk i de token som problem med Microsoft Identity Platform.
* [Oauth 2,0 i Microsoft Identity Platform](v2-oauth2-auth-code-flow.md) – lär dig mer om implementeringen av OAuth 2,0 i Microsoft Identity Platform.
* [OpenID Connect 1,0](v2-protocols-oidc.md) – lär dig hur du använder OAuth 2,0, ett Authorization-protokoll, för autentisering.
* [Tjänst-till-tjänst-anrop med klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md) – lär dig hur du använder OAuth 2,0-klientens autentiseringsuppgifter för att tilldela tjänst-till-tjänst-anrop.
* [Tjänst-till-tjänst-anrop med on-of-Flow](v2-oauth2-on-behalf-of-flow.md) – lär dig hur du använder OAuth 2,0 på uppdrag av Flow för tjänst-till-tjänst-anrop.
* [Referens för SAML-protokoll](active-directory-saml-protocol-reference.md) – lär dig mer om de enkla Sign-On och enkel inloggnings-SAML-profilerna för Microsoft Identity Platform.

## <a name="see-also"></a>Se även

* [Översikt över Microsoft Identity Platform](v2-overview.md)
* [Active Directory kod exempel](sample-v2-code.md)
