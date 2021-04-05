---
title: Tilldela gäster till Azure-resurs roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du bjuder in externa gäst användare och tilldelar Azure-resurs roller i Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010647"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Bjud in gäst användare och tilldela Azure-resurs roller i Privileged Identity Management

Azure Active Directory (Azure AD) gäst användare ingår i samarbets funktionerna för Business-to-Business (B2B) i Azure AD så att du kan hantera externa gäst användare och leverantörer som gäster i Azure AD. När du kombinerar B2B-samarbete med Azure AD Privileged Identity Management (PIM) kan du utöka dina krav på efterlevnad och styrning till gästerna. Du kan till exempel använda dessa Privileged Identity Management-funktioner för Azure Identity-uppgifter med gäster:

- Tilldela åtkomst till vissa Azure-resurser
- Aktivera just-in-Time-åtkomst
- Ange tilldelningens varaktighet och slutdatum
- Kräv Multi-Factor Authentication för aktiv tilldelning eller aktivering
- Utför åtkomst granskningar
- Använda aviseringar och gransknings loggar

Den här artikeln beskriver hur du bjuder in en gäst i din organisation och hanterar deras åtkomst till Azure-resurser med hjälp av Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>När bjuder du in gäster?

Här följer några exempel på när du kan bjuda in gäster till din organisation:

- Tillåt en extern självgående leverantör som bara har ett e-postkonto för att få åtkomst till dina Azure-resurser för ett projekt.
- Tillåt en extern partner i en stor organisation som använder lokala Active Directory Federation Services (AD FS) för att få åtkomst till ditt utgifts program.
- Tillåt support tekniker som inte tillhör din organisation (till exempel Microsoft Support) att tillfälligt komma åt din Azure-resurs för att felsöka problem.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Hur fungerar samarbete med B2B-gäster?

När du använder B2B-samarbete kan du bjuda in en extern användare till din organisation som gäst. Gästen kan hanteras som en användare i din organisation, men en gäst måste autentiseras i sin hem organisation och inte i din Azure AD-organisation. Det innebär att om gästen inte längre har åtkomst till sin hem organisation, förlorar de också åtkomst till din organisation. Om gästen till exempel lämnar sin organisation förlorar de automatiskt åtkomst till de resurser som du har delat med dem i Azure AD utan att du behöver göra något. Mer information om B2B-samarbete finns i [Vad är gäst användar åtkomst i Azure Active Directory B2B?](../external-identities/what-is-b2b.md).

![Diagram över hur en gäst användare autentiseras i sin Hem Katalog](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Kontrol lera inställningarna för gäst samarbete

För att se till att du kan bjuda in gäster till din organisation bör du kontrol lera inställningarna för gäst samarbete.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **Azure Active Directory**  >  **användar inställningar**.

1. Välj **Hantera inställningar för externt samarbete**.

    ![Sidan Inställningar för extern samarbets partner som visar behörighet, Bjud in och samarbets begränsnings inställningar](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Se till att **rollen administratörer och användare i gäst deltagarens roll kan bjuda** in växeln till **Ja**.

## <a name="invite-a-guest-and-assign-a-role"></a>Bjud in en gäst och tilldela en roll

Med hjälp av Privileged Identity Management kan du bjuda in en gäst och göra dem tillgängliga för en Azure-resurs roll.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) eller [användar administratör](../roles/permissions-reference.md#user-administrator) .

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure-resurser**.

1. Använd **resurs filtret** om du vill filtrera listan över hanterade resurser.

1. Välj den resurs som du vill hantera, till exempel en resurs, resurs grupp, prenumeration eller hanterings grupp.

    Du bör ange omfång till enbart vad gästen behöver.

1. Under hantera väljer du **roller** för att se listan över roller för Azure-resurser.

    ![Listan med Azure-resurser roller som visar antalet användare som är aktiva och berättigade](./media/pim-resource-roles-external-users/resources-roles.png)

1. Välj den lägsta roll som användaren behöver.

    ![Den valda roll sidan visar de aktuella medlemmarna i rollen](./media/pim-resource-roles-external-users/selected-role.png)

1. På sidan roll väljer du **Lägg till medlem** för att öppna fönstret nytt uppdrag.

1. Klicka på **Välj en medlem eller grupp**.

    ![Ny tilldelning – Välj en medlem eller ett grupp fönster som visar användare och grupper tillsammans med ett alternativ för inbjudan](./media/pim-resource-roles-external-users/select-member-group.png)

1. Klicka på **Bjud** in för att bjuda in en gäst.

    ![Bjud in en gäst sida med rutor för att ange en e-postadress och ange ett personligt meddelande](./media/pim-resource-roles-external-users/invite-guest.png)

1. När du har valt en gäst klickar du på **Bjud in**.

    Gästen ska läggas till som en vald medlem.

1. I fönstret **Välj en medlem eller grupp** klickar du på **Välj**.

1. I rutan **medlemskaps inställningar** väljer du tilldelnings typ och varaktighet.

    ![Ny tilldelning – sidan Inställningar för medlemskap med alternativ för att ange tilldelnings typ, start datum och slutdatum](./media/pim-resource-roles-external-users/membership-settings.png)

1. Slutför tilldelningen genom att välja **klar** och sedan **lägga till**.

    Gäst Rolls tilldelningen visas i din roll lista.

    ![Roll sidan som visar gästen som giltig](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivera rollen som gäst

Om du är extern användare måste du godkänna att inbjudan är en gäst i Azure AD-organisationen och eventuellt aktivera roll tilldelningen.

1. Öppna e-postmeddelandet med din inbjudan. E-postmeddelandet kommer att se ut ungefär så här.

    ![Inbjudan till e-post med katalog namn, personligt meddelande och en kom igång-länk](./media/pim-resource-roles-external-users/email-invite.png)

1. Välj länken **Kom igång** i e-postmeddelandet.

1. När du har granskat behörigheterna klickar du på **acceptera**.

    ![Sidan granska behörigheter i en webbläsare med en lista med behörigheter som organisationen vill granska](./media/pim-resource-roles-external-users/invite-accept.png)

1. Du kan bli ombedd att godkänna användnings villkoren och ange om du vill fortsätta vara inloggad. Om du är *berättigad* till en roll i Azure Portal har du inte åtkomst till resurser än.

1. Om du vill aktivera din roll tilldelning öppnar du e-postmeddelandet med länken Aktivera roll. E-postmeddelandet kommer att se ut ungefär så här.

    ![E-postmeddelande som anger att du är berättigad till en roll med en aktiv roll länk](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Välj **Aktivera roll** för att öppna dina giltiga roller i Privileged Identity Management.

    ![Sidan mina roller i Privileged Identity Management visar dina giltiga roller](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Under Åtgärd väljer du länken **Aktivera** .

    Beroende på roll inställningarna måste du ange viss information för att aktivera rollen.

1. När du har angett inställningarna för rollen klickar du på **Aktivera** för att aktivera rollen.

    ![Aktivera omfattning och alternativ för sid lista för att ange start tid, varaktighet och orsak](./media/pim-resource-roles-external-users/activate-role.png)

    Om administratören inte behöver godkänna din begäran, bör du ha åtkomst till de angivna resurserna.

## <a name="view-activity-for-a-guest"></a>Visa aktivitet för en gäst

Du kan visa gransknings loggar för att hålla reda på vad gästerna gör.

1. Som administratör öppnar du Privileged Identity Management och väljer den resurs som har delats.

1. Välj **resurs granskning** om du vill visa aktiviteten för resursen. Nedan visas ett exempel på aktiviteten för en resurs grupp.

    ![Azure-resurser – resurs gransknings sidan visar tid, begär ande och åtgärd](./media/pim-resource-roles-external-users/audit-resource.png)

1. Om du vill visa aktiviteten för gästen väljer du **Azure Active Directory**  >  **användarens**  >  *gäst namn*.

1. Välj **gransknings loggar** om du vill se gransknings loggarna för organisationen. Om det behövs kan du ange filter.

    ![Katalog gransknings loggar lista datum, mål, initierad av och aktivitet](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela administratörs roller för Azure AD i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Vad är gäst användar åtkomst i Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md)