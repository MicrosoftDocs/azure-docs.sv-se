---
title: Tilldela kvalificerade ägare och medlemmar för privilegierade åtkomst grupper – Azure Active Directory
description: Lär dig hur du tilldelar kvalificerade ägare eller medlemmar i en roll tilldelnings bara grupp i Azure AD Privileged Identity Management (PIM).
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
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534428"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Tilldela berättigande till en privilegie rad åtkomst grupp (för hands version) i Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan hjälpa dig att hantera kvalifikationer och aktivering av tilldelningar till privilegierade åtkomst grupper i Azure AD. Du kan tilldela behörighet till medlemmar eller ägare av gruppen.

>[!NOTE]
>Alla användare som är berättigade till medlemskap i eller ägande rätt till en privilegie rad åtkomst grupp måste ha en Azure AD Premium P2-licens. Mer information finns i [licens krav för att använda Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Tilldela en ägare eller medlem i en grupp

Följ de här stegen för att göra en användare berättigad att vara medlem eller ägare av en privilegie rad åtkomst grupp.

1. [Logga in på Azure AD](https://aad.portal.azure.com) med behörigheter för global administratör eller grupp ägare.
1. Välj **grupper** och välj sedan den roll tilldelnings bara grupp som du vill hantera. Du kan söka eller filtrera listan.

    ![Hitta en roll tilldelnings bara grupp att hantera i PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Öppna gruppen och välj **privilegie rad åtkomst (förhands granskning)**.

    ![Öppna Privileged Identity Managements upplevelsen](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Välj **Lägg till tilldelningar**.

    ![Nytt tilldelnings fönster](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Välj de medlemmar eller ägare som du vill göra berättigade till gruppen privilegierad åtkomst.

    ![Skärm bild som visar sidan "Lägg till tilldelningar" med rutan "Välj en medlem eller grupp" öppen och knappen "Välj" markerad.](./media/groups-assign-member-owner/add-assignments.png)

1. Välj **Nästa** för att ange medlemskap eller ägarskaps varaktighet.

    ![Välj en medlem eller grupp fönster](./media/groups-assign-member-owner/assignment-duration.png)

1. I listan **tilldelnings typ** väljer du **kvalificerad** eller **aktiv**. Privilegierade åtkomst grupper innehåller två olika tilldelnings typer:

    - **Berättigade** tilldelningar kräver att rollen medlem av rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som tilldelas som aktiva har behörigheten alltid tilldelade till rollen.

1. Om tilldelningen ska vara permanent (permanent kvalificerad eller permanent tilldelad) markerar du kryss rutan **permanent** . Beroende på organisationens inställningar kanske kryss rutan inte visas eller går kanske inte att redigera.

1. När du är färdig väljer du **tilldela**.

1. Välj **Lägg till** för att skapa den nya roll tilldelningen. Ett meddelande om status visas.

    ![Ny tilldelning – meddelande](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig roll tilldelning

Följ dessa steg om du vill uppdatera eller ta bort en befintlig roll tilldelning.

1. [Logga in på Azure AD](https://aad.portal.azure.com) med behörigheter för global administratör eller grupp ägare.
1. Välj **grupper** och välj sedan den roll tilldelnings bara grupp som du vill hantera. Du kan söka eller filtrera listan.

    ![Hitta en roll tilldelnings bara grupp att hantera i PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Öppna gruppen och välj **privilegie rad åtkomst (förhands granskning)**.

    ![Öppna Privileged Identity Managements upplevelsen](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Välj den roll som du vill uppdatera eller ta bort.

1. Hitta roll tilldelningen på flikarna **berättigade roller** eller **aktiva roller** .

    ![Uppdatera eller ta bort roll tilldelning](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Välj **Uppdatera** eller **ta bort** om du vill uppdatera eller ta bort roll tilldelningen.

    Information om hur du utökar en roll tilldelning finns i [utöka eller förnya Azures resurs roller i Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Nästa steg

- [Utöka eller förnya Azures resurs roller i Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
