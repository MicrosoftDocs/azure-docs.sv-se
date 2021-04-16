---
title: Förstå hur användare tilldelas till appar i Azure Active Directory
description: Förstå hur användare tilldelas till en app som använder Azure Active Directory för identitetshantering.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: 84700bca6ff306dbcce01a837c312c4c0c90066d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376417"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Förstå hur användare tilldelas till appar i Azure Active Directory
Den här artikeln hjälper dig att förstå hur användare tilldelas till ett program i din klientorganisation.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur tilldelas användare till ett program i Azure AD?
För att en användare ska kunna komma åt ett program måste de först tilldelas till det på något sätt. Tilldelningen kan utföras av en administratör, en företagsdelegat eller ibland av användaren själv. Nedan beskrivs hur användare kan tilldelas till program:

*  En administratör [tilldelar en användare](./assign-user-or-group-access-portal.md) till programmet direkt
*  En administratör [tilldelar en](./assign-user-or-group-access-portal.md) grupp som användaren är medlem i i programmet, inklusive:
    * En grupp som synkroniserades från en lokal plats
    * En statisk säkerhetsgrupp som skapats i molnet
    * En [dynamisk säkerhetsgrupp](../enterprise-users/groups-dynamic-membership.md) som skapats i molnet
    * En Microsoft 365 grupp som skapats i molnet
    * Gruppen [Alla](../fundamentals/active-directory-groups-create-azure-portal.md) användare
*  En administratör aktiverar [programåtkomst via självbetjäning](./manage-self-service-access.md) så att en användare kan lägga till ett program med hjälp [Mina appar](../user-help/my-apps-portal-end-user-access.md) **lägg till app** utan **företagsgodkännande**
*  En administratör aktiverar [programåtkomst](./manage-self-service-access.md) via självbetjäning så att en användare kan lägga till ett program med [hjälp av Mina appar-funktionen](../user-help/my-apps-portal-end-user-access.md) Lägg till **app,** men endast med förhandsgodkännande från en vald uppsättning företags **godkännare**
*  En administratör aktiverar [grupphantering med självbetjäning så](../enterprise-users/groups-self-service-management.md) att en användare kan ansluta till en grupp som ett program har tilldelats **utan företagsgodkännande**
*  En administratör aktiverar [grupphantering med självbetjäning](../enterprise-users/groups-self-service-management.md) så att en användare kan ansluta till en grupp som ett program har tilldelats, men endast med förhandsgodkännande från en vald uppsättning **företags godkännare**
*  En administratör tilldelar en licens till en användare direkt för ett program från första part, till exempel [Microsoft 365](https://products.office.com/)
*  En administratör tilldelar en licens till en grupp som användaren är medlem i i ett program från första part, till exempel [Microsoft 365](https://products.office.com/)
*  En [administratör samtycker till att ett](../develop/howto-convert-app-to-be-multi-tenant.md) program ska användas av alla användare och sedan loggar en användare in i programmet
* En användare [godkänner själva ett program](../develop/howto-convert-app-to-be-multi-tenant.md) genom att logga in i programmet

## <a name="next-steps"></a>Nästa steg
* [Snabbstartsserie om programhantering](view-applications-portal.md)
* [Vad är programhantering?](what-is-application-management.md)
* [Vad är enkel inloggning?](what-is-single-sign-on.md)