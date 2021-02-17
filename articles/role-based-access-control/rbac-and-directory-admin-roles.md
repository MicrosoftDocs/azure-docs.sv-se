---
title: Klassiska administratörs roller för prenumeration, Azure-roller och Azure AD-roller
description: Beskriver de olika rollerna i Azure-klassiska prenumerations administratörs roller, Azure-roller och Azure Active Directory-roller (Azure AD)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: daab07a72f61cfdf4f39cae03a1c61bd18f37a8f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557429"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Klassiska administratörs roller för prenumeration, Azure-roller och Azure AD-roller

Om du är nybörjare på Azure kan det vara lite svårt att förstå de olika rollerna i Azure. Den här artikeln förklarar följande roller och när du ska använda dem:
- Administratörsroller för klassiska prenumerationer
- Azure-roller
- Azure Active Directory-roller (Azure AD)

## <a name="how-the-roles-are-related"></a>Så är rollerna relaterade

För att bättre förstå rollerna i Azure är det bra att känna till lite av historien. När Azure ursprungligen lanserades hanterades åtkomst till resurser med bara tre administratörsroller: kontoadministratör, tjänstadministratör och medadministratör. Senare lades Azure-rollbaserad åtkomst kontroll (Azure RBAC) till. Azure RBAC är ett nyare auktoriseringssystem som ger detaljerad åtkomsthantering för Azure-resurser. Azure RBAC innehåller många inbyggda roller, kan tilldelas i olika omfattningar och du kan skapa dina egna anpassade roller. För att hantera resurser i Azure AD, till exempel användare, grupper och domäner, finns det flera Azure AD-roller.

Följande diagram är en övergripande vy över hur de klassiska prenumerations rollerna för prenumeration, Azure-roller och Azure AD-roller är relaterade.

![De olika rollerna i Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Administratörsroller för klassiska prenumerationer

Kontoadministratör, tjänstadministratör och medadministratör är de tre administratörsrollerna för klassiska prenumerationer i Azure. Administratörer för klassiska prenumerationer har fullständig åtkomst till Azure-prenumerationen. De kan hantera resurser med hjälp av Azure-portalen, Azure Resource Manager-API:er och den klassiska distributionsmodellens API:er. Det konto som används för att registrera sig för Azure anges automatiskt som både kontoadministratör och tjänstadministratör. Sedan kan ytterligare medadministratörer läggas till. Tjänst administratören och Co-Administrators har motsvarande åtkomst till användare som har tilldelats ägar rollen (en Azure-roll) i prenumerations omfånget. I följande tabell beskrivs skillnaderna mellan dessa tre administrativa roller för klassiska prenumerationer.

| Klassisk prenumerationsadministratör | Gräns | Behörigheter | Kommentarer |
| --- | --- | --- | --- |
| Kontoadministratör | 1 per Azure-konto | <ul><li>Hantera fakturering i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)</li><li>Hantera alla prenumerationer i ett konto</li><li>Skapa nya prenumerationer</li><li>Avbryt prenumerationer</li><li>Ändra faktureringen för en prenumeration</li><li>Ändra tjänstadministratör</li></ul> | Begreppsmässigt är detta faktureringsägaren för prenumerationen. |
| Tjänstadministratör | 1 per Azure-prenumeration | <ul><li>Hantera tjänster i [Azure-portalen](https://portal.azure.com)</li><li>Avbryt prenumerationen</li><li>Tilldela användare till rollen Medadministratör</li></ul> | Som standard för en ny prenumeration är kontoadministratören också tjänstadministratören.<br>Tjänstadministratören har likvärdig åtkomst som en användare som har tilldelats rollen Ägare i prenumerationsomfånget.<br>Tjänstadministratören har fullständig åtkomst till Azure-portalen. |
| Medadministratör | 200 per prenumeration | <ul><li>Samma åtkomstbehörigheter som tjänstadministratören, men kan inte ändra associationen mellan prenumerationer och Azure-kataloger</li><li>Tilldela användare till rollen Medadministratör, men kan inte ändra tjänstadministratör</li></ul> | Medadministratören har likvärdig åtkomst som en användare som har tilldelats rollen Ägare i prenumerationsomfånget. |

I Azure-portalen kan du hantera medadministratörer eller visa tjänstadministratören med hjälp av fliken **Klassiska administratörer**.

![Klassiska Azure-prenumerationsadministratörer i Azure-portalen](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

I Azure-portalen kan du visa eller ändra tjänstadministratören eller visa kontoadministratören på egenskapsbladet för din prenumeration.

![Kontoadministratör och tjänstadministratör i Azure-portalen](./media/rbac-and-directory-admin-roles/account-admin.png)

Mer information finns i [Klassiska Azure-prenumerationsadministratörer](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure-konto och Azure-prenumerationer

Ett Azure-konto representerar en faktureringsrelation. Ett Azure-konto är en användaridentitet, en eller flera Azure-prenumerationer och en associerad uppsättning Azure-resurser. Den person som skapar kontot är kontoadministratör för alla prenumerationer som skapas i det kontot. Den personen är även standardtjänstadministratör för prenumerationen.

Azure-prenumerationer hjälper dig att organisera åtkomst till Azure-resurser. samt styra hur resursanvändningen rapporteras, faktureras och betalas. Olika prenumerationer kan ha olika fakturerings- och betalningskonfiguration, vilket betyder att du kan ha olika faktureringsplaner beroende på kontor, avdelning, projekt och så vidare. Varje tjänst tillhör en prenumeration, och prenumerations-ID kan krävas för programmässiga åtgärder.

Varje prenumeration är associerad med en Azure AD-katalog. Du hittar katalogen som prenumerationen är kopplad till genom att öppna **prenumerationer** i Azure Portal och sedan välja en prenumeration för att se katalogen.

Konton och prenumerationer hanteras i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

## <a name="azure-roles"></a>Azure-roller

Azure RBAC är ett auktoriseringssystem som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomsthantering till Azure-resurser såsom beräkning och lagring. Azure RBAC har över 70 inbyggda roller. Det finns fyra grundläggande Azure-roller. De första tre gäller för alla resurstyper:

| Azure-roll | Behörigheter | Kommentarer |
| --- | --- | --- |
| [Ägare](built-in-roles.md#owner) | <ul><li>Fullständig åtkomst till alla resurser</li><li>Delegera åtkomst till andra</li></ul> | Tjänstadministratören och medadministratörer tilldelas rollen Ägare i prenumerationsomfånget<br>Gäller för alla resurstyper. |
| [Deltagare](built-in-roles.md#contributor) | <ul><li>Skapa och hantera alla typer av Azure-resurser</li><li>Skapa en ny klientorganisation i Azure Active Directory</li><li>Kan inte bevilja åtkomst till andra</li></ul> | Gäller för alla resurstyper. |
| [Läsare](built-in-roles.md#reader) | <ul><li>Visa Azure-resurser</li></ul> | Gäller för alla resurstyper. |
| [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator) | <ul><li>Hantera användaråtkomst till Azure-resurser</li></ul> |  |

Resten av de inbyggda rollerna tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer. En lista över alla inbyggda roller finns i [inbyggda roller i Azure](built-in-roles.md).

Endast Azure Portal och Azure Resource Manager-API: er stöder Azure RBAC. Användare, grupper och program som har tilldelats Azure-roller kan inte använda de [klassiska API: erna för distributions modellen i Azure](../azure-resource-manager/management/deployment-models.md).

Roll tilldelningar med Azure RBAC visas på bladet **åtkomst kontroll (IAM)** i Azure Portal. Du hittar det här bladet i hela portalen, till exempel hanterings grupper, prenumerationer, resurs grupper och olika resurser.

![Bladet Åtkomstkontroll (IAM) i Azure-portalen](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

När du klickar på fliken **Roller** visas listan över inbyggda och anpassade roller.

![Inbyggda roller i Azure-portalen](./media/rbac-and-directory-admin-roles/roles-list.png)

Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Azure AD-roller

Azure AD-roller används för att hantera Azure AD-resurser i en katalog, till exempel skapa eller redigera användare, tilldela administrativa roller till andra, återställa användar lösen ord, hantera användar licenser och hantera domäner. I följande tabell beskrivs några av de viktigare Azure AD-rollerna.

| Azure AD-roll | Behörigheter | Kommentarer |
| --- | --- | --- |
| [Global administratör](../active-directory/roles/permissions-reference.md#global-administrator-permissions) | <ul><li>Hantera åtkomst till alla administrativa funktioner i Azure Active Directory samt tjänster som federerar till Azure Active Directory</li><li>Tilldela administratörsroller till andra</li><li>Återställa lösenordet för valfri användare och alla andra administratörer</li></ul> | Den person som registrerar sig för Azure Active Directory-klientorganisationen blir en global administratör. |
| [Användar administratör](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>Skapa och hantera alla aspekter av användare och grupper</li><li>Hantera supportbegäranden</li><li>Övervaka tjänstens hälsa</li><li>Ändra lösenord för användare, supportadministratörer och andra användaradministratörer</li></ul> |  |
| [Fakturerings administratör](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Genomför inköp</li><li>Hantera prenumerationer</li><li>Hantera supportbegäranden</li><li>Övervakar tjänstens hälsa</li></ul> |  |

I Azure Portal kan du se listan över Azure AD-roller på bladet **roller och administratörer** . En lista över alla Azure AD-roller finns [i administratörs roll behörigheter i Azure Active Directory](../active-directory/roles/permissions-reference.md).

![Azure AD-roller i Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Skillnader mellan Azure-roller och Azure AD-roller

På en hög nivå kontrollerar Azure-roller behörigheter för att hantera Azure-resurser, medan Azure AD-roller kontrollerar behörigheter för att hantera Azure Active Directory-resurser. I följande tabell jämförs några av skillnaderna.

| Azure-roller | Azure AD-roller |
| --- | --- |
| Hantera åtkomst till Azure-resurser | Hantera åtkomst till Azure Active Directory-resurser |
| Stöder anpassade roller | Stöder anpassade roller |
| Omfånget kan anges på flera nivåer (hanteringsgrupp, prenumeration, resursgrupp och resurs) | Omfånget är på klientorganisationsnivån |
| Rollinformation kan nås i Azure-portalen, Azure CLI, Azure PowerShell, Azure Resource Manager-mallar samt REST API | Du kan komma åt roll information i Azure Admin Portal, Microsoft 365 administrations Center, Microsoft Graph, AzureAD PowerShell |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Överlappar Azure-roller och Azure AD-roller?

Som standard omfattar inte Azure-roller och Azure AD-roller Azure och Azure AD. Men om en global administratör höjer åtkomsten genom att välja **åtkomst hantering för Azure-resurser** i Azure Portal, beviljas den globala administratören rollen [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) (en Azure-roll) på alla prenumerationer för en viss klient. Med rollen Administratör för användaråtkomst kan användaren bevilja åtkomst till Azure-resurser för andra användare. Den här växeln kan vara användbar för att få åtkomst till en prenumeration. Mer information finns i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](elevate-access-global-admin.md).

Flera Azure AD-roller omfattar Azure AD och Microsoft 365, till exempel rollen global administratör och användar administratör. Om du till exempel är medlem i rollen global administratör har du globala administratörs funktioner i Azure AD och Microsoft 365, till exempel att göra ändringar i Microsoft Exchange och Microsoft SharePoint. Som standard har den globala administratören dock inte åtkomst till Azure-resurser.

![Azure RBAC kontra Azure AD-roller](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Nästa steg

- [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](overview.md)
- [Behörigheter för administratörsroller i Azure Active Directory](../active-directory/roles/permissions-reference.md)
- [Klassiska Azure-prenumerationsadministratörer](classic-administrators.md)
