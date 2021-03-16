---
title: Tilldela Azure AD-roller till användare – Azure Active Directory
description: Lär dig hur du beviljar åtkomst till användare i Azure Active Directory genom att tilldela Azure AD-roller.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466689"
---
# <a name="assign-azure-ad-roles-to-users"></a>Tilldela Azure AD-roller till användare

Nu kan du se och hantera alla medlemmar i administratörs rollerna i administrations centret för Azure AD. Om du ofta hanterar roll tilldelningar kommer du förmodligen att föredra den här upplevelsen. Den här artikeln beskriver hur du tilldelar Azure AD-roller med hjälp av administrations Center för Azure AD.

## <a name="assign-a-role"></a>Tilldela en roll

1. Logga in på [administrations Center för Azure AD](https://aad.portal.azure.com) med administratörs behörighet för global administratör eller privilegierad roll.

1. Välj **Azure Active Directory**.

1. Välj **roller och administratörer** om du vill se en lista över alla tillgängliga roller.

    ![Skärm bild av sidan roller och administratörer](./media/manage-roles-portal/roles-and-administrators.png)

1. Välj en roll för att se dess tilldelningar.

    För att hjälpa dig att hitta den roll du behöver kan du med Azure AD Visa under uppsättningar av rollerna baserat på roll kategorier. Kolla in **typ** filtret för att visa att du bara har roller i den valda typen.

1. Välj **Lägg till tilldelningar** och välj sedan de användare som du vill tilldela den här rollen.

    Om du ser något som skiljer sig från följande bild läser du Obs! i [Privileged Identity Management (PIM)](#privileged-identity-management-pim) för att kontrol lera om du använder PIM.

    ![lista över behörigheter för en administratörs roll](./media/manage-roles-portal/add-assignments.png)

1. Välj **Lägg till** för att tilldela rollen.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Du kan välja **Hantera i PIM** för ytterligare hanterings funktioner med hjälp av [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Privilegierade roll administratörer kan ändra "permanent" (alltid aktivt i rollen) tilldelningar till "kvalificerat" (i rollen endast när det har förhöjds). Om du inte har Privileged Identity Management kan du fortfarande välja **Hantera i PIM** för att registrera dig för en utvärderings version. Privileged Identity Management kräver en [Azure AD Premium P2-licens plan](../privileged-identity-management/subscription-requirements.md).

![Skärm bild som visar sidan "User Administrator-tilldelningar" med åtgärden "hantera i PIM" vald](./media/manage-roles-portal/member-list-pim.png)

Om du är global administratör eller administratör för privilegierade roller kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller välja en medlem för att se deras aktiva tilldelade roller.

> [!Note]
> Om du har en Azure AD Premium P2-licens och du redan använder Privileged Identity Management, utförs alla roll hanterings uppgifter i Privileged Identity Management och inte i Azure AD.
>
> ![Azure AD-roller som hanteras i PIM för användare som redan använder PIM och har en Premium P2-licens](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller finns i [inbyggda Azure AD-roller](permissions-reference.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
