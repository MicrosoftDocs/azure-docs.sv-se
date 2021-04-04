---
title: SAML-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå SAML-autentisering med Azure Active Directory
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
ms.openlocfilehash: 7cd2aa5e9ff8cbaeead69f11d2e3de7f760b53ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168652"
---
# <a name="saml-authentication-with-azure-active-directory"></a>SAML-autentisering med Azure Active Directory

Security Assertion Markup Language (SAML) är en öppen standard för att utbyta autentiserings-och auktoriseringsdata mellan en identitets leverantör och en tjänst leverantör. SAML är ett XML-baserat kodspråk för säkerhets kontroller, som är instruktioner som tjänst leverantörer använder för att fatta beslut om åtkomst kontroll. 

SAML-specifikationen definierar tre roller:

* Huvudobjektet, vanligt vis en användare
* Identitets leverantören (IdP)
* Tjänste leverantören (SP)


## <a name="use-when"></a>Använd när

Det finns ett behov av att tillhandahålla enkel inloggning (SSO) för ett Enterprise SAML-program.

En av de viktigaste användnings fallen är att SAML-adresser är SSO, särskilt genom att utöka SSO över säkerhets domäner, men det finns även andra användnings fall (kallas profiler). 

![arkitektur diagram för SAML](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: begär en tjänst från programmet.

* **Webbläsare**: komponenten som användaren interagerar med.

* **Webbapp**: företags program som stöder SAML och använder Azure AD som IDP.

* **Token**: en SAML-kontroll (kallas även SAML-token) som utför uppsättningar av anspråk gjorda av IDP om principen (användaren). Den innehåller information om autentisering, attribut och auktorisering.

* **Azure AD**: Enterprise Cloud-IDP som ger enkel inloggning och Multi-Factor Authentication för SAML-appar. Den synkroniserar, underhåller och hanterar identitets information för användare samtidigt som de tillhandahåller autentiserings tjänster för att förlita sig på program. 

## <a name="implement-saml-authentication-with-azure-ad"></a>Implementera SAML-autentisering med Azure AD

* [Självstudier för att integrera SaaS-program med hjälp av Azure Active Directory](../saas-apps/tutorial-list.md) 

* [Konfigurera SAML-baserad enkel inloggning för program som inte är gallerier](../manage-apps/add-application-portal.md) 

* [Så här använder Azure AD SAML-protokoll](../develop/active-directory-saml-protocol-reference.md)