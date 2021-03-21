---
title: Tilldela Azure AD-roller i PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du tilldelar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/16/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4374067fe0070c379a76ef5f59bb6aef5b29fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123113"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Tilldela Azure AD-roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) kan en global administratör göra **permanenta** Azure AD-administrativa roll tilldelningar. Roll tilldelningarna kan skapas med hjälp av [Azure Portal](../roles/permissions-reference.md) eller med [PowerShell-kommandon](/powershell/module/azuread#directory_roles).

Tjänsten Azure AD Privileged Identity Management (PIM) gör det också möjligt för privilegierade roll administratörer att göra permanenta administratörs roll tilldelningar. Dessutom kan administratörer med privilegierade roller göra användare **berättigade** till administratörs roller i Azure AD. En berättigad administratör kan aktivera rollen när de behöver den och sedan upphör deras behörigheter när de är klara.

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

  [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="assign-a-role"></a>Tilldela en roll

Följ dessa steg om du vill göra en användare tillgänglig för en administratörs roll för Azure AD.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som är medlem i rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .

    Information om hur du beviljar en annan administratörs åtkomst till att hantera Privileged Identity Management finns i [bevilja åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **roller** om du vill se en lista över roller för Azure AD-behörigheter.

    ![Skärm bild av sidan "roller" med åtgärden Lägg till tilldelningar markerat.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Välj **Lägg till tilldelningar** för att öppna sidan **Lägg till tilldelningar** .

1. Välj **Välj en roll** för att öppna sidan **Välj en roll** .

    ![Nytt tilldelnings fönster](./media/pim-how-to-add-role-to-user/select-role.png)

1. Välj en roll som du vill tilldela och välj en medlem som du vill tilldela rollen till och välj sedan **Nästa**.

1. I listan **tilldelnings typ** i rutan **medlemskaps inställningar** väljer du **kvalificerad** eller **aktiv**.

    - **Berättigade** tilldelningar kräver att rollen medlem av rollen utför en åtgärd för att använda rollen. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare.

    - **Aktiva** tilldelningar kräver inte att medlemmen utför någon åtgärd för att använda rollen. Medlemmar som tilldelas som aktiva har behörigheten alltid tilldelade till rollen.

1. Om du vill ange en varaktighet för en speciell tilldelning lägger du till en start-och slutdatum-och tids rutor. När du är färdig väljer du **tilldela** för att skapa den nya roll tilldelningen.

    ![Medlemskaps inställningar-datum och tid](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. När rollen har tilldelats visas ett meddelande om tilldelnings status.

    ![Ny tilldelning – meddelande](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Tilldela en roll med begränsad omfattning

För vissa roller kan omfattningen av beviljade behörigheter begränsas till en enskild administratörs enhet, tjänstens huvud namn eller program. Den här proceduren är ett exempel på om du tilldelar en roll som har omfånget för en administrativ enhet. En lista över roller som stöder omfång via administrativ enhet finns i [tilldela begränsade roller till en administrativ enhet](../roles/admin-units-assign-roles.md). Den här funktionen är för närvarande distribuerad till Azure AD-organisationer.

1. Logga in på [Azure Active Directory administrations Center](https://aad.portal.azure.com) med administratörs behörighet för privilegierade roller.

1. Välj **Azure Active Directory**  >  **roller och administratörer**.

1. Välj **användar administratör**.

    ![Kommandot Lägg till tilldelning är tillgängligt när du öppnar en roll i portalen](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Välj **Lägg till tilldelningar**.

    ![När en roll stöder omfång kan du välja ett omfång](./media/pim-how-to-add-role-to-user/add-scope.png)

1. På sidan **Lägg till tilldelningar** kan du:

   - Välj en användare eller grupp som ska tilldelas rollen
   - Välj roll omfång (i det här fallet administrativa enheter)
   - Välj en administrativ enhet för omfånget

Mer information om hur du skapar administrativa enheter finns i [lägga till och ta bort administrativa enheter](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Uppdatera eller ta bort en befintlig roll tilldelning

Följ dessa steg om du vill uppdatera eller ta bort en befintlig roll tilldelning. **Endast Azure AD P2-licensierade kunder**: Tilldela inte en grupp som aktiv till en roll via både Azure AD och PRIVILEGED Identity Management (PIM). En detaljerad förklaring finns i [kända problem](../roles/groups-concept.md#known-issues).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **roller** om du vill se en lista över roller för Azure AD.

1. Välj den roll som du vill uppdatera eller ta bort.

1. Hitta roll tilldelningen på flikarna **berättigade roller** eller **aktiva roller** .

    ![Uppdatera eller ta bort roll tilldelning](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Välj **Uppdatera** eller **ta bort** om du vill uppdatera eller ta bort roll tilldelningen.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Gör en användare berättigad till en roll

Följ dessa steg om du vill göra en användare tillgänglig för en administratörs roll för Azure AD.

1. Välj **roller** eller **medlemmar**.

    ![öppna Azure AD-roller](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Välj **Lägg till medlem** för att öppna **Lägg till hanterade medlemmar**.

1. Välj **Välj en roll**, Välj en roll som du vill hantera och välj sedan **Välj**.

    ![Välj en roll](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Välj **Välj medlemmar**, Välj de användare som du vill tilldela rollen och välj sedan **Välj**.

    ![Välj en användare eller grupp som ska tilldelas](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. I **Lägg till hanterade medlemmar** väljer du **OK** för att lägga till användaren i rollen.

1. I listan över roller väljer du den roll som du just har tilldelat för att visa listan över medlemmar.

     När rollen har tilldelats visas den användare som du har valt i listan medlemmar som **giltig** för rollen.

    ![Användare som är berättigade till en roll](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Nu när användaren är berättigad till rollen kan de se att de kan aktivera den enligt anvisningarna i [Aktivera mina Azure AD-roller i Privileged Identity Management](pim-how-to-activate-role.md).

    Berättigade administratörer uppmanas att registrera sig för Azure AD Multi-Factor Authentication under aktiveringen. Om en användare inte kan registrera sig för MFA, eller använder en Microsoft-konto (till exempel @outlook.com ), måste du göra dem permanenta i alla sina roller.

## <a name="make-a-role-assignment-permanent"></a>Gör en roll tilldelning permanent

Som standard är nya användare endast *berättigade* till en administratörs roll för Azure AD. Följ de här stegen om du vill göra en roll tilldelning permanent.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Lista över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj en **berättigad** roll som du vill göra permanent.

1. Välj **mer** och välj sedan **skapa** behörighet.

    ![Gör roll tilldelning permanent](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rollen visas nu som **permanent**.

    ![Lista över medlemmar med permanent ändring](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Ta bort en användare från en roll

Du kan ta bort användare från roll tilldelningar, men se till att det alltid finns minst en användare som är en permanent global administratör. Om du inte är säker på vilka användare som fortfarande behöver sina roll tilldelningar kan du [starta en åtkomst granskning för rollen](pim-how-to-start-security-review.md).

Följ dessa steg om du vill ta bort en särskild användare från en administratörs roll för Azure AD.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **medlemmar**.

    ![Lista över medlemmar](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Välj en roll tilldelning som du vill ta bort.

1. Välj **mer** och välj sedan **ta bort**.

    ![Ta bort en roll](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. I meddelandet som ber dig att bekräfta väljer du **Ja**.

    ![Bekräfta borttagningen](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Roll tilldelningen tas bort.

## <a name="authorization-error-when-assigning-roles"></a>Auktoriseringsfel vid tilldelning av roller

Om du nyligen har aktiverat Privileged Identity Management för en prenumeration och du får ett auktoriseringsfel när du försöker göra en användare tillgänglig för en Azure AD-administratör, kan det bero på att tjänstens huvud namn för MS-PIM ännu inte har rätt behörighet. Tjänstens huvud namn för MS-PIM måste ha rollen [administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) för att tilldela roller till andra. I stället för att vänta tills MS-PIM tilldelas rollen administratör för användar åtkomst kan du tilldela den manuellt.

Följ dessa steg om du vill tilldela rollen administratör för användar åtkomst till tjänstens huvud namn för MS-PIM för en prenumeration.

1. Logga in på Azure Portal som global administratör.

1. Välj **alla tjänster** och sedan **prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

1. Välj **roll tilldelningar** för att se den aktuella listan över roll tilldelningar i prenumerations omfattningen.

   ![Åtkomst kontroll (IAM) bladet för en prenumeration](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Kontrol lera om tjänstens huvud namn för **MS-PIM** har tilldelats rollen **administratör för användar åtkomst** .

1. Om inte väljer du **Lägg till roll tilldelning** för att öppna fönstret **Lägg till roll tilldelning** .

1. Välj rollen **användar åtkomst administratör** i list rutan **roll** .

1. I listan **Välj** letar du reda på och väljer tjänstens huvud namn för **MS-PIM** .

   ![Fönstret Lägg till roll tilldelning – Lägg till behörigheter för MS-PIM-tjänstens huvud namn](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Välj **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas tjänstens huvud namn rollen administratör för användar åtkomst i prenumerations omfånget.

   ![Sidan åtkomst kontroll som visar roll tilldelning av användar åtkomst för MS-PIM-tjänstens huvud namn](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningarna för Azure AD admin-rollen i Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)