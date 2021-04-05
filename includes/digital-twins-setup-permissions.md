---
author: baanders
description: inkludera fil för behörighets krav i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205510"
---
## <a name="prerequisites-permission-requirements"></a>Krav: behörighets krav

För att kunna slutföra alla steg i den här artikeln måste du ha en [roll i din prenumeration](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) som har följande behörigheter:
* Skapa och hantera Azure-resurser
* Hantera användar åtkomst till Azure-resurser (inklusive beviljande och delegering av behörigheter)

Vanliga roller som uppfyller detta krav är *ägare*, *konto administratör* eller kombinationen av *användar åtkomst administratör* och *deltagare*. En fullständig förklaring av roller och behörigheter, inklusive vilka behörigheter som ingår i andra roller, finns i [*klassiska prenumerations administratörs roller, Azure-roller och Azure AD-roller*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) i Azure RBAC-dokumentationen.

Om du vill visa din roll i din prenumeration går du till [sidan prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal (du kan använda den här länken eller leta efter *prenumerationer* med Portal Sök fältet). Leta efter namnet på den prenumeration som du använder och Visa rollen för den i kolumnen *min roll* :

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Vy av sidan prenumerationer i Azure Portal, som visar användare som ägare" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Om du upptäcker att värdet är *deltagare* eller en annan roll som inte har de *behörigheter som anges* ovan, kan du kontakta användaren på prenumerationen som har dessa behörigheter (till exempel en prenumerations ägare eller konto administratör) och fortsätta på något av följande sätt:
* Begär att de slutför stegen i den här artikeln för din räkning
* Begär att de höjer din roll i prenumerationen så att du får behörighet att fortsätta. Huruvida detta är lämpligt beror på din organisation och din roll i den.