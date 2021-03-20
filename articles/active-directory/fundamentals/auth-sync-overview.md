---
title: Översikt över Azure Active Directory Authentication and Synchronization Protocol
description: Arkitektur vägledning om hur du integrerar Azure AD med äldre autentiseringsprotokoll och Sync-mönster
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441206"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory-integration med autentiserings-och protokoll för synkronisering

Microsoft Azure Active Directory (Azure AD) möjliggör integrering med många autentiserings-och protokoll för synkronisering. Med hjälp av integreringen av autentisering kan du använda Azure AD och dess säkerhets-och hanterings funktioner med små eller inga ändringar i dina program som använder äldre autentiseringsmetoder. Med synkroniseringen av synkronisering kan du synkronisera användar-och grupp data till Azure AD och sedan användar hanterings funktionerna för Azure AD. Vissa Sync-mönster aktiverar även automatisk etablering.

## <a name="legacy-authentication-protocols"></a>Bakåtkompatibla autentiseringsprotokoll

I följande tabell visas autentisering av Azure AD-integrering med äldre autentiseringsprotokoll och deras funktioner. Välj namnet på ett autentiseringsprotokoll som ska visas

* En detaljerad beskrivning

* När du ska använda detta

* Arkitektur diagram

* Förklaring av system komponenter

* Länkar till hur du implementerar integreringen

 

| Autentiseringsprotokoll| Autentisering| Auktorisering| Multifaktorautentisering| Villkorlig åtkomst |
| - |- | - | - | - |
| [Huvudbaserad autentisering](auth-header-based.md)|![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [LDAP-autentisering](auth-ldap.md)| ![Bock markering](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0-autentisering](auth-oauth2.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [OIDC-autentisering](auth-oidc.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Lösenordsbaserad SSO-autentisering](auth-password-based-sso.md )| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [RADIUS-autentisering]( auth-radius.md)| ![Bock markering](./media/authentication-patterns/check.png)| | ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Tjänster för fjärrskrivbordsgateway](auth-remote-desktop-gateway.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![Bock markering](./media/authentication-patterns/check.png)| | ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [SAML-autentisering](auth-saml.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |
| [Windows-autentisering – Kerberos-begränsad delegering](auth-kcd.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Mönster för synkronisering

I följande tabell presenteras Azure AD-integrering med synkroniseringsfel och deras funktioner. Välj namnet på ett mönster som ska visas

* En detaljerad beskrivning

* När du ska använda detta

* Arkitektur diagram

* Förklaring av system komponenter

* Länkar till hur du implementerar integreringen



| Mönster för synkronisering| Katalogsynkronisering| Användaretablering |
| - | - | - |
| [Katalogsynkronisering](sync-directory.md)| ![Bock markering](./media/authentication-patterns/check.png)|  |
| [LDAP-synkronisering](sync-ldap.md)| ![Bock markering](./media/authentication-patterns/check.png)|  |
| [SCIM-synkronisering](sync-scim.md)| ![Bock markering](./media/authentication-patterns/check.png)| ![Bock markering](./media/authentication-patterns/check.png) |

