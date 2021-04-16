---
title: Visa reservationsanvändning i Azure
description: Lär dig hur du hämtar reservationsanvändning och information.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568954"
---
# <a name="view-reservation-utilization-after-purchase"></a>Visa reservationsanvändning efter köp

Du kan visa procentandelen reservationsanvändning och de resurser som använde reservationen i Azure Portal och i Cost Management Power BI appen.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Visa användning i Azure Portal med Azure RBAC-åtkomst

Om du vill visa reservationsanvändningen måste du ha Azure RBAC-åtkomst till reservationen eller ha förhöjd behörighet för att hantera alla Azure-prenumerationer och hanteringsgrupper.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till [Reservationer](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. I listan visas alla reservationer där du har rollen ägare eller läsare. För varje reservation visas den senast kända utnyttjandegraden.
1. Välj användningsprocenten för att visa användningshistorik och information. Följande video visar ett exempel.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Visa användning som faktureringsadministratör

En EA-administratör (Enterprise-avtal) eller en Microsoft-kundavtal-faktureringsadministratör (MCA) kan visa användningen **från Cost Management + Billing**.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **Cost Management + Billing**  >  **Reservationer.**
1. Välj användningsprocenten för att visa användningshistorik och information.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Hämta reservationer och användning med API:er, PowerShell och CLI

Du kan hämta [reservationsanvändningen med hjälp av](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) API:et för reserverad instans-användning.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Visa reservationer och användning i Power BI

Det finns två alternativ för Power BI användare:

- Azure Cost Management för Power BI Desktop – Datum för reservationsköp och användningsdata finns i [Azure Cost Management anslutningsappen för Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Skapa de rapporter som du vill använda med hjälp av anslutningsappen.
- Azure Cost Management Power BI App – Använd [Azure Cost Management Power BI-appen](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) för förskapade rapporter som du kan anpassa ytterligare.

## <a name="next-steps"></a>Nästa steg

- [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- [Förstå reservationsanvändningen i din ”Betala per användning”-prenumeration](understand-reserved-instance-usage.md).
- [Förstå reservationsanvändningen i din Enterprise-registrering](understand-reserved-instance-usage-ea.md).
- [Förstå reservationsanvändningen i CSP-prenumerationer](/partner-center/azure-reservations).
