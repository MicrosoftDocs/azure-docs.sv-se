---
title: Använd en grupp för att hantera åtkomst till SaaS-appar – Azure AD | Microsoft Docs
description: Så här använder du grupper i Azure Active Directory för att tilldela åtkomst till SaaS-program som är integrerade med Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df36cd334dbb455cf1717bf18fc6c8337d0ee6d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547890"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Använd en grupp för att hantera åtkomst till SaaS-program

Med hjälp av Azure Active Directory (Azure AD) med en Azure AD Premium licens plan kan du använda grupper för att tilldela åtkomst till ett SaaS-program som är integrerat med Azure AD. Om du till exempel vill tilldela åtkomst för marknadsförings avdelningen att använda fem olika SaaS-program kan du skapa en grupp som innehåller användarna på marknadsförings avdelningen och sedan tilldela gruppen till dessa fem SaaS-program som marknadsförings avdelningen behöver. På så sätt kan du spara tid genom att hantera marknadsförings avdelningens medlemskap på ett och samma ställe. Användarna tilldelas sedan programmet när de läggs till som medlemmar i marknadsförings gruppen och har tilldelningar borttagna från programmet när de tas bort från marknadsförings gruppen. Den här funktionen kan användas med hundratals program som du kan lägga till i Azure AD-programgalleriet.

> [!IMPORTANT]
> Du kan bara använda den här funktionen när du har startat en Azure AD Premium utvärderings version eller en licens plan för inköps Azure AD Premium.
> Gruppbaserad tilldelning stöds endast för säkerhets grupper.
> Kapslade gruppmedlemskap stöds inte för gruppbaserad tilldelning till program just nu.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Tilldela åtkomst för en användare eller grupp till ett SaaS-program

1. Välj **företags program** i [administrations centret för Azure AD](https://aad.portal.azure.com).
2. Välj ett program som du har lagt till i program galleriet för att öppna det.
3. Välj **användare och grupper** och välj sedan **Lägg till användare**.
4. I **Lägg till tilldelning** väljer du **användare och grupper** för att öppna urvals listan **användare och grupper** .
6. Välj så många grupper eller användare som du vill, klicka eller tryck sedan på **Välj** för att lägga till dem i listan **Lägg till tilldelning** . Du kan också tilldela en roll till en användare i det här skedet.
7. Välj **tilldela** för att tilldela användare eller grupper till det valda företags programmet.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](../enterprise-users/groups-settings-cmdlets.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
