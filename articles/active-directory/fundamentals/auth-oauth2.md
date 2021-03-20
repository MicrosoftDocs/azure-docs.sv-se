---
title: OAUTH 2,0-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå OAUTH 2,0-autentisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172847"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>OAuth 2,0-autentisering med Azure Active Directory

OAuth 2,0 är bransch protokollet för auktorisering. Den låter en användare ge begränsad åtkomst till sina skyddade resurser. OAuth är utformad för att fungera specifikt med Hypertext Transfer Protocol (HTTP), separerar OAuth rollen för klienten från resurs ägaren. Klienten begär åtkomst till de resurser som styrs av resurs ägaren och som finns på resurs servern. Resurs servern utfärdar åtkomsttoken med godkännande av resurs ägaren. Klienten använder åtkomsttoken för att komma åt de skyddade resurserna som finns på resurs servern. 

OAuth 2,0 är direkt relaterad till OpenID Connect (OIDC). Eftersom OIDC är ett lager för autentisering och auktorisering som byggts ovanpå OAuth 2,0 är det inte bakåtkompatibelt med OAuth 1,0. Azure Active Directory (Azure AD) stöder alla OAuth 2,0-flöden. 

## <a name="use-when"></a>Använd när:

För avancerade klient & moderna app-scenarier och RESTful Web API-åtkomst.

![Diagram över arkitektur](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: begär en tjänst från webb programmet (app). Användaren är vanligt vis resurs ägaren som äger data och har behörighet att tillåta klienter att komma åt data eller resursen. 

* **Webbläsare**: den webbläsare som användaren interagerar med är OAuth-klienten. 

* **Webbapp**: webbappen eller resurs servern, är den plats där resursen eller data finns. Den litar på auktoriseringsservern för att autentisera och auktorisera OAuth-klienten på ett säkert sätt. 

* **Azure AD**: Azure AD är auktoriseringsservern, som även kallas identitets leverantör (IdP). Det hanterar säkert vad som helst att göra med användarens information, deras åtkomst och förtroende relationen. Det ansvarar för att utfärda de token som beviljar och återkallar åtkomst till resurser.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementera OAuth 2,0 med Azure AD

* [Integrera program med Azure AD](../saas-apps/tutorial-list.md) 

* [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](../develop/active-directory-v2-protocols.md) 

* [Program typer och OAuth2](../develop/v2-app-types.md) 

