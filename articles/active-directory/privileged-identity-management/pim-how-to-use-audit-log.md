---
title: Visa Gransknings logg rapport för Azure AD-roller i Azure AD PIM | Microsoft Docs
description: Lär dig hur du visar Gransknings logg historiken för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8481c562ecbab1f26e877e55a5a9454695ddf4c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370839"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Visa gransknings historik för Azure AD-roller i Privileged Identity Management

Du kan använda gransknings historiken Privileged Identity Management (PIM) för att se alla roll tilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. Om du vill se en fullständig gransknings historik för aktiviteten i din Azure Active Directory (Azure AD)-organisation, inklusive administratör, slutanvändare och synkroniseringsuppgift, kan du använda [rapporterna Azure Active Directory säkerhets-och aktivitets rapporter](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

    [![Skärm bild som visar sidan "Azure AD-roller-katalog roller gransknings historik".](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Välj fliken för din version")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Ny version](#tab/new)

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

## <a name="view-resource-audit-history"></a>Visa resurs gransknings historik

Resurs granskning ger dig en översikt över all aktivitet som är kopplad till dina Azure AD-roller.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **resurs granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Resurs gransknings lista med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visa min granskning

I min granskning kan du Visa din personliga roll aktivitet.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj den resurs som du vill visa gransknings historik för.

1. Välj **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Gransknings lista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="view-audit-history"></a>Visa granskningshistorik

Följ dessa steg om du vill visa gransknings historiken för Azure AD-roller.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **gransknings historik för katalog roller**.

    Beroende på din gransknings historik visas ett stapeldiagram tillsammans med totalt antal aktiveringar, Max aktiveringar per dag och genomsnittlig aktivering per dag.

    [![Ny version av Azure AD-roller](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Visa gransknings historik för katalog roller")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Längst ned på sidan visas en tabell med information om varje åtgärd i den tillgängliga gransknings historiken. Kolumnerna har följande betydelser:

    | Kolumn | Beskrivning |
    | --- | --- |
    | Tid | När åtgärden utfördes. |
    | Requestor | Användare som begärde roll aktiveringen eller ändringen. Om värdet är **Azure-system** kontrollerar du Azures gransknings historik för mer information. |
    | Action | Åtgärder som vidtas av begär Ande. Åtgärder kan omfatta tilldela, ta bort, aktivera, inaktivera eller AddedOutsidePIM. |
    | Medlem | Användare som aktiverar eller har tilldelats en roll. |
    | Roll | Roll som tilldelats eller Aktiver ATS av användaren. |
    | REA soning | Text som angavs i orsaks fältet under aktiveringen. |
    | Förfallodatum | När en aktive rad roll upphör att gälla. Gäller endast berättigade roll tilldelningar. |

1. Sortera gransknings historiken genom att klicka på knapparna **tid**, **åtgärd** och **roll** .

## <a name="filter-audit-history"></a>Filtrera gransknings historik

1. Klicka på knappen **filter** överst på sidan gransknings historik.

    Fönstret **Parametrar för uppdaterings diagram** visas.

1. I **tidsintervall** väljer du ett tidsintervall.

1. I **roller** markerar du kryss rutorna för att ange de roller som du vill visa.

    ![Fönstret uppdatera diagram parametrar](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Välj **färdig** om du vill visa den filtrerade gransknings historiken.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Hämta orsak, god kännare och biljett nummer för godkännande händelser

1. Logga in på [Azure Portal](https://aad.portal.azure.com) med roll behörigheten privilegierad roll administratör och öppna Azure AD.
1. Välj **Granskningsloggar**.
1. Använd **tjänst** filtret om du endast vill visa gransknings händelser för tjänsten Privileged Identity Management. På sidan **gransknings loggar** kan du:

    - Se orsaken till en gransknings händelse i kolumnen **status orsak** .
    - I kolumnen god kännare i kolumnen **initierad av (aktör)** för händelsen "Lägg till medlem i roll förfrågan har godkänts".

    [![Skärm bild som visar sidan "gransknings loggar" med menyn "initierad av (aktör) öppen och" PIM "vald.](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrera gransknings loggen för PIM-tjänsten")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Välj en Gransknings logg händelse om du vill se biljett numret på fliken **aktivitet** i **informations** fönstret.
  
    [![Skärm bild som visar sidan "gransknings loggar" med biljett numret markerat i fönstret "information".](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Kontrol lera biljett numret för gransknings händelsen")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Du kan visa beställaren (personen som aktiverar rollen) på fliken **mål** i **informations** fönstret för en gransknings händelse. Det finns två mål typer för Azure AD-roller:

    - Rollen (**typ** = roll)
    - Beställaren (**typ** = användare)

Vanligt vis är Gransknings logg händelsen omedelbart ovanför godkännande händelsen en händelse för "Lägg till medlem i rollen slutförd" där den **initierade av (aktör)** är beställaren. I de flesta fall behöver du inte hitta beställaren i begäran om godkännande från ett gransknings perspektiv.

---

## <a name="next-steps"></a>Nästa steg

- [Visa aktivitet och gransknings historik för Azures resurs roller i Privileged Identity Management](azure-pim-resource-rbac.md)
