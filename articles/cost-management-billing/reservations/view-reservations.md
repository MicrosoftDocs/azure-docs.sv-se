---
title: Visa reservationer för Azure-resurser
description: Lär dig hur du visar Azure-reservationer i Azure-portalen. Se reservationer och användning med hjälp av API:er, PowerShell, CLI och Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561112"
---
# <a name="view-azure-reservations"></a>Visa Azure-reservationer

Den här artikeln förklarar hur du visar Azure-reservationer på Azure-portalen. Du kan visa och hantera en reservation som du köpt på Azure-portalen.

## <a name="who-can-manage-a-reservation-by-default"></a>Vem kan hantera en reservation som standard?

Som standard kan följande användare visa och hantera reservationer:

- Den person som köper en reservation och kontoadministratören för den faktureringsprenumeration som används för att köpa reservationen läggs till i reservationsbeställningen.
- Faktureringsadministratörer för Enterprise-avtal och Microsoft-kundavtal.

Du kan välja mellan två alternativ för att låta andra hantera reservationer:

- Delegera åtkomsthantering för en enskild reservationsbeställning:
    1. Logga in på [Azure-portalen](https://portal.azure.com).
    1. Välj **Alla tjänster** > **Reservationer** så visas de reservationer du har åtkomst till.
    1. Markera den reservation som du vill ge andra användare åtkomst till.
    1. Välj reservationsbeställningen i reservationsinformationen.
    1. Välj **Åtkomstkontroll (IAM)** .
    1. Välj **Lägg till rolltilldelning** > **Roll** > **Ägare**. Du kan välja en annan roll om du vill ge begränsad åtkomst.
    1. Ange e-postadressen för den användare som du vill lägga till som ägare.
    1. Välj användaren och sedan **Spara**.

- Lägg till en användare som faktureringsadministratör för ett Enterprise-avtal eller ett Microsoft-kundavtal:
    - För ett Enterprise-avtal lägger du till användare med rollen _Företagsadministratör_ för att visa och hantera alla reservationsbeställningar som gäller för Enterprise-avtalet. Användare med rollen _Enterprise-administratör (skrivskyddad)_ kan bara visa reservationen. Avdelningsadministratörer och kontoägare kan inte visa reservationer _om de inte_ uttryckligen läggs till i dem via åtkomstkontroll (IAM). Mer information finns i [Hantera Azure Enterprise-roller](../manage/understand-ea-roles.md).

        _Enterprise-administratörer kan bli ägare till en reservationsbeställning och kan lägga till andra användare till en reservation via åtkomstkontroll (IAM)._
    - För ett Microsoft-kundavtal kan användare med rollen som faktureringsprofilägare eller rollen som faktureringsprofildeltagare hantera alla reservationsköp som görs via faktureringsprofilen. Debiteringsprofilläsare och fakturahanterare kan visa alla reservationer som betalas via faktureringsprofilen. De kan dock inte göra ändringar i reservationer.
    Mer information finns i [Roller och uppgifter för faktureringsprofiler](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Så här kan faktureringsadministratörer visa och hantera reservationer

1. Gå till **Cost Management + Billing** och välj **Reservationstransaktioner** till vänster på sidan.
2. Om du har nödvändiga faktureringsbehörigheter kan du visa och hantera reservationer. Om du inte ser några reservationer kontrollerar du att du är inloggad med den Azure AD-klientorganisation där reservationerna skapades.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Visa reservation och användning i Azure-portalen

Så här visar du en reservation som ägare eller läsare

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. I listan visas alla reservationer där du har rollen ägare eller läsare. För varje reservation visas den senast kända utnyttjandegraden.
4. Välj användningsprocenten för att visa användningshistorik och information. Se mer information i videon nedan.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Hämta reservationer och användning med API:er, PowerShell och CLI

Hämta en lista över alla reservationer med hjälp av följande resurser:

- [API: Reservationsbeställning – Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Reservationsbeställning – Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Reservationsbeställning – Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Du kan också hämta [reservationsanvändningen](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) med hjälp av användnings-API:et för reserverad instans. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Visa reservationer och användning i Power BI

Det finns två alternativ för Power BI-användare
- Innehållspaket: Datum för reservationsköp och användningsdata är tillgängliga i [Power BI-innehållspaketet Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Skapa önskade rapporter med hjälp av innehållspaketet. 
- Cost Management-app: Använd [Cost Management-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) för färdiga rapporter som du kan anpassa ytterligare.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- [Förstå reservationsanvändningen i din ”Betala per användning”-prenumeration](understand-reserved-instance-usage.md).
- [Förstå reservationsanvändningen i din Enterprise-registrering](understand-reserved-instance-usage-ea.md).
- [Förstå reservationsanvändningen i CSP-prenumerationer](/partner-center/azure-reservations).

