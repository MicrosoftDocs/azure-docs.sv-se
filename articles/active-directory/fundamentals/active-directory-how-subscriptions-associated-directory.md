---
title: Lägg till en befintlig Azure-prenumeration till din klient organisation – Azure AD
description: Anvisningar om hur du lägger till en befintlig Azure-prenumeration i Azure Active Directory-klienten.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7a39340f44e2c6eeae5b5f1a8e687bc73b3b0fe
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028418"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten

En Azure-prenumeration har en förtroenderelation med Azure Active Directory (Azure AD). En prenumeration litar på Azure AD för att autentisera användare, tjänster och enheter.

Flera prenumerationer kan lita på samma Azure AD-katalog. Varje prenumeration kan bara lita på en enda katalog.

En eller flera Azure-prenumerationer kan upprätta en förtroenderelation med en instans av Azure Active Directory (Azure AD) för att autentisera och auktorisera säkerhetsobjekt och enheter mot Azure-tjänster.  När en prenumeration går ut är den betrodda instansen av Azure AD-tjänsten kvar, men säkerhetsprinciperna förlorar åtkomst till Azure-resurser.

När en användare registrerar sig för en molntjänst från Microsoft skapas en ny Azure AD-klient och användaren blir medlem i rollen global administratör. Men när en prenumerations ägare ansluts till en befintlig klientorganisation är ägaren inte tilldelad rollen som global administratör.

Alla användare har en enda *arbets*-katalog för autentisering. Dina användare kan också vara gäster i andra kataloger. Du kan se både hem- och gästkataloger för varje användare i Azure AD.

> [!Important]
> När du kopplar en prenumeration till en annan katalog, förlorar användare som har roller som har tilldelats med hjälp av [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md) åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
>
> Om du flyttar ditt Azure Kubernetes service-kluster (AKS) till en annan prenumeration eller flyttar klustrets ägande prenumeration till en ny klient kan klustret förlora funktioner på grund av förlorade rolltilldelningar och tjänstens huvudnamn. Mer information om AKS finns i [Azure Kubernetes service (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration ska du utföra följande steg:

- Granska följande lista över ändringar som kommer att ske när du har associerat eller lagt till din prenumeration och hur du kan påverkas:

  - Användare som har tilldelats roller med Azure RBAC förlorar sin åtkomst
  - Tjänsteadministratör och Co-Administratörer kommer att förlora sin åtkomst
  - Om du har ett nyckelvalv kommer de inte att vara tillgängliga och du måste åtgärda dem efter kopplingen
  - Om du har några hanterade identiteter för resurser som Virtual Machines eller Logic Apps måste du återaktivera eller återskapa dem efter associationen
  - Om du har en registrerad Azure Stack måste du registrera den igen efter kopplingen
  - Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).

- Logga in med ett konto som:

  - Har en [ägarroll](../../role-based-access-control/built-in-roles.md#owner)-tilldelning för prenumerationen. Information om hur du tilldelar ägarrollen finns i [lägga till eller ta bort Azure roll tilldelningar med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Finns både i den aktuella katalogen och i den nya katalogen. Den aktuella katalogen är kopplad till prenumerationen. Du kopplar den nya katalogen till prenumerationen. Mer information om hur du får åtkomst till en annan katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../external-identities/add-users-administrator.md).

- Se till att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), en intern Microsoft-prenumeration (MS-AZR-0015P) eller en Microsoft Imagine-prenumeration (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Koppla en prenumeration till en katalog<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Följ dessa steg om du vill associera en befintlig prenumeration till din Azure AD-katalog:

1. Logga in och välj den prenumeration som du vill använda från [sidan prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Välj **ändra katalog**.

   ![Sidan prenumerationer med alternativet för att ändra katalog är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Granska eventuella varningar som visas och välj sedan **ändra**.

   ![Ändra katalog-sidan, som visar katalogen som ska ändras till](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   När katalogen har ändrats för prenumerationen får du ett meddelande om att det är klart.

1. Välj **Växla kataloger** på sidan prenumeration för att gå till din nya katalog.

   ![Sidan katalogväxlaren med exempel & information](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   Det kan ta flera timmar innan allting visas korrekt. Om det verkar ta för lång tid, kontrollera det **globala prenumerationsfiltret**. Kontrollera att den flyttade prenumerationen inte är dold. Du kan behöva logga ut från Azure Portal och logga in igen för att se den nya katalogen.

Att ändra prenumerationskatalogen är en åtgärd på tjänstenivå, så den påverkar inte prenumerationens faktureringsägande. Om du vill ta bort den ursprungliga katalogen måste du överföra ägarskapet för prenumerationsfaktureringen till en ny kontoadministratör. Mer information om hur du överför faktureringsägarskap finns i [överföra ägarskap för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Steg efter associationen

När du har associerat en prenumeration till en annan katalog kan du behöva utföra följande uppgifter för att återuppta åtgärder:

- Om du har några nyckelvalv måste du ändra klient-ID för nyckelvalvet. Mer information finns i [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../../key-vault/general/move-subscription.md).

- Om du använde systemtilldelade hanterade identiteter för resurser måste du återaktivera dessa identiteter. Om du använde användarspecifika hanterade identiteter måste du återskapa dessa identiteter. När du har aktiverat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter igen. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

- Om du har registrerat en Azure Stack som använder den här prenumerationen måste du registrera dig igen. Mer information finns i [registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).

- Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure AD-klient finns i [snabbstart: skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Mer information om hur Microsoft Azure kontrollerar resursåtkomsten finns i [klassiska prenumerationer på administratörsroller, Azure-roller och administratörsroller i Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Mer information om hur du tilldelar roller i Azure AD finns i [tilldela administratörsroller och icke-administratörer roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
