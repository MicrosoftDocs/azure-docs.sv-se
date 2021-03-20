---
title: Godkänn aktiverings begär Anden för grupp medlemmar och ägare i Privileged Identity Management – Azure AD
description: Lär dig hur du godkänner eller nekar begär Anden för roll tilldelnings bara grupper i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536995"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Godkänn aktiverings begär Anden för privilegierade åtkomst grupper medlemmar och ägare (förhands granskning)

Med Privileged Identity Management (PIM) i Azure Active Directory (Azure AD) kan du konfigurera privilegierade åtkomst grupp medlemmar och ägare för att kräva godkännande för aktivering och välja användare eller grupper från din Azure AD-organisation som delegerade god kännare. Vi rekommenderar att du väljer två eller fler god kännare för varje grupp för att minska arbets belastningen för den privilegierade roll administratören. Delegerade god kännare har 24 timmar på sig att godkänna begär Anden. Om en begäran inte godkänns inom 24 timmar måste den behöriga användaren skicka en ny begäran igen. Tids perioden för godkännande av 24 timmar kan inte konfigureras.

Följ stegen i den här artikeln för att godkänna eller Neka förfrågningar om Azure-resurs roller.

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad god kännare får du ett e-postmeddelande när en Azure Resource Role-begäran väntar på ditt godkännande. Du kan visa väntande begär anden i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Godkänn begär Anden**.

    ![Sidan Godkänn förfrågningar – Azure-resurser som visar begäran om granskning](./media/groups-approval-workflow/groups-select-request.png)

    I avsnittet **begär Anden om roll aktiveringar** visas en lista över begär Anden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Sök efter och välj den begäran som du vill godkänna och välj **Godkänn**.

    ![Skärm bild som visar sidan Godkänn begär Anden med knapparna Godkänn och bekräfta som är markerade.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **Bekräfta**. Ett Azure-meddelande genereras av ditt godkännande.

## <a name="deny-requests"></a>Neka förfrågningar

1. Leta upp och välj den begäran som du vill neka och välj **neka**.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/groups-approval-workflow/groups-confirm-denial.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **Bekräfta**. Ett Azure-meddelande genereras av nekad.

## <a name="workflow-notifications"></a>Arbets flödes meddelanden

Här är lite information om arbets flödes meddelanden:

- God kännare meddelas via e-post när en begäran om en grupp tilldelning väntar på att granskas. E-postmeddelanden innehåller en direkt länk till begäran, där god kännaren kan godkänna eller neka.
- Begär Anden löses av den första god kännaren som godkänner eller nekar.
- När en god kännare svarar på begäran, meddelas alla god kännare om åtgärden.

>[!Note]
>En administratör som anser att en godkänd användare inte ska vara aktiv kan ta bort den aktiva grupp tilldelningen i Privileged Identity Management. Även om resurs administratörer inte meddelas om väntande begär Anden om de inte är en god kännare, kan de Visa och avbryta väntande begär Anden för alla användare genom att visa väntande begär anden i Privileged Identity Management.

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya grupp tilldelningar i Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-postmeddelanden i Privileged Identity Management](pim-email-notifications.md)
- [Godkänn eller neka begär Anden för grupp tilldelningar i Privileged Identity Management](azure-ad-pim-approval-workflow.md)
