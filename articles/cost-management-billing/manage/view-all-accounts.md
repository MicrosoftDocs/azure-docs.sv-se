---
title: Visa dina faktureringskonton på Azure-portalen
description: Lär dig hur du visar faktureringskonton i Azure-portalen. Se omfångsinformation för Enterprise-avtal, Microsoft-kundavtal och Microsoft-partneravtal.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: a9e3297ad055157d552424ecc48db6801f43b220
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929963"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Faktureringskonton och omfång på Azure-portalen

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kan till exempel ha registrerat dig för Azure för dina personliga projekt. Du kan även ha åtkomst via organisationens Enterprise-avtal eller Microsoft-kundavtal. I den här situationen skulle du ha två separata faktureringskonton.

Azure-portalen stöder följande typer av faktureringskonton:

- **Microsoft Online Services-programmet**: Ett faktureringskonto för ett Microsoft Online Services-program skapas när du registrerar dig för Azure via Azure-webbplatsen. Det gäller till exempel när du registrerar dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) eller ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller som [Visual studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise-avtal**: Ett faktureringskonto för ett Enterprise-avtal skapas när din organisation tecknar ett [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) för att använda Azure. Du kan ha högst 2000 prenumerationer i ett Enterprise-avtal.

- **Microsoft-kundavtal**: Ett faktureringskonto för ett Microsoft-kundavtal skapas när din organisation arbetar med en Microsoft-representant för att teckna ett Microsoft-kundavtal. Vissa kunder i utvalda regioner som registrerar sig via Azure-webbplatsen för ett [konto med Betala per användning-priser](https://azure.microsoft.com/offers/ms-azr-0003p/) eller ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) kan även ha ett faktureringskonto för ett Microsoft-kundavtal. Du kan ha högst 20 prenumerationer i ett Microsoft-kundavtal för en användare. Ett Microsoft-kundavtal för ett företag har ingen gräns för antalet prenumerationer. Mer information finns i artikeln om att [komma igång med ditt faktureringskonto för Microsoft-kundavtal](../understand/mca-overview.md).

- **Microsoft-partneravtal**: Ett faktureringskonto för ett Microsoft-partneravtal skapas för CSP-partner (leverantörer av molnlösningar) för kundhantering i den nya köpupplevelsen. Partner måste ha minst en kund med en [Azure-plan](/partner-center/purchase-azure-plan) för att kunna hantera sina faktureringskonton på Azure-portalen. Mer information finns i artikeln om [hur du kommer igång med ditt faktureringskonto för Microsoft-partneravtal](../understand/mpa-overview.md).

Information om hur du avgör typen av ditt faktureringskonto finns i [Kontrollera typen av ditt faktureringskonto](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Omfång för faktureringskonton
Ett omfång är en nod i ett faktureringskonto som du använder för att visa och hantera fakturering. Det är där du hanterar faktureringsdata, betalningar och fakturor samt sköter allmän kontohantering.

Om du inte har åtkomst att visa eller hantera faktureringskonton har du förmodligen inte åtkomstbehörighet. Du kan be din administratör för faktureringskonton att ge dig åtkomst. Mer information finns i följande artiklar:

- [Microsoft Online Services-programåtkomst](manage-billing-access.md)
- [Microsoft-kundavtalsåtkomst](understand-mca-roles.md)
- [Enterprise-avtalsåtkomst](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programmet

![Skärmbild som visar MOSP-hierarki](./media/view-all-accounts/mosp-hierarchy.png)

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto     | Representerar ett avtal som en kund accepterar för att använda Azure. Det innehåller en eller flera prenumerationer.  |
|Prenumeration     |  Representerar en gruppering av Azure-resurser. Fakturan genereras i detta omfång. Annan faktureringsinformation som betalningsmetoder och användningsadress är kopplade till det här omfånget.|

### <a name="enterprise-agreement"></a>Enterprise-avtal

![Skärmbild som visar EA-hierarki](./media/view-all-accounts/ea-hierarchy.png)

|Omfång  |Definition  |
|---------|---------|
|Faktureringskonto    | Representerar en Enterprise-avtalsregistrering. Det innehåller en eller flera avdelningar och konton. Fakturan genereras i detta omfång. |
|Avdelning     |  Valfri gruppering av konton för att segmentera kostnader i logiska grupper och ange budget.     |
|Konto     |  Representerar en enskild kontoägare. Kontoägare har behörighet att skapa och hantera Azure-prenumerationer som debiteras för registreringen. |

### <a name="microsoft-customer-agreement"></a>Microsoft-kundavtal

![Skärmbild som visar MCA-hierarki](./media/view-all-accounts/mca-hierarchy.png)

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett avtal som en kund accepterar för att använda Microsofts produkter och tjänster. Det innehåller en eller flera faktureringsprofiler. |
|Faktureringsprofil     |   Representerar en faktura och relaterad faktureringsinformation som betalningsmetoder och faktureringsadress. Den innehåller ett eller flera fakturaavsnitt. |
|Fakturaavsnitt     |   Representerar en grupp med kostnader i en faktura. Azure-prenumerationer och andra köp, till exempel Azure Marketplace- och App Source-produkter, är associerade med det här omfånget.    |

### <a name="microsoft-partner-agreement"></a>Microsoft-partneravtal

![Skärmbild som visar MPA-hierarki](./media/view-all-accounts/mpa-hierarchy.png)

|Omfång  |Aktiviteter  |
|---------|---------|
|Faktureringskonto     |   Representerar ett partneravtal för att hantera kunders Microsoft-produkter och -tjänster i den nya köpupplevelsen. Det innehåller en eller flera faktureringsprofiler och kunder.   |
|Faktureringsprofil     |   Representerar en faktura för en valuta.     |
|Kund    |   Representerar en kund för en CSP-partner (leverantör av molnlösningar).  Azure-prenumerationer och andra köp, till exempel Azure Marketplace- och App Source-produkter, är associerade med det här omfånget.  |
|Återförsäljare    |   Återförsäljare som tillhandahåller tjänster till en kund. Det är ett valfritt fält för en prenumeration och gäller endast för indirekta leverantörer i CSP-modellen med två nivåer.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Byta faktureringsomfång i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen efter Kostnadshantering + fakturering.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. På sidan Översikt väljer du **Växla omfång**.

   ![Skärmbild som visar faktureringsomfång.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Du ser inte Växla omfång om du bara har åtkomst till ett omfång.

4. Välj ett omfång för att visa information.

   ![Skärmbild som visar faktureringsomfång, som du kan välja för att få mer information.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Kontrollera vilken typ av konto du har
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du börjar [analysera dina kostnader](../costs/quick-acm-cost-analysis.md).