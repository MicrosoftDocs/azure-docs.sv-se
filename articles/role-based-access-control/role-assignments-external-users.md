---
title: Tilldela Azure-roller till externa gäst användare med hjälp av Azure Portal – Azure RBAC
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare utanför en organisation med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: it-pro
ms.openlocfilehash: d834f4ccd8dba26c895e0578f161813fc49332ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556287"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Tilldela Azure-roller till externa gäst användare med hjälp av Azure Portal

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) ger bättre säkerhets hantering för stora organisationer och för små och medel stora företag som arbetar med externa medarbetare, leverantörer eller Freelancer som behöver åtkomst till vissa resurser i din miljö, men inte nödvändigt vis till hela infrastrukturen eller någon fakturerings knuten omfattning. Du kan använda funktionerna i [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) för att samar beta med externa gäst användare och du kan använda Azure RBAC för att ge bara de behörigheter som gäst användare behöver i din miljö.

## <a name="prerequisites"></a>Förutsättningar

Om du vill tilldela Azure-roller eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)


## <a name="when-would-you-invite-guest-users"></a>När bjuder du in gäst användare?

Här följer några exempel scenarier när du kan bjuda in gäst användare till din organisation och bevilja behörigheter:

- Tillåt en extern självgående leverantör som bara har ett e-postkonto för att få åtkomst till dina Azure-resurser för ett projekt.
- Tillåt en extern partner att hantera vissa resurser eller en hel prenumeration.
- Tillåt support tekniker som inte tillhör din organisation (till exempel Microsoft Support) att tillfälligt komma åt din Azure-resurs för att felsöka problem.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Behörighets skillnader mellan medlems användare och gäst användare

Interna medlemmar i en katalog (medlems användare) har olika behörigheter än användare som har bjudits in från en annan katalog som ett B2B-samarbets gäst (gäst användare). Medlemmar som användare kan till exempel läsa nästan all katalog information medan gäst användare har begränsad katalog behörighet. Mer information om medlems användare och gäst användare finns i [Vad är standard användar behörigheter i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Lägga till en gästanvändare i din katalog

Följ dessa steg om du vill lägga till en gäst användare till din katalog med hjälp av Azure Active Directory sidan.

1. Kontrol lera att organisationens inställningar för externt samarbete är konfigurerade så att du får bjuda in gäster. Mer information finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](../active-directory/external-identities/delegate-invitations.md).

1. Klicka på **Azure Active Directory**  >  **användare**  >  **nya gäst användare** i Azure Portal.

    ![Ny gäst användar funktion i Azure Portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Följ stegen för att lägga till en ny gäst användare. Mer information finns i [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory).

När du har lagt till en gäst användare i katalogen kan du antingen skicka gäst användaren en direkt länk till en delad app, eller gäst användaren kan klicka på inlösnings-URL: en i inbjudan via e-post.

![E-postinbjudan till gäst användare](./media/role-assignments-external-users/invite-email.png)

För att gäst användaren ska kunna komma åt din katalog måste de slutföra Inbjudnings processen.

![Gäst användar inbjudan granska behörigheter](./media/role-assignments-external-users/invite-review-permissions.png)

Mer information om Inbjudnings processen finns i [Azure Active Directory B2B-samverkan med inbjudan](../active-directory/external-identities/redemption-experience.md).

## <a name="assign-a-role-to-a-guest-user"></a>Tilldela en roll till en gäst användare

I Azure RBAC, för att bevilja åtkomst, tilldelar du en roll. Om du vill tilldela en roll till en gäst användare följer du [samma steg](role-assignments-portal.md) som för en medlems användare, grupp, tjänstens huvud namn eller hanterad identitet. Följ de här stegen för att tilldela en roll till en gäst användare i olika scope.

1. Klicka på **Alla tjänster** i Azure-portalen.

1.  Välj den uppsättning resurser som åtkomsten gäller för, även kallat omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande skärmbild visar ett exempel på bladet Åtkomstkontroll (IAM) för en resursgrupp. Om du gör ändringar i åtkomst kontrollen gäller de bara för resurs gruppen.

    ![Åtkomst kontroll (IAM) bladet för en resurs grupp](./media/role-assignments-external-users/access-control-resource-group.png)

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.

    Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

    ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** väljer du gäst användaren. Om du inte ser användaren i listan kan du skriva i rutan **Välj** för att söka i katalogen efter visnings namn, e-postadresser och objekt identifierare.

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-external-users/add-role-assignment.png)

1. Klicka på **Spara** för att tilldela rollen vid det valda omfånget.

    ![Roll tilldelning för virtuell dator deltagare](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>Tilldela en roll till en gäst användare som ännu inte är i din katalog

Om du vill tilldela en roll till en gäst användare följer du [samma steg](role-assignments-portal.md) som för en medlems användare, grupp, tjänstens huvud namn eller hanterad identitet.

Om gäst användaren inte finns i din katalog än kan du bjuda in användaren direkt från fönstret Lägg till roll tilldelning.

1. Klicka på **Alla tjänster** i Azure-portalen.

1.  Välj den uppsättning resurser som åtkomsten gäller för, även kallat omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper** eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.

    ![Menyn Lägg till roll tilldelning](./media/shared/add-role-assignment-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** anger du e-postadressen till den person som du vill bjuda in och väljer personen.

   ![Bjud in gäst användare i fönstret Lägg till roll tilldelning](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Klicka på **Spara** för att lägga till gäst användaren i din katalog, tilldela rollen och skicka en inbjudan.

    Efter en liten stund visas ett meddelande om roll tilldelningen och information om inbjudan.

    ![Roll tilldelning och meddelande om inbjudna användare](./media/role-assignments-external-users/invited-user-notification.png)

1. Om du vill bjuda in gäst användaren manuellt högerklickar du på länken inbjudan i meddelandet. Klicka inte på länken inbjudan eftersom den startar processen för inbjudan.

    Länken för inbjudan har följande format:

    `https://invitations.microsoft.com/redeem/...`

1. Slutför Inbjudnings processen genom att skicka inbjudan till gäst användaren.

    Mer information om Inbjudnings processen finns i [Azure Active Directory B2B-samverkan med inbjudan](../active-directory/external-identities/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Ta bort en gäst användare från din katalog

Innan du tar bort en gäst användare från en katalog bör du först ta bort alla roll tilldelningar för gäst användaren. Följ dessa steg om du vill ta bort en gäst användare från en katalog.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där gäst användaren har en roll tilldelning.

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar.

1. Lägg till en bock bredvid gäst användaren med den roll tilldelning som du vill ta bort i listan över roll tilldelningar.

   ![Ta bort roll tilldelning](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-external-users/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

1. Klicka på **Azure Active Directory** användare i det vänstra navigerings fältet  >  .

1. Klicka på den gäst användare som du vill ta bort.

1. Klicka på **Ta bort**.

   ![Ta bort gäst användare](./media/role-assignments-external-users/delete-guest-user.png)

1. Klicka på **Ja** i det borttagnings meddelande som visas.

## <a name="troubleshoot"></a>Felsöka

### <a name="guest-user-cannot-browse-the-directory"></a>Gäst användare kan inte bläddra i katalogen

Gäst användare har begränsad katalog behörighet. Gäst användare kan till exempel inte bläddra i katalogen och kan inte söka efter grupper eller program. Mer information finns i [Vad är standard användar behörigheter i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![Gäst användare kan inte bläddra i användare i en katalog](./media/role-assignments-external-users/directory-no-users.png)

Om en gäst användare behöver ytterligare behörighet i katalogen kan du tilldela gäst användaren en katalog roll. Om du verkligen vill att en gäst användare ska ha fullständig Läs behörighet till din katalog kan du lägga till gäst användaren i rollen [katalog läsare](../active-directory/roles/permissions-reference.md) i Azure AD. Mer information finns i [bevilja behörigheter till användare från partner organisationer i din Azure Active Directory klient](../active-directory/external-identities/add-guest-to-role.md).

![Tilldela rollen katalog läsare](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gäst användare kan inte bläddra användare, grupper eller tjänstens huvud namn för att tilldela roller

Gäst användare har begränsad katalog behörighet. Även om en gäst användare är en [ägare](built-in-roles.md#owner) vid ett omfång, och de försöker tilldela en roll för att ge någon annan åtkomst, kan de inte bläddra i listan över användare, grupper eller tjänstens huvud namn.

![Gäst användare kan inte bläddra i säkerhets objekt för att tilldela roller](./media/role-assignments-external-users/directory-no-browse.png)

Om gäst användaren känner till någons exakta inloggnings namn i katalogen kan de bevilja åtkomst. Om du verkligen vill att en gäst användare ska ha fullständig Läs behörighet till din katalog kan du lägga till gäst användaren i rollen [katalog läsare](../active-directory/roles/permissions-reference.md) i Azure AD. Mer information finns i [bevilja behörigheter till användare från partner organisationer i din Azure Active Directory klient](../active-directory/external-identities/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gäst användare kan inte registrera program eller skapa tjänstens huvud namn

Gäst användare har begränsad katalog behörighet. Om en gäst användare behöver kunna registrera program eller skapa tjänstens huvud namn kan du lägga till gäst användaren i rollen [programutvecklare](../active-directory/roles/permissions-reference.md) i Azure AD. Mer information finns i [bevilja behörigheter till användare från partner organisationer i din Azure Active Directory klient](../active-directory/external-identities/add-guest-to-role.md).

![Gäst användare kan inte registrera program](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gäst användare ser inte den nya katalogen

Om en gäst användare har beviljats åtkomst till en katalog, men de inte ser den nya katalogen som listas i Azure Portal när de försöker växla i katalog-och **prenumerations** fönstret, se till att gäst användaren har slutfört inbjudan. Mer information om Inbjudnings processen finns i [Azure Active Directory B2B-samverkan med inbjudan](../active-directory/external-identities/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Gäst användare ser inte resurser

Om en gäst användare har beviljats åtkomst till en katalog, men de inte ser de resurser som de har beviljats åtkomst till i Azure Portal, se till att gäst användaren har valt rätt katalog. En gäst användare kan ha åtkomst till flera kataloger. För att växla kataloger, i det övre vänstra hörnet, klickar du på **katalog + prenumeration** och sedan på lämplig katalog.

![Fönstret kataloger + prenumerationer i Azure Portal](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Nästa steg

- [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/external-identities/add-users-administrator.md)
- [Egenskaper för en Azure Active Directory B2B-samarbets användare](../active-directory/external-identities/user-properties.md)
- [Elementen i e-postinbjudanen B2B – Azure Active Directory](../active-directory/external-identities/invitation-email-elements.md)
- [Lägg till en gäst användare som en medadministratör](classic-administrators.md#add-a-guest-user-as-a-co-administrator)