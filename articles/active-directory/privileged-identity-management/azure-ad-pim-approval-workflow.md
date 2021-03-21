---
title: Godkänn eller neka begär Anden för Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du godkänner eller nekar begär Anden för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94980595"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Godkänn eller neka begär Anden för Azure AD-roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du konfigurera roller för att kräva godkännande för aktivering och välja en eller flera användare eller grupper som delegerade god kännare. Delegerade god kännare har 24 timmar på sig att godkänna begär Anden. Om en begäran inte godkänns inom 24 timmar måste den behöriga användaren skicka en ny begäran igen. Tids perioden för godkännande av 24 timmar kan inte konfigureras.

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-roll delen av Privileged Identity Management till en ny version som matchar upplevelserna för Azure-roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

    [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att godkänna eller neka begär Anden för Azure AD-roller.

## <a name="new-version"></a>[Ny version](#tab/new)

### <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad god kännare får du ett e-postmeddelande när en begäran om Azure AD-roll väntar på ditt godkännande. Du kan visa dessa väntande begär anden i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Godkänn begär Anden**.

    ![Sidan Godkänn begär Anden – sidan visar begäran om att granska Azure AD-roller](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    I avsnittet **begär Anden om roll aktiveringar** visas en lista över begär Anden som väntar på ditt godkännande.

### <a name="approve-requests"></a>Godkänn förfrågningar

1. Sök efter och välj den begäran som du vill godkänna. Sidan Godkänn eller neka visas.

    ![Skärm bild som visar sidan "Godkänn förfrågningar – Azure AD-roller".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **Godkänn**. Du får ett Azure-meddelande om ditt godkännande.

    ![Godkänn meddelandet som visar att begäran har godkänts](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Neka förfrågningar

1. Sök efter och välj den begäran som du vill neka. Sidan Godkänn eller neka visas.

    ![Rutan Godkänn begär Anden – Godkänn eller neka i fönstret med information och motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. I rutan **motivering** anger du affärs justeringen.

1. Välj **neka**. Ett meddelande visas med ditt avslag.

### <a name="workflow-notifications"></a>Arbets flödes meddelanden

Här är lite information om arbets flödes meddelanden:

- God kännare meddelas via e-post när en begäran om en roll väntar på att granskas. E-postmeddelanden innehåller en direkt länk till begäran, där god kännaren kan godkänna eller neka.
- Begär Anden löses av den första god kännaren som godkänner eller nekar.
- När en god kännare svarar på begäran, meddelas alla god kännare om åtgärden.
- Globala administratörer och administratörer för privilegierade roller meddelas när en godkänd användare blir aktiv i sin roll.

>[!NOTE]
>En global administratör eller en privilegie rad roll administratör som anser att en godkänd användare inte ska vara aktiv kan ta bort den aktiva roll tilldelningen i Privileged Identity Management. Även om administratörer inte meddelas om väntande begär Anden om de inte är god kännare, kan de Visa och avbryta väntande begär Anden för alla användare genom att visa väntande begär anden i Privileged Identity Management.

## <a name="previous-version"></a>[Tidigare version](#tab/previous)

### <a name="view-pending-requests"></a>Visa väntande begäranden

Som en delegerad god kännare får du ett e-postmeddelande när en begäran om Azure AD-roll väntar på ditt godkännande. Du kan visa dessa väntande begär anden i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Godkänn begär Anden**.

    ![Azure AD-roller – Godkänn begär Anden](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Du ser en lista över begär Anden som väntar på ditt godkännande.

### <a name="approve-requests"></a>Godkänn förfrågningar

1. Välj de begär Anden som du vill godkänna och klicka sedan på **Godkänn** för att öppna fönstret Godkänn valda begär Anden.

    ![Listan Godkänn begär Anden med alternativet Godkänn markerat](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Skriv en orsak i rutan **Godkänn orsak** .

    ![Godkänn fönstret valda begär Anden med en Godkänn orsak](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicka på **Godkänn**.

    Status symbolen kommer att uppdateras med ditt godkännande.

    ![Rutan Godkänn valda begär Anden efter att en Godkänn-knapp har klickats](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Neka förfrågningar

1. Välj de begär Anden som du vill neka och klicka sedan på **neka** för att öppna fönstret neka valda begär Anden.

    ![Listan Godkänn begär Anden med alternativet neka markerat](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Skriv en orsak i rutan **orsak till neka** .

    ![Neka fönstret valda begär Anden med en orsak till neka](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Välj **neka**.

    Status symbolen kommer att uppdateras med ditt avslag.

---

## <a name="next-steps"></a>Nästa steg

- [E-postmeddelanden i Privileged Identity Management](pim-email-notifications.md)
- [Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management](pim-resource-roles-approval-workflow.md)
