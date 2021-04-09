---
title: Bevilja åtkomst för att hantera PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du beviljar åtkomst till andra administrationer för att hantera Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835211"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delegera åtkomst till Privileged Identity Management

För att kunna delegera åtkomst till Privileged Identity Management (PIM) kan en global administratör tilldela andra användare till rollen privilegie rad roll administratör. Som standard har säkerhets administratörer och säkerhets läsare skrivskyddad åtkomst till Privileged Identity Management. För att bevilja åtkomst till Privileged Identity Management kan den första användaren tilldela andra till rollen **administratör för privilegie rad roll** . Rollen administratör för privilegie rad roll krävs endast för hantering av Azure AD-roller. Administratörs behörighet för privilegierade roller krävs inte för att hantera inställningar för Azure-resurser.

> [!NOTE]
> Hantering av Privileged Identity Management kräver Azure AD Multi-Factor Authentication. Eftersom Microsoft-konton inte kan registreras för Azure AD Multi-Factor Authentication, kan en användare som loggar in med ett Microsoft-konto inte komma åt Privileged Identity Management.

Se till att det alltid finns minst två användare i rollen administratör för privilegierad roll, om en användare är utelåst eller om kontot har tagits bort.

## <a name="delegate-access-to-manage-pim"></a>Delegera åtkomst för att hantera PIM

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Privileged Identity Management** i Azure AD.

1. Välj **Azure AD-roller**.

1. Välj **roller**.

    ![Privileged Identity Management Azure AD-roller – roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Välj rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

    ![Privilegie rad roll administratör – medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Välj **Lägg till medlem**  för att öppna fönstret **Lägg till hanterade medlemmar** .

1. Välj **Välj medlemmar** för att öppna fönstret **Välj medlemmar** .

    ![Administratör för privilegie rad roll – Välj medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Välj en medlem och klicka sedan på **Välj**.

1. Välj **OK** för att göra medlemmen berättigad till rollen **privilegie rad roll administratör** .

    När du tilldelar en ny roll till någon i Privileged Identity Management konfigureras de automatiskt som **giltiga** för att aktivera rollen.

1. Om du vill göra medlemmen permanent väljer du användaren i medlems listan privilegierad roll administratör.

1. Välj **mer** och **gör permanent** för att göra tilldelningen permanent.

    ![Privilegie rad roll administratör – gör permanenta](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Skicka en länk till användaren för att [börja använda Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Ta bort åtkomst till hantera PIM

Innan du tar bort någon från rollen administratör för privilegie rad roll ser du alltid till att det fortfarande finns minst två användare som är tilldelade till den.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **roller**.

1. Välj rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

1. Markera kryss rutan bredvid den användare som du vill ta bort och välj sedan **ta bort medlem**.

    ![Privilegie rad roll administratör – ta bort medlem](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. När du uppmanas att bekräfta att du vill ta bort medlemmen från rollen väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Börja använda Privileged Identity Management](pim-getting-started.md)
