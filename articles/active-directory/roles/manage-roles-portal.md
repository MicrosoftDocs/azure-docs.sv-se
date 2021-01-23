---
title: Visa och tilldela administratörs roll behörigheter – Azure AD | Microsoft Docs
description: Nu kan du se och hantera medlemmar i en administratörs roll för Azure AD i portalen. För dem som ofta hanterar roll tilldelningar.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26217930b79b958ae86d976d06a28ba4a4852ab6
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742037"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Visa och tilldela administratörsroller i Azure Active Directory

Nu kan du se och hantera alla medlemmar i administratörs rollerna i Azure Active Directory portalen. Om du ofta hanterar roll tilldelningar kommer du förmodligen att föredra den här upplevelsen. Och om du skulle undra "vad de här rollerna egentligen gör?" kan du se en detaljerad lista över behörigheter för var och en av administratörs rollerna för Azure AD.

## <a name="view-all-roles"></a>Visa alla roller

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Azure Active Directory**.

1. Välj **roller och administratörer** om du vill se en lista över alla tillgängliga roller.

1. Välj ellipsen till höger om varje rad för att se behörigheterna för rollen. Välj en roll om du vill visa de användare som har tilldelats rollen. Om något skiljer sig från följande bild läser du anteckningen i [Visa tilldelningar för privilegierade roller](#view-assignments-for-privileged-roles) för att kontrol lera om du är i PRIVILEGED Identity Management (PIM).

    ![lista över roller i Azure AD Portal](./media/manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Visa mina roller

Det är enkelt att visa dina egna behörigheter. Välj **din roll** på sidan **roller och administratörer** för att se de roller som för närvarande är tilldelade till dig.

## <a name="view-assignments-for-privileged-roles"></a>Visa tilldelningar för privilegierade roller

Du kan välja **Hantera i PIM** för ytterligare hanterings funktioner. Privilegierade roll administratörer kan ändra "permanent" (alltid aktivt i rollen) tilldelningar till "kvalificerat" (i rollen endast när det har förhöjds). Om du inte har Privileged Identity Management kan du fortfarande välja **Hantera i PIM** för att registrera dig för en utvärderings version. Privileged Identity Management kräver en [Azure AD Premium P2-licens plan](../privileged-identity-management/subscription-requirements.md).

![lista över medlemmar i en administratörs roll](./media/manage-roles-portal/member-list.png)

Om du är global administratör eller administratör för privilegierade roller kan du enkelt lägga till eller ta bort medlemmar, filtrera listan eller välja en medlem för att se deras aktiva tilldelade roller.

> [!Note]
> Om du har en Azure AD Premium P2-licens och du redan använder Privileged Identity Management, utförs alla roll hanterings uppgifter i Privileged Identity Management och inte i Azure AD.
>
> ![Azure AD-roller som hanteras i PIM för användare som redan använder PIM och har en Premium P2-licens](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Visa en användares roll behörigheter

När du visar en Rolls medlemmar väljer du **Beskrivning** för att se en fullständig lista över behörigheter som beviljats av roll tilldelningen. Sidan innehåller länkar till relevant dokumentation för att hjälpa dig att hantera katalog roller.

![Skärm bild som visar sidan "global administratörs Beskrivning".](./media/manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Hämta roll tilldelningar

Om du vill hämta alla tilldelningar för en speciell roll väljer du en roll på sidan **roller och administratörer** och väljer sedan **Hämta roll tilldelningar**. En CSV-fil som visar tilldelningar för alla omfattningar för rollen hämtas.

![Hämta alla tilldelningar för en roll](./media/manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Tilldela en roll

1. Logga in på [Azure Portal](https://portal.azure.com) med behörigheterna global administratör eller privilegie rad roll administratör och välj **Azure Active Directory**.

1. Välj **roller och administratörer** om du vill se en lista över alla tillgängliga roller.

1. Välj en roll för att se dess tilldelningar.

    ![Skärm bild som visar sidan "User Administrator-tilldelningar" med åtgärden "hantera i PIM" vald.](./media/manage-roles-portal/member-list.png)

1. Välj **Lägg till tilldelningar** och välj de roller som du vill tilldela. Du kan välja **Hantera i PIM** för ytterligare hanterings funktioner. Om något skiljer sig från följande bild läser du anteckningen i [Visa tilldelningar för privilegierade roller](#view-assignments-for-privileged-roles) för att kontrol lera om du är i Pim.

    ![lista över behörigheter för en administratörs roll](./media/manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
