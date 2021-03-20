---
title: Godkänn begär Anden för Azure Resource roles i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du godkänner eller nekar begär Anden för Azures resurs roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742087"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management

Med Privileged Identity Management (PIM) i Azure Active Directory (Azure AD) kan du konfigurera roller för att kräva godkännande för aktivering och välja användare eller grupper från din Azure AD-organisation som delegerade god kännare. Vi rekommenderar att du väljer två eller fler god kännare för varje roll för att minska arbets belastningen för den privilegierade roll administratören. Delegerade god kännare har 24 timmar på sig att godkänna begär Anden. Om en begäran inte godkänns inom 24 timmar måste den behöriga användaren skicka en ny begäran igen. Tids perioden för godkännande av 24 timmar kan inte konfigureras.

Följ stegen i den här artikeln för att godkänna eller Neka förfrågningar om Azure-resurs roller.

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad god kännare får du ett e-postmeddelande när en Azure Resource Role-begäran väntar på ditt godkännande. Du kan visa dessa väntande begär anden i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Godkänn begär Anden**.

    ![Sidan Godkänn förfrågningar – Azure-resurser som visar begäran om granskning](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    I avsnittet **begär Anden om roll aktiveringar** visas en lista över begär Anden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Sök efter och välj den begäran som du vill godkänna. Sidan Godkänn eller neka visas.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **Godkänn**. Du får ett Azure-meddelande om ditt godkännande.

    ![Godkänn meddelandet som visar att begäran har godkänts](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Neka förfrågningar

1. Sök efter och välj den begäran som du vill neka. Sidan Godkänn eller neka visas.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **neka**. Ett meddelande visas med ditt avslag.

## <a name="workflow-notifications"></a>Arbets flödes meddelanden

Här är lite information om arbets flödes meddelanden:

- God kännare meddelas via e-post när en begäran om en roll väntar på att granskas. E-postmeddelanden innehåller en direkt länk till begäran, där god kännaren kan godkänna eller neka.
- Begär Anden löses av den första god kännaren som godkänner eller nekar.
- När en god kännare svarar på begäran, meddelas alla god kännare om åtgärden.
- Resurs administratörer meddelas när en godkänd användare blir aktiv i sin roll.

>[!Note]
>En resurs administratör som tror att en godkänd användare inte ska vara aktiv kan ta bort den aktiva roll tilldelningen i Privileged Identity Management. Även om resurs administratörer inte meddelas om väntande begär Anden om de inte är en god kännare, kan de Visa och avbryta väntande begär Anden för alla användare genom att visa väntande begär anden i Privileged Identity Management.

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azures resurs roller i Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [E-postmeddelanden i Privileged Identity Management](pim-email-notifications.md)
- [Godkänn eller neka begär Anden för Azure AD-roller i Privileged Identity Management](azure-ad-pim-approval-workflow.md)
