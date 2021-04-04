---
title: Uppdatera eller ta bort den anpassade rollen för Azure AD – Privileged Identity Management (PIM)
description: Så här uppdaterar eller tar du bort en anpassad roll tilldelning för Azure AD Privileged Identity Management (PIM)
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
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371536"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Uppdatera eller ta bort en tilldelad Azure AD-roll i Privileged Identity Management

Den här artikeln beskriver hur du använder Privileged Identity Management (PIM) för att uppdatera eller ta bort just-in-Time-och Time-Bound till anpassade roller som skapats för program hantering i den administrativa miljön för Azure Active Directory (Azure AD). 

- Mer information om hur du skapar anpassade roller för att delegera program hantering i Azure AD finns i [anpassade administratörs roller i Azure Active Directory (för hands version)](../roles/custom-overview.md). 
- Om du inte har använt Privileged Identity Management ännu kan du få mer information när du [börjar använda Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> Anpassade Azure AD-roller är inte integrerade med de inbyggda katalog rollerna under för hands versionen. När funktionen är allmänt tillgänglig, sker roll hanteringen i den inbyggda roll upplevelsen. Om du ser följande banderoll bör dessa roller hanteras [i den inbyggda roll upplevelsen](pim-how-to-add-role-to-user.md) och den här artikeln gäller inte:
>
> [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Uppdatera eller ta bort en tilldelning

Följ dessa steg om du vill uppdatera eller ta bort en befintlig anpassad roll tilldelning.

1. Logga in på [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) i Azure Portal med ett användar konto som tilldelats rollen privilegie rad roll administratör.
1. Välj **anpassade Azure AD-roller (för hands version)**.

    ![Välj för hands versionen av Azure AD-anpassade roller för att se kvalificerade roll tilldelningar](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Välj **roller** för att se en lista över **tilldelningar** för anpassade roller för Azure AD-program.

    ![Välj roller se listan över kvalificerade roll tilldelningar](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Välj den roll som du vill uppdatera eller ta bort.
1. Hitta roll tilldelningen på flikarna **berättigade roller** eller **aktiva roller** .
1. Välj **Uppdatera** eller **ta bort** om du vill uppdatera eller ta bort roll tilldelningen.

    ![Välj Ta bort eller uppdatera i den berättigade roll tilldelningen](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Tilldela en anpassad Azure AD-roll](azure-ad-custom-roles-assign.md)
- [Konfigurera en anpassad roll tilldelning för Azure AD](azure-ad-custom-roles-configure.md)
