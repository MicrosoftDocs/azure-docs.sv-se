---
title: Klassiska Azure-prenumerationsadministratörer
description: Beskriver hur du lägger till eller ändrar Azure Co-Administrator-och tjänst administratörs roller och hur du visar konto administratören.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557532"
---
# <a name="azure-classic-subscription-administrators"></a>Klassiska Azure-prenumerationsadministratörer

Microsoft rekommenderar att du hanterar åtkomst till Azure-resurser med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Men om du fortfarande använder den klassiska distributions modellen måste du använda en klassisk prenumerations administratörs roll: tjänst administratör och delad administratör. Mer information finns i [Azure Resource Manager vs. Classic-distribution](../azure-resource-manager/management/deployment-models.md).

Den här artikeln beskriver hur du lägger till eller ändrar rollen Co-Administrator-och tjänst administratör och hur du visar konto administratören.

## <a name="add-a-co-administrator"></a>Lägga till en medadministratör

> [!TIP]
> Du behöver bara lägga till en Co-Administrator om användaren behöver hantera klassiska Azure-distributioner med hjälp av [Azure Service Management PowerShell-modulen](/powershell/module/servicemanagement/azure.service). Om användaren bara använder Azure Portal för att hantera de klassiska resurserna behöver du inte lägga till den klassiska administratören för användaren.

1. Logga in på [Azure Portal](https://portal.azure.com) som tjänst administratör eller delad administratör.

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

    Co-Administrators kan bara tilldelas i prenumerations omfånget.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **klassiska administratörer** .

    ![Skärm bild som öppnar klassiska administratörer](./media/classic-administrators/classic-administrators.png)

1. Klicka på **Lägg** till  >  **Lägg till gemensam administratör** för att öppna fönstret Lägg till samadministratörer.

    Om alternativet Lägg till samadministratör är inaktiverat har du inte behörighet.

1. Välj den användare som du vill lägga till och klicka på **Lägg till**.

    ![Skärm bild som lägger till gemensam administratör](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Lägg till en gäst användare som en Co-Administrator

Om du vill lägga till en gäst användare som en medadministratör följer du samma steg som i föregående avsnitt för att [lägga till en gemensam administratör](#add-a-co-administrator) . Gäst användaren måste uppfylla följande kriterier:

- Gäst användaren måste ha en närvaro i din katalog. Det innebär att användaren har bjudits in till din katalog och accepterat inbjudan.

Mer information om hur du lägger till en gäst användare i din katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../active-directory/external-identities/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Skillnader för gäst användare

Gäst användare som har tilldelats Co-Administrator-rollen kan se vissa skillnader jämfört med medlems användare med Co-Administrator-rollen. Föreställ dig följande scenario:

- Användare A med ett Azure AD-konto (arbets-eller skol konto) är en tjänst administratör för en Azure-prenumeration.
- Användare B har en Microsoft-konto.
- Användare A tilldelar Co-Administrator rollen till användare B.
- Användare B kan göra nästan allt, men kan inte registrera program eller söka efter användare i Azure AD-katalogen.

Du förväntar dig att användare B kan hantera allt. Orsaken till den här skillnaden är att Microsoft-konto läggs till i prenumerationen som gäst användare i stället för en medlems användare. Gäst användare har olika standard behörigheter i Azure AD jämfört med medlems användare. Medlems användare kan till exempel läsa andra användare i Azure AD och gäst användare kan inte. Medlems användare kan registrera nya tjänst huvud namn i Azure AD-och gäst användare kan inte.

Om en gäst användare behöver kunna utföra dessa uppgifter, är en möjlig lösning att tilldela de Azure AD-roller som gäst användaren behöver. I det föregående scenariot kan du till exempel tilldela rollen [katalog läsare](../active-directory/roles/permissions-reference.md#directory-readers) för att läsa andra användare och tilldela rollen [programutvecklare](../active-directory/roles/permissions-reference.md#application-developer) att kunna skapa tjänstens huvud namn. Mer information om medlemmar och gäst användare och deras behörigheter finns i [Vad är standard användar behörigheter i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Mer information om hur du beviljar åtkomst för gäst användare finns i [tilldela Azure-roller till externa gäst användare med hjälp av Azure Portal](role-assignments-external-users.md).

Observera att de [inbyggda Azure-rollerna](../role-based-access-control/built-in-roles.md) skiljer sig från [Azure AD-rollerna](../active-directory/roles/permissions-reference.md). De inbyggda rollerna beviljar inte åtkomst till Azure AD. Mer information finns i [förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md).

Information som jämför medlems användare och gäst användare finns i [Vad är standard användar behörigheter i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Ta bort en Co-Administrator

1. Logga in på [Azure Portal](https://portal.azure.com) som tjänst administratör eller delad administratör.

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **klassiska administratörer** .

1. Lägg till en bock bredvid Co-Administrator som du vill ta bort.

1. Klicka på **Ta bort**.

1. Klicka på **Ja** i meddelande rutan som visas.

    ![Skärm bild som tar bort gemensam administratör](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändra tjänstadministratör

Endast konto administratören kan ändra tjänst administratör för en prenumeration. När du registrerar dig för en Azure-prenumeration är tjänst administratören som standard samma som konto administratören. Användaren med rollen konto administratör har ingen åtkomst till Azure Portal. Användaren med rollen tjänst administratör har fullständig åtkomst till Azure Portal. Om konto administratören och tjänst administratören är samma användare och du ändrar tjänst administratören till en annan användare förlorar konto administratören åtkomst till Azure Portal. Konto administratören kan dock alltid använda konto Center för att ändra tjänst administratören tillbaka till sig själva.

Följ dessa steg om du vill ändra tjänst administratören i Azure Portal.

1. Kontrol lera att ditt scenario stöds genom att kontrol lera [begränsningarna för att ändra tjänst administratören](#limitations-for-changing-the-service-administrator).

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.

1. Öppna **Cost Management + fakturering** och välj en prenumeration.

1. Klicka på **Egenskaper** i det vänstra navigerings fältet.

1. Klicka på **tjänst administratör**.

    ![Skärm bild som visar prenumerations egenskaperna i Azure Portal](./media/classic-administrators/service-admin.png)

1. På sidan **Redigera tjänst administratör** anger du e-postadressen för den nya tjänst administratören.

    ![Skärm bild som visar sidan Redigera tjänst administratör](./media/classic-administrators/service-admin-edit.png)

1. Klicka på **OK** för att spara ändringen.

### <a name="limitations-for-changing-the-service-administrator"></a>Begränsningar för att ändra tjänst administratör

Det kan bara finnas en tjänst administratör per Azure-prenumeration. Att ändra tjänst administratören beter sig på olika sätt beroende på om konto administratören är en Microsoft-konto eller om det är ett Azure AD-konto (arbets-eller skol konto).

| Konto administratörs konto | Kan du ändra tjänst administratör till en annan Microsoft-konto? | Kan du ändra tjänst administratör till ett Azure AD-konto i samma katalog? | Kan du ändra tjänst administratör till ett Azure AD-konto i en annan katalog? |
| --- | --- | --- | --- |
| Microsoft-konto | Ja | Nej | Nej |
| Azure AD-konto | Ja | Ja | Nej |

Om konto administratören är ett Azure AD-konto kan du ändra tjänst administratören till ett Azure AD-konto i samma katalog, men inte i en annan katalog. Kan till exempel abby@contoso.com ändra tjänst administratören till bob@contoso.com , men kan inte ändra tjänst administratören till john@notcontoso.com om den inte john@notcontoso.com har en närvaro i contoso.com-katalogen.

Mer information om Microsoft-konton och Azure AD-konton finns i [Vad är Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Visa kontoadministratören

Konto administratören är den användare som ursprungligen registrerade sig för Azure-prenumerationen och som ansvarar för prenumerationens fakturerings ägare. Information om hur du ändrar konto administratören för en prenumeration finns i [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md).

Följ dessa steg om du vill visa konto administratören.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna **Cost Management + fakturering** och välj en prenumeration.

1. Klicka på **Egenskaper** i det vänstra navigerings fältet.

    Konto administratören för prenumerationen visas i rutan **konto administratör** .

    ![Skärm bild som visar konto administratören](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Lägg till eller ändra Azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md)