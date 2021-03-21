---
title: Lägg till en befintlig Azure-prenumeration till din klient organisation – Azure AD
description: Anvisningar om hur du lägger till en befintlig Azure-prenumeration till din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594639"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten

En Azure-prenumeration har en förtroende relation med Azure Active Directory (Azure AD). En prenumeration litar på Azure AD för att autentisera användare, tjänster och enheter.

Flera prenumerationer kan lita på samma Azure AD-katalog. Varje prenumeration kan bara lita på en enda katalog.

En eller flera Azure-prenumerationer kan upprätta en förtroende relation med en instans av Azure Active Directory (Azure AD) för att autentisera och auktorisera säkerhets objekt och enheter mot Azure-tjänster.  När en prenumeration går ut är den betrodda instansen av Azure AD-tjänsten kvar, men säkerhets principerna förlorar åtkomst till Azure-resurser.

När en användare registrerar sig för en moln tjänst från Microsoft skapas en ny Azure AD-klient och användaren blir medlem i rollen global administratör. Men när en prenumerations ägare ansluts till en befintlig klient organisation är ägaren inte tilldelad rollen som global administratör.

Alla användare har en enda *arbets* katalog för autentisering. Dina användare kan också vara gäster i andra kataloger. Du kan se både hem-och gäst kataloger för varje användare i Azure AD.

> [!Important]
> När du kopplar en prenumeration till en annan katalog, förlorar användare som har roller som har tilldelats med hjälp av [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
>
> Om du flyttar ditt Azure Kubernetes service-kluster (AKS) till en annan prenumeration eller flyttar klustrets ägande prenumeration till en ny klient kan klustret förlora funktioner på grund av förlorade roll tilldelningar och tjänstens huvud namn. Mer information om AKS finns i [Azure Kubernetes service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration ska du utföra följande uppgifter:

- Granska följande lista över ändringar som kommer att ske när du har associerat eller lagt till din prenumeration och hur du kan påverkas:

  - Användare som har tilldelats roller med Azure RBAC förlorar sin åtkomst.
  - Tjänst administratör och Co-Administrators kommer att förlora åtkomst.
  - Om du har nyckel valv kommer de inte att vara tillgängliga och du måste åtgärda dem efter associationen.
  - Om du har några hanterade identiteter för resurser som Virtual Machines eller Logic Apps måste du återaktivera eller återskapa dem efter associationen.
  - Om du har en registrerad Azure Stack måste du registrera den igen efter associationen.
  - Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).

- Logga in med ett konto som:

  - Har en [ägar](../../role-based-access-control/built-in-roles.md#owner) roll tilldelning för prenumerationen. Information om hur du tilldelar ägar rollen finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Finns både i den aktuella katalogen och i den nya katalogen. Den aktuella katalogen är kopplad till prenumerationen. Du kopplar den nya katalogen till prenumerationen. Mer information om hur du får åtkomst till en annan katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md).

- Se till att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), en intern Microsoft-prenumeration (MS-AZR-0015P) eller en Microsoft Azure for Students Starter-prenumeration (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Koppla en prenumeration till en katalog<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Följ dessa steg om du vill associera en befintlig prenumeration till din Azure AD-katalog:

1. Logga in och välj den prenumeration som du vill använda från [sidan prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Välj **ändra katalog**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Skärm bild som visar sidan prenumerationer med alternativet ändra katalog markerat.":::

1. Granska eventuella varningar som visas och välj sedan **ändra**.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Skärm bild som visar sidan Ändra katalog med en exempel katalog och ändrings knappen markerad.":::

   När katalogen har ändrats för prenumerationen får du ett meddelande om att det är klart.

1. Välj **Växla kataloger** på sidan prenumeration för att gå till din nya katalog.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Skärm bild som visar sidan för katalog växlaren med exempel information.":::

   Det kan ta flera timmar innan allting visas korrekt. Om det verkar ta för lång tid, kontrol lera det **globala prenumerations filtret**. Kontrol lera att den flyttade prenumerationen inte är dold. Du kan behöva logga ut från Azure Portal och logga in igen för att se den nya katalogen.

Att ändra prenumerations katalogen är en åtgärd på tjänst nivå, så den påverkar inte prenumerationens fakturerings ägande. Om du vill ta bort den ursprungliga katalogen måste du överföra prenumerations fakturerings ägarskapet till en ny konto administratör. Mer information om hur du överför fakturerings ägarskap finns i [överföra ägarskap för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Steg efter associationen

När du har associerat en prenumeration till en annan katalog kan du behöva utföra följande uppgifter för att återuppta åtgärder:

- Om du har några nyckel valv måste du ändra klient-ID för nyckel valvet. Mer information finns i [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../../key-vault/general/move-subscription.md).

- Om du använde systemtilldelade hanterade identiteter för resurser måste du återaktivera dessa identiteter. Om du använde användarspecifika hanterade identiteter måste du återskapa dessa identiteter. När du har aktiverat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter igen. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md)

- Om du har registrerat en Azure Stack som använder den här prenumerationen måste du registrera dig igen. Mer information finns i [registrera Azure Stack hubb med Azure](/azure-stack/operator/azure-stack-registration).

- Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure AD-klient finns i [snabb start: skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Mer information om hur Microsoft Azure kontrollerar resurs åtkomsten finns i [klassiska prenumerationer på administratörs roller, Azure-roller och administratörs roller i Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Mer information om hur du tilldelar roller i Azure AD finns i [tilldela administratörs roller och icke-administratörer roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
