---
title: Faktureringsroller för Microsofts-kundavtal – Azure
description: Lär dig mer om faktureringsroller för faktureringskonton i Azure för Microsoft-kundavtal.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 12/17/2020
ms.author: banders
ms.openlocfilehash: 511c0ecdef45a882bcc3a427d5ec602b3ebabc66
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673839"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Förstå administrativa roller för Microsoft-kundavtal i Azure

För hantering av ditt faktureringskonto för ett Microsoft-kundavtal använder du de roller som beskrivs i följande avsnitt. De här rollerna gäller utöver de fördefinierade roller som finns i Azure för kontroll av åtkomst till resurser. Mer information finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. Kontrollera om du har åtkomst till ett Microsoft-kundavtal.

## <a name="billing-role-definitions"></a>Definitioner av faktureringsroller

I följande tabell beskrivs de faktureringsroller som du använder för att hantera faktureringskonto, faktureringsprofiler och fakturaavsnitt.

|Roll|Beskrivning|
|---|---|
|Faktureringskontoägare|Hantera allt för faktureringskontot|
|Faktureringskontodeltagare|Hantera allt förutom behörigheter på faktureringskontot|
|Faktureringskontoläsare|Skrivskyddad vy över allt på faktureringskontot|
|Faktureringsprofilsägare|Hantera allt för faktureringsprofilen|
|Faktureringsprofilsdeltagare|Hantera allt förutom behörigheter i faktureringsprofilen|
|Faktureringsprofilsläsare|Skrivskyddad vy över allt i faktureringsprofilen|
|Fakturaansvarig|Visa fakturor för faktureringsprofilen|
|Fakturaavsnittsägare|Hantera allt i fakturaavsnittet|
|Fakturaavsnittsdeltagare|Hantera allt förutom behörigheter i fakturaavsnittet|
|Fakturaavsnittsläsare|Skrivskyddad vy över allt i fakturaavsnittet|
|Azure-prenumerationsskapare|Skapa Azure-prenumerationer|

## <a name="billing-account-roles-and-tasks"></a>Roller och uppgifter för faktureringskonto

Med ett faktureringskonto kan du hantera faktureringen för din organisation. Du använder faktureringskontot till att organisera kostnader, övervaka avgifter och fakturor samt kontrollera faktureringsåtkomsten för din organisation. Mer information finns i [Förstå faktureringskonton](../understand/mca-overview.md#your-billing-account).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för faktureringskontot.

### <a name="manage-billing-account-permissions-and-properties"></a>Hantera behörigheter och egenskaper för faktureringskonto

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa befintliga behörigheter för faktureringskontot|✔|✔|✔|
|Ge andra behörigheter att visa och hantera faktureringskontot|✔|✘|✘|
|Visa egenskaper för faktureringskonto såsom företagsnamn, adress och annat|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>Hantera faktureringsprofiler för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla faktureringsprofiler i kontot|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>Hantera fakturor för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla fakturor i kontot|✔|✔|✔|
|Ladda ned fakturor, filer för Azure-användning och -avgifter, prisdokument och skattedokument i kontot|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>Hantera fakturaavsnitt för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla fakturaavsnitt i kontot|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>Hantera transaktioner för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla faktureringstransaktioner för kontot|✔|✔|✔|
|Visa alla produkter som har köpts för kontot|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>Hantera prenumerationer för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla Azure-prenumerationer på faktureringskontot|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>Roller och uppgifter för faktureringsprofil

Med en faktureringsprofil kan du hantera dina fakturor och betalningsmetoder. En månadsfaktura skapas för Azure-prenumerationerna och andra produkter som köpts med hjälp av faktureringsprofilen. Du kan använda betalningsmetoderna till att betala fakturan. Mer information finns i [Förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för faktureringsprofilen.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Hantera behörigheter, egenskaper och principer för faktureringsprofil

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa befintliga behörigheter för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Ge andra behörigheter att visa och hantera faktureringsprofilen|✔|✘|✘|✘|✘|✘|✘|
|Visa egenskaper för faktureringsprofil såsom IO-nummer, inställningar för e-postfaktura och mer|✔|✔|✔|✔|✔|✔|✔|
|Uppdatera egenskaper för faktureringsprofil |✔|✔|✘|✘|✘|✘|✘|
|Visa principer som tillämpas på faktureringsprofilen, till exempel aktivering av Azure-reservationsinköp, aktivering av Azure Marketplace-inköp och mer|✔|✔|✔|✔|✔|✔|✔|
|Tillämpa principer på faktureringsprofilen |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>Hantera fakturor för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla fakturor för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Ladda ned fakturor, filer för Azure-användning och -avgifter, prisdokument och skattedokument för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Hantera fakturaavsnitt för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla fakturaavsnitt för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Skapa nytt fakturaavsnitt för faktureringsprofilen|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>Hantera transaktioner för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla faktureringstransaktioner för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>Hantera betalningsmetoder för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa betalningsmetoder för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Spåra Azure-kreditsaldo för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Hantera prenumerationer för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>Roller och uppgifter för fakturaavsnitt

Med ett fakturaavsnitt kan du organisera kostnaderna på din faktura. Du kan skapa ett avsnitt för att organisera dina kostnader efter avdelning, utvecklingsmiljö eller organisationens behov. Ge andra behörighet att skapa Azure-prenumerationer för avsnittet. Eventuella användningsavgifter och inköp för prenumerationerna visas sedan på avsnittet på fakturan. Mer information finns i [Förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för fakturaavsnitt.

### <a name="manage-invoice-section-permissions-and-properties"></a>Hantera behörigheter och egenskaper för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare |
|---|---|---|---|---|---|---|---|
|Visa alla behörigheter för fakturaavsnittet|✔|✔|✔|✔|✔|✔|✔|
|Ge andra behörigheter att visa och hantera fakturaavsnittet|✔|✘|✘|✘|✘|✘|✘|
|Visa egenskaper för fakturaavsnitt|✔|✔|✔|✔|✔|✔|✔|
|Uppdatera egenskaper för fakturaavsnitt|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>Hantera produkter för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla produkter som har köpts i fakturaavsnittet|✔|✔|✔|✘|✔|✔|✔|
|Hantera fakturering för produkter för fakturaavsnitt såsom annullering, avstängning av automatisk förnyelse och mer|✔|✔|✘|✘|✘|✘|✘|
|Ändra fakturaavsnitt för produkterna|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Hantera prenumerationer för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för fakturaavsnitt|✔|✔|✔|✘|✔|✔|✔|
|Ändra fakturaavsnitt för prenumerationerna|✔|✔|✘|✘|✘|✘|✘|
|Begära faktureringsägarskap för prenumerationer från användare i andra faktureringskonton|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Roller och uppgifter för prenumerationsfakturering

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för en prenumeration.

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|
|---|---|---|---|---|
|Skapa Azure-prenumerationer|✔|✔|✘|✔|
|Uppdatera kostnadsställe för prenumerationen|✔|✔|✘|✘|
|Ändra fakturaavsnitt för prenumerationen|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Hantera faktureringsroller i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Välj **Åtkomstkontroll (IAM)** i ett omfång såsom faktureringskonto, faktureringsprofil eller fakturaavsnitt där du vill ge åtkomst.

4. Sidan Åtkomstkontroll (IAM) visar användare och grupper som tilldelas till varje roll för det omfånget.

   ![Skärmbild som visar en lista över administratörer för faktureringskontot](./media/understand-mca-roles/billing-list-admins.png)

5. Om du vill ge åtkomst till en användare väljer du **Lägg till** överst på sidan. I listrutan Roll väljer du en roll. Ange e-postadressen för den användare som du vill ge åtkomst till. Välj **Spara** för att tilldela rollen.

   ![Skärmbild som visar tillägg av en administratör i ett faktureringskonto](./media/understand-mca-roles/billing-add-admin.png)

6. Om du vill ta bort åtkomsten för en användare väljer du användaren med den rolltilldelning som du vill ta bort. Välj Ta bort.

   ![Skärmbild som visar borttagning av en administratör från ett faktureringskonto](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten
Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du läsa mer om ditt faktureringskonto:

- [Komma igång med ditt faktureringskonto för Microsoft-kundavtal](../understand/mca-overview.md)
- [Skapa en Azure-prenumeration för ditt faktureringskonto för Microsoft-kundavtal](create-subscription.md)
