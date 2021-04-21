---
title: Behörigheter för att visa och hantera Azure-reservationer
description: Lär dig hur du visar och hanterar Azure-reservationer i Azure Portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: fe2f36b08f98ceb2a5f6085510b589a712ff194d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780469"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Behörigheter för att visa och hantera Azure-reservationer

Den här artikeln beskriver hur reservationsbehörigheter fungerar och hur användare kan visa och hantera Azure-reservationer i Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Vem kan hantera en reservation som standard?

Som standard kan följande användare visa och hantera reservationer:

- Den person som köper en reservation och kontoadministratören för den faktureringsprenumeration som används för att köpa reservationen läggs till i reservationsbeställningen.
- Faktureringsadministratörer för Enterprise-avtal och Microsoft-kundavtal.
- Användare med förhöjd behörighet att hantera alla Azure-prenumerationer och hanteringsgrupper

Reservationslivscykeln är oberoende av en Azure-prenumeration, så reservationen är inte en resurs under Azure-prenumerationen. I stället är det en resurs på klientorganisationsnivå med en egen Azure RBAC-behörighet som är separat från prenumerationer. Reservationer ärver inte behörigheter från prenumerationer efter köpet.

## <a name="how-billing-administrators-can-view-or-manage-reservations"></a>Så här kan faktureringsadministratörer visa eller hantera reservationer

Om du är faktureringsadministratör kan du använda följande steg för att visa och hantera alla reservationer och reservationstransaktioner.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till **Cost Management + Billing**.
    - Om du är EA-administratör väljer du **Faktureringsomfång** på den vänstra menyn och väljer sedan ett i listan över faktureringsomfång.
    - Om du är Microsoft-kundavtal faktureringsprofilsägare väljer du Faktureringsprofiler på **den vänstra menyn.** I listan över faktureringsprofiler väljer du en.
1. I den vänstra menyn väljer du **Produkter + tjänster**  >  **Reservationer.**
1. Den fullständiga listan över reservationer för din EA-registrering eller faktureringsprofil visas.
1. Faktureringsadministratörer kan bli ägare till en reservation genom att välja den och **sedan välja Bevilja** åtkomst i fönstret som visas.

### <a name="how-to-add-billing-administrators"></a>Så här lägger du till faktureringsadministratörer

Lägg till en användare som faktureringsadministratör för ett Enterprise-avtal eller ett Microsoft-kundavtal:

- Om du har ett Enterprise-avtal lägger du till användare med rollen _Företagsadministratör_ så att de kan visa och hantera reservationsbeställningar under Enterprise-avtalet. Företagsadministratörer kan visa och hantera reservationer **i Cost Management + Billing**.
    - Användare med rollen _Företagsadministratör (skrivskyddat)_ kan bara visa reservationen från **Cost Management + Billing**. 
    - Avdelningsadministratörer och kontoägare kan inte visa reservationer _om de inte_ uttryckligen läggs till i dem via åtkomstkontroll (IAM). Mer information finns i [Hantera Azure Enterprise-roller](../manage/understand-ea-roles.md).
- Med ett Microsoft-kundavtal kan användare med rollen som faktureringsprofilägare eller faktureringsprofildeltagare hantera alla reservationsköp som görs via faktureringsprofilen. Debiteringsprofilläsare och fakturahanterare kan visa alla reservationer som betalas via faktureringsprofilen. Däremot kan de inte göra ändringar i reservationer.
    Mer information finns i [Roller och uppgifter för faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Visa reservationer med Azure RBAC-åtkomst

Om du har köpt reservationen eller om du har lagts till i en reservation kan du använda följande steg för att visa och hantera reservationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** > **Reservationer** så visas de reservationer du har åtkomst till.

## <a name="users-with-elevated-access-can-manage-all-azure-subscriptions-and-management-groups"></a>Användare med utökad åtkomst kan hantera alla Azure-prenumerationer och hanteringsgrupper

Du kan höja en användares behörighet [för att hantera alla Azure-prenumerationer och hanteringsgrupper.](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)

När du har förhöjd behörighet:

1. Gå till **Alla tjänster Reservation**  >  **för** att se alla reservationer som finns i klientorganisationen.
1. Om du vill göra ändringar i reservationen lägger du till dig själv som ägare av reservationsbeställningen med åtkomstkontroll (IAM).

## <a name="give-users-azure-rbac-access-to-individual-reservations"></a>Ge användare Azure RBAC åtkomst till enskilda reservationer

Användare som har ägaråtkomst till reservationerna och faktureringsadministratörerna kan delegera åtkomsthantering för en enskild reservationsbeställning.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** > **Reservationer** så visas de reservationer du har åtkomst till.
1. Markera den reservation som du vill ge andra användare åtkomst till.
1. Välj reservationsbeställningen i informationen om reservationer.
1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till rolltilldelning** > **Roll** > **Ägare**. Du kan välja en annan roll om du vill ge begränsad åtkomst.
1. Ange e-postadressen för den användare som du vill lägga till som ägare.
1. Välj användaren och sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Hantera Azure-reservationer](manage-reserved-vm-instance.md).