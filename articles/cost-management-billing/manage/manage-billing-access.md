---
title: Hantera åtkomst till Azure-fakturering
description: Lär dig hur du ger åtkomst till din Azure-faktureringsinformation för medlemmar i ditt team.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/26/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1574149e0587701323cb9ae5ae0c5cfeaccf47c7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096410"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Hantera åtkomst till faktureringsinformation för Azure

Du kan ge andra tillgång till faktureringsinformation för ditt konto i Azure-portalen. Typen av faktureringsroller och instruktionerna för att ge åtkomst till faktureringsinformationen varierar beroende på typen av ditt faktureringskonto. Information om hur du avgör typen av ditt faktureringskonto finns i [Kontrollera typen av ditt faktureringskonto](#check-the-type-of-your-billing-account).

Artikeln gäller för kunder med Microsoft Online-tjänstprogramkonton. Om du är Azure-kund med ett Enterprise-avtal (EA) och är företagsadministratören kan du ge behörighet till avdelningsadministratörer och kontoägare i Enterprise-portalen. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md). Om du är Microsoft Customer Agreement-kund kan du läsa [Förstå administrativa roller för Microsofts kundavtal i Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Kontoadministratörer för Microsoft Online-tjänstprogramkonton

Kontoadministratören är den enda ägaren till ett faktureringskonto för Microsoft Online-tjänstprogram. Rollen tilldelas till en person som har registrerat sig för Azure. Kontoadministratörer har behörighet att utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa fakturor eller ändra faktureringen för en prenumeration.

## <a name="give-others-access-to-view-billing-information"></a>Ge andra åtkomst till att visa faktureringsinformation

Kontoadministratören kan ge andra åtkomst till faktureringsinformationen i Azure genom att tilldela någon av följande roller i en prenumeration i sitt konto.

- Tjänstadministratör
- Medadministratör
- Ägare
- Deltagare
- Läsare
- Faktureringsläsare

Dessa roller har åtkomst till faktureringsinformation i [Azure-portalen](https://portal.azure.com/). Personer som har tilldelats dessa roller kan även använda [API:er för fakturering](consumption-api-overview.md#usage-details-api) för att programmatiskt hämta fakturor och användningsinformation.

Information om hur du tilldelar roller finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

** Om du är EA-kund kan en kontoägare tilldela rollen ovan till andra användare i sitt team. Men för att dessa användare ska kunna visa faktureringsinformation måste företagsadministratören aktivera AO-visningsdebitering i Enterprise-portalen.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> Tillåta användare att ladda ned fakturor

När en kontoadministratör har tilldelat lämpliga roller till andra användare måste den aktivera åtkomst till att ladda ned fakturor i Azure-portalen. Fakturor som är äldre än december 2016 är endast tillgängliga för kontoadministratören.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som kontoadministratör.

1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar Cost Management + Billing under avsnittet Tjänster.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Välj **Prenumerationer** i den vänstra rutan. Beroende på din åtkomst kan du behöva välja ett faktureringsomfång och sedan välja **Prenumerationer**.

    ![Skärmbild som visar val av prenumerationer](./media/manage-billing-access/billing-select-subscriptions.png)

1. Välj **Fakturor** och sedan **Åtkomst till faktura**.

    ![Skärmbild som visar hur du delegerar åtkomst till fakturor](./media/manage-billing-access/aa-optin01.png)

1. Välj **På** och spara.

    ![Skärmbild som visar på/av för att delegera åtkomst till fakturor](./media/manage-billing-access/aa-optinallow01.png)

Kontoadministratören kan också konfigurera att fakturor ska skickas via e-post. Mer information finns i [Få fakturan via e-post](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Ge skrivskyddad behörighet till fakturering

Tilldela rollen Faktureringsläsare till någon som behöver skrivskyddad åtkomst till faktureringsinformationen för prenumerationen men inte möjligheten att hantera eller skapa Azure-tjänster. Den här rollen är lämplig för användare i en organisation som ansvarar för ekonomi- och kostnadshanteringen för Azure-prenumerationer.

Funktionen Faktureringsläsare är i förhandsversion och har ännu inte stöd för icke-globala moln.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som kontoadministratör.

1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Välj **Prenumerationer** i den vänstra rutan. Beroende på din åtkomst kan du behöva välja ett faktureringsomfång och sedan välja **Prenumerationer**.

    ![Skärmbild som visar val av prenumerationer](./media/manage-billing-access/billing-select-subscriptions.png)

1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till** längst upp på sidan.

    ![Skärmbild som visar hur du klickar för att lägga till rolltilldelning](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. I listrutan **Roll** väljer du **Faktureringsläsare**.
1. I rutan **Välj** skriver du namn eller e-postadress för den användare som du vill lägga till.
1. Välj användaren.
1. Välj **Spara**.
    ![Skärmbild som visar knappen Spara.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Efter en liten stund tilldelas användaren rollen Faktureringsläsare för prenumerationen.

** Om du är EA-kund kan en kontoägare eller en avdelningsadministratör tilldela rollen Faktureringsläsare till teammedlemmar. Men för att den faktureringsläsaren ska kunna visa faktureringsinformation för avdelningen eller kontot måste företagsadministratören aktivera principer för **AO-visningsdebitering** eller **DA-visningsdebitering** i Enterprise-portalen.

## <a name="check-the-type-of-your-billing-account"></a>Kontrollera typen av ditt faktureringskonto
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Nästa steg

- Användare i andra roller, till exempel Ägare eller Deltagare, kan inte bara komma åt faktureringsinformation, utan även Azure-tjänster. Information om hur du hanterar dessa roller finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).
- Mer information om roller finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
