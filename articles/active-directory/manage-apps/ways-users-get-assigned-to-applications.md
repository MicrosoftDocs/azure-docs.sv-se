---
title: Förstå hur användare tilldelas till appar i Azure Active Directory
description: Ta reda på hur användare tilldelas en app som använder Azure Active Directory för identitets hantering.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257448"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Förstå hur användare tilldelas till appar i Azure Active Directory
I den här artikeln får du hjälp att förstå hur användare tilldelas ett program i din klient organisation.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hur kommer användarna att tilldelas ett program i Azure AD?
För att en användare ska kunna komma åt ett program måste de först tilldelas till det på något sätt. Tilldelningen kan utföras av en administratör, ett företags ombud eller ibland själva användaren. Nedan beskrivs hur användare kan tilldelas program:

*  En administratör [tilldelar en användare](./assign-user-or-group-access-portal.md) till programmet direkt
*  En administratör [tilldelar en grupp](./assign-user-or-group-access-portal.md) som användaren är medlem i programmet, inklusive:
    * En grupp som har synkroniserats lokalt
    * En statisk säkerhets grupp som skapats i molnet
    * En [dynamisk säkerhets grupp](../enterprise-users/groups-dynamic-membership.md) som skapats i molnet
    * En Microsoft 365 grupp som skapats i molnet
    * Gruppen [alla användare](../fundamentals/active-directory-groups-create-azure-portal.md)
*  En administratör möjliggör [åtkomst till självbetjänings program](./manage-self-service-access.md) så att en användare kan lägga till ett program med [Mina appar](../user-help/my-apps-portal-end-user-access.md) **Lägg till app** -funktion **utan affärs godkännande**
*  En administratör möjliggör [åtkomst till självbetjänings program](./manage-self-service-access.md) så att en användare kan lägga till ett program med hjälp av [Mina appar](../user-help/my-apps-portal-end-user-access.md) **Lägg till app** -funktion, men endast **med tidigare godkännande från en vald uppsättning affärs god kännare**
*  En administratör möjliggör självbetjänings [grupp hantering](../enterprise-users/groups-self-service-management.md) så att en användare kan ansluta till en grupp som ett program har tilldelats till **utan affärs godkännande**
*  En administratör möjliggör självbetjänings [grupp hantering](../enterprise-users/groups-self-service-management.md) så att en användare kan ansluta till en grupp som ett program har tilldelats, men endast **med tidigare godkännande från en vald uppsättning affärs god kännare**
*  En administratör tilldelar en licens till en användare direkt för ett program från den första parten, t. ex. [Microsoft 365](https://products.office.com/)
*  En administratör tilldelar en licens till en grupp som användaren är medlem i till ett program i den första parten, t. ex. [Microsoft 365](https://products.office.com/)
*  En [administratör godkänner ett program](../develop/howto-convert-app-to-be-multi-tenant.md) som används av alla användare och sedan loggar användaren in på programmet
* En användare [samtycker till ett program](../develop/howto-convert-app-to-be-multi-tenant.md) genom att logga in på programmet

## <a name="next-steps"></a>Nästa steg
* [Snabb starts serie för program hantering](view-applications-portal.md)
* [Vad är programhantering?](what-is-application-management.md)
* [Vad är enkel inloggning?](what-is-single-sign-on.md)