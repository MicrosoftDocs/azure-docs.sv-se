---
title: Tilldela Azure AD anpassad roll – Privileged Identity Management (PIM)
description: Så här tilldelar du en anpassad Azure AD-roll för tilldelning Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371655"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Tilldela en anpassad Azure AD-roll i Privileged Identity Management

Den här artikeln beskriver hur du använder Privileged Identity Management (PIM) för att skapa just-in-Time-och Time-Bound-tilldelning till anpassade roller som skapats för att hantera program i den administrativa miljön för Azure Active Directory (Azure AD).

- Mer information om hur du skapar anpassade roller för att delegera program hantering i Azure AD finns i [anpassade administratörs roller i Azure Active Directory (för hands version)](../roles/custom-overview.md).
- Om du inte har använt Privileged Identity Management ännu kan du få mer information när du [börjar använda Privileged Identity Management](pim-getting-started.md).
- Information om hur du beviljar en annan administratörs åtkomst till att hantera Privileged Identity Management finns i [bevilja åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Anpassade Azure AD-roller är inte integrerade med de inbyggda katalog rollerna under för hands versionen. När funktionen är allmänt tillgänglig, sker roll hanteringen i den inbyggda roll upplevelsen. Om du ser följande banderoll bör dessa roller hanteras [i den inbyggda roll upplevelsen](pim-how-to-activate-role.md) och den här artikeln gäller inte:
>
> [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Tilldela en roll

Privileged Identity Management kan hantera anpassade roller som du kan skapa i Azure Active Directory (Azure AD) program hantering.  Följande steg gör en berättigad tilldelning till en anpassad katalog roll.

1. Logga in på [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure Portal med ett användar konto som tilldelats rollen privilegie rad roll administratör.
1. Välj **anpassade Azure AD-roller (för hands version)**.

    ![Välj för hands versionen av Azure AD-anpassade roller för att se kvalificerade roll tilldelningar](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Välj **roller** om du vill visa en lista över anpassade roller för Azure AD-program.

    ![Välj roller se listan över kvalificerade roll tilldelningar](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Välj **Lägg till medlem** för att öppna sidan tilldelning.
1. Om du vill begränsa omfånget för roll tilldelningen till ett enda program väljer du **omfång** för att ange en program omfattning.

    ![begränsa omfattningen av kvalificerade roll tilldelningar i Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Välj **Välj en roll** för att öppna listan **Välj en roll** .

    ![Välj den kvalificerade roll som ska tilldelas en användare](./media/azure-ad-custom-roles-assign/select-role.png)

1. Välj en roll som du vill tilldela och klicka sedan på **Välj**. Listan **Välj en medlem** öppnas.

    ![Välj den användare som du tilldelar rollen](./media/azure-ad-custom-roles-assign/select-member.png)

1. Välj en användare som du vill tilldela rollen och klicka sedan på **Välj**. Listan över **medlemskaps inställningar** öppnas.

    ![Ange roll tilldelnings typen som giltig eller aktiv](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. På sidan **medlemskaps inställningar** väljer du **berättigade** eller **aktiva**:

    - **Berättigade** tilldelningar kräver att användaren har tilldelats rollen för att utföra en åtgärd innan de kan använda rollen. Åtgärder kan omfatta att skicka en Multi-Factor Authentication-kontroll, tillhandahålla en affärs motivering eller begära godkännande från angivna god kännare.
    - **Aktiva** tilldelningar kräver inte att den tilldelade användaren utför någon åtgärd för att använda rollen. Aktiva användare har alla behörigheter som tilldelats rollen.

1. Om den **permanenta** kryss rutan finns tillgänglig och tillgänglig (beroende på roll inställningar) kan du ange om tilldelningen är permanent. Markera kryss rutan om du vill göra tilldelningen permanent eller permanent tilldelad. Avmarkera kryss rutan om du vill ange en tilldelnings varaktighet.
1. Skapa den nya roll tilldelningen genom att klicka på **Spara** och sedan på **Lägg till**. Ett meddelande om tilldelnings processens status visas.

För att verifiera roll tilldelningen i en öppen roll väljer du **tilldelningar**  >  **tilldela** och kontrollerar att roll tilldelningen identifieras korrekt som giltig eller aktiv.

 ![Kontrol lera om roll tilldelningen är synlig som giltig eller aktiv](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Ta bort eller uppdatera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-update-remove.md)
- [Konfigurera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-configure.md)
- [Roll definitioner i Azure AD](../roles/permissions-reference.md)
