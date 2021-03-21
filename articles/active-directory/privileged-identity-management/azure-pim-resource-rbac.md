---
title: Visa gransknings rapport för Azure Resource roles i Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Visa aktivitet och gransknings historik för Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002501"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Visa aktivitet och gransknings historik för Azures resurs roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du Visa aktivitet, aktiveringar och gransknings historik för Azure-resurser i din organisation. Detta omfattar prenumerationer, resurs grupper och till och med virtuella datorer. Alla resurser i Azure Portal som utnyttjar Azures rollbaserade åtkomst kontroll funktioner kan dra nytta av funktionerna för säkerhet och livs cykel hantering i Privileged Identity Management.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

## <a name="view-activity-and-activations"></a>Visa aktivitet och aktiveringar

Om du vill se vilka åtgärder en viss användare har tagit i olika resurser kan du Visa den Azure Resource-aktivitet som är associerad med en viss aktiverings period.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa aktivitet och aktivering för.

1. Välj **roller** eller **medlemmar**.

1. Välj en användare.

    Du ser en sammanfattning av användarens åtgärder i Azure-resurser efter datum. Den visar även de senaste roll aktiveringarna under samma tids period.

    ![Användar information med sammanfattning av resurs aktivitet och roll aktiveringar](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Välj en speciell roll aktivering om du vill visa information och motsvarande Azure Resource-aktivitet som uppstod när användaren var aktiv.

    [![Roll aktivering har valts och aktivitets information](media/azure-pim-resource-rbac/export-membership.png "Roll aktivering har valts och aktivitets information")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Exportera roll tilldelningar med underordnade

Du kan ha ett krav på efterlevnad där du måste ange en fullständig lista över roll tilldelningar till granskare. Med Privileged Identity Management kan du fråga roll tilldelningar för en speciell resurs, vilket innefattar roll tilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över roll tilldelningar för en prenumeration och de behövde exportera roll tilldelningar för varje enskild resurs. Med hjälp av Privileged Identity Management kan du fråga efter alla aktiva och berättigade roll tilldelningar i en prenumeration, inklusive roll tilldelningar för alla resurs grupper och resurser.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill exportera roll tilldelningar för, till exempel en prenumeration.

1. Välj **medlemmar**.

1. Välj **Exportera** för att öppna fönstret Exportera medlemskap.

    [![Exportera medlemskaps fönstret för att exportera alla medlemmar](media/azure-pim-resource-rbac/export-membership.png "Exportera medlemskaps sidan för att exportera alla medlemmar")](media/azure-pim-resource-rbac/export-membership.png)

1. Välj **Exportera alla medlemmar** för att exportera alla roll tilldelningar i en CSV-fil.

    ![Exporterade roll tilldelningar i CSV-fil som visas i Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visa resurs gransknings historik

Resurs granskning ger dig en översikt över alla roll aktiviteter för en resurs.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa gransknings historik för.

1. Välj **resurs granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    [![Resurs gransknings lista med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png "Resurs gransknings lista med filter")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. För **gransknings typ** väljer du **Aktivera (tilldelad + aktive rad)**.

    [![Resurs gransknings listan filtrerad efter aktivera gransknings typ](media/azure-pim-resource-rbac/rbac-audit-activity.png "Resurs gransknings lista filtrerad efter aktivering")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ Resurs gransknings lista som filtrerats efter aktivera gransknings typ](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Under **åtgärd** klickar du på **(aktivitet)** för en användare för att se användarens aktivitets information i Azure-resurser.

    ![Information om användar aktiviteter för en viss åtgärd](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visa min granskning

I min granskning kan du Visa din personliga roll aktivitet.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa gransknings historik för.

1. Välj **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    [![Gransknings lista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png "Gransknings lista för den aktuella användaren")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Åtkomst till gransknings historik kräver antingen en global administratör eller en privilegie rad roll administratörs roll.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Hämta orsak, god kännare och biljett nummer för godkännande händelser

1. Logga in på [Azure Portal](https://aad.portal.azure.com) med roll behörigheten privilegierad roll administratör och öppna Azure AD.
1. Välj **Granskningsloggar**.
1. Använd **tjänst** filtret om du endast vill visa gransknings händelser för tjänsten Privileged Identity Management. På sidan **gransknings loggar** kan du:

    - Se orsaken till en gransknings händelse i kolumnen **status orsak** .
    - I kolumnen god kännare i kolumnen **initierad av (aktör)** för händelsen "Lägg till medlem i roll förfrågan har godkänts".

    [![Filtrera gransknings loggen för PIM-tjänsten](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrera gransknings loggen för PIM-tjänsten")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Välj en Gransknings logg händelse om du vill se biljett numret på fliken **aktivitet** i **informations** fönstret.
  
    [![Kontrol lera biljett numret för gransknings händelsen](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Kontrol lera biljett numret för gransknings händelsen")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Du kan visa beställaren (personen som aktiverar rollen) på fliken **mål** i **informations** fönstret för en gransknings händelse. Det finns tre mål typer för Azure-resurs roller:

    - Rollen (**typ** = roll)
    - Beställaren (**typ** = annan)
    - God kännaren (**typ** = användare)

    [![Kontrol lera måltypen](media/azure-pim-resource-rbac/audit-event-target-type.png "Kontrol lera måltypen")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Vanligt vis är logg händelsen omedelbart ovanför godkännande händelsen en händelse för "Lägg till medlem i rollen slutförd" där den **initierade av (aktör)** är beställaren. I de flesta fall behöver du inte hitta beställaren i begäran om godkännande från ett gransknings perspektiv.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Visa gransknings historik för Azure AD-roller i Privileged Identity Management](pim-how-to-use-audit-log.md)
