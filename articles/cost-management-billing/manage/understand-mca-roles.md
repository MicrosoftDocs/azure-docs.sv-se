---
title: Faktureringsroller för Microsofts-kundavtal – Azure
description: Lär dig mer om faktureringsroller för faktureringskonton i Azure för Microsoft-kundavtal.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: e334a423fd11aa3a357d52099a792dcc905aedeb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011671"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Förstå administrativa roller för Microsoft-kundavtal i Azure

För hantering av ditt faktureringskonto för ett Microsoft-kundavtal använder du de roller som beskrivs i följande avsnitt. De här rollerna gäller utöver de fördefinierade roller som finns i Azure för kontroll av åtkomst till resurser. Mer information finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Titta på videon [Hantera åtkomst till ditt MCA-fakturerings konto](https://www.youtube.com/watch?v=9sqglBlKkho) och lär dig hur du kan styra åtkomsten till ditt Microsoft Customer Agreement (MCA)-fakturerings konto.

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

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
|Fakturaansvarig|Visa och betala fakturor för faktureringsprofilen|
|Fakturaavsnittsägare|Hantera allt i fakturaavsnittet|
|Fakturaavsnittsdeltagare|Hantera allt förutom behörigheter i fakturaavsnittet|
|Fakturaavsnittsläsare|Skrivskyddad vy över allt i fakturaavsnittet|
|Azure-prenumerationsskapare|Skapa Azure-prenumerationer|

## <a name="billing-account-roles-and-tasks"></a>Roller och uppgifter för faktureringskonto

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot till att hantera fakturor och betalningar samt spåra kostnader. Rollerna på fakturerings kontot har den högsta behörighets nivån och användare i dessa roller får insyn i kostnads-och fakturerings informationen för hela kontot. Tilldela bara dessa roller till användare som behöver Visa fakturor och spåra kostnader för hela ditt konto, till exempel medlem i ekonomi och redovisnings team. Mer information finns i [Förstå faktureringskonton](../understand/mca-overview.md#your-billing-account).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för faktureringskontot.

### <a name="manage-billing-account-permissions-and-properties"></a>Hantera behörigheter och egenskaper för faktureringskonto

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa roll tilldelningar för fakturerings konto|✔|✔|✔|
|Ge andra behörigheter att visa och hantera faktureringskontot|✔|✘|✘|
|Visa egenskaper för fakturerings konto, till exempel adress, avtal med mera|✔|✔|✔|
|Uppdatera egenskaper för fakturerings konto, t. ex. såld till, visnings namn, med mera|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Hantera faktureringsprofiler för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla fakturerings profiler för kontot|✔|✔|✔|
|Skapa nya fakturerings profiler|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Hantera fakturor för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla fakturor för kontot|✔|✔|✔|
|Betala fakturor med kredit kort|✔|✔|✘|
|Ladda ned fakturor, Azure-tilläggsfiler, pris dokument och skatte dokument|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Hantera produkter för fakturerings konto

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla produkter som köpts för kontot|✔|✔|✔|
|Hantera fakturering för produkter som Avbryt, inaktivera automatisk förnyelse och mycket mer|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Hantera prenumerationer för faktureringskontot

|Aktivitet|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare|
|---|---|---|---|
|Visa alla Azure-prenumerationer som skapats för fakturerings kontot|✔|✔|✔|
|Skapa nya Azure-prenumerationer|✔|✔|✘|
|Avbryt Azure-prenumerationer|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Roller och uppgifter för faktureringsprofil

Varje fakturerings konto har minst en fakturerings profil. Din första fakturerings profil konfigureras när du registrerar dig för att använda Azure. En månads faktura skapas för fakturerings profilen och innehåller alla tillhör ande avgifter från föregående månad. Du kan ställa in fler fakturerings profiler utifrån dina behov. Användare med roller på en fakturerings profil kan visa kostnader, ställa in budget och hantera och betala sina fakturor. Tilldela dessa roller till användare som ansvarar för att hantera budget och betala fakturor för fakturerings profilen som medlemmar i teamen för företags administration i din organisation. Mer information finns i [Förstå faktureringsprofiler](../understand/mca-overview.md#billing-profiles).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för faktureringsprofilen.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Hantera behörigheter, egenskaper och principer för faktureringsprofil

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa roll tilldelningar för fakturerings profilen|✔|✔|✔|✔|✔|✔|✔|
|Ge andra behörigheter att visa och hantera faktureringsprofilen|✔|✘|✘|✘|✔|✘|✘|
|Visa egenskaper för fakturerings profil som IO-nummer, fakturering och mer|✔|✔|✔|✔|✔|✔|✔|
|Uppdatera egenskaper för faktureringsprofil |✔|✔|✘|✘|✔|✔|✘|
|Visa principer som tillämpas på fakturerings profilen som Azure reservation-inköp, Azure Marketplace-köp med mera|✔|✔|✔|✔|✔|✔|✔|
|Tillämpa principer på faktureringsprofilen |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Hantera fakturor för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla fakturor för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Betala fakturor med kredit kort|✔|✔|✘|✔|✔|✘|✘|
|Ladda ned fakturor, filer för Azure-användning och -avgifter, prisdokument och skattedokument för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Hantera fakturaavsnitt för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla fakturaavsnitt för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Skapa nytt fakturaavsnitt för faktureringsprofilen|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Hantera produkter för fakturerings profil

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla produkter som köpts för fakturerings profilen|✔|✔|✔|✔|✔|✔|✔|
|Hantera fakturering för produkter som Avbryt, inaktivera automatisk förnyelse och mycket mer|✔|✔|✘|✘|✔|✔|✘|
|Ändra fakturerings profil för produkterna|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Hantera betalningsmetoder för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa betalningsmetoder för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Hantera betalnings metoder som att ersätta kredit kort, koppla från kredit kort med mera|✔|✔|✘|✘|✔|✔|✘|
|Spåra Azure-kreditsaldo för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Hantera prenumerationer för faktureringsprofilen

|Aktivitet|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare|Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare
|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för faktureringsprofilen|✔|✔|✔|✔|✔|✔|✔|
|Skapa nya Azure-prenumerationer|✔|✔|✘|✘|✔|✔|✘|
|Avbryt Azure-prenumerationer|✘|✘|✘|✘|✘|✘|✘|
|Ändra fakturerings profil för Azure-prenumerationer|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Roller och uppgifter för fakturaavsnitt

Varje fakturerings profil innehåller som standard ett faktura avsnitt. Du kan skapa fler faktura avsnitt för att gruppera kostnader på fakturerings profilens faktura.  Användare med roller i en faktura avsnittet kan styra vem som skapar Azure-prenumerationer och gör andra inköp. Tilldela dessa roller till användare som konfigurerar Azure-miljön för team i organisationen, som ingenjörer och tekniska arkitekter. Mer information finns i [Förstå fakturaavsnitt](../understand/mca-overview.md#invoice-sections).

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för fakturaavsnitt.

### <a name="manage-invoice-section-permissions-and-properties"></a>Hantera behörigheter och egenskaper för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare |Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visa roll tilldelningar för faktura avsnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Ge andra behörigheter att visa och hantera fakturaavsnittet|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Visa egenskaper för fakturaavsnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Uppdatera egenskaper för fakturaavsnitt|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Hantera produkter för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare |Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visa alla produkter som har köpts för faktura avsnittet|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Hantera fakturering för produkter som Avbryt, inaktivera automatisk förnyelse och mycket mer|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändra fakturaavsnitt för produkterna|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Hantera prenumerationer för fakturaavsnitt

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare |Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Visa alla Azure-prenumerationer för fakturaavsnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Skapa Azure-prenumerationer|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Avbryt Azure-prenumerationer|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Avsnittet Ändra faktura för Azure-prenumerationen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Begära faktureringsägarskap för prenumerationer från användare i andra faktureringskonton|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Roller och uppgifter för prenumerationsfakturering

Följande tabeller visar vilken roll du behöver för att slutföra uppgifter i kontexten för en prenumeration.

|Aktiviteter|Fakturaavsnittsägare|Fakturaavsnittsdeltagare|Fakturaavsnittsläsare|Azure-prenumerationsskapare|Faktureringsprofilsägare|Faktureringsprofilsdeltagare|Faktureringsprofilsläsare |Fakturaansvarig|Faktureringskontoägare|Faktureringskontodeltagare|Faktureringskontoläsare 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Skapa prenumerationer|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Uppdatera kostnadsställe för prenumerationen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändra fakturaavsnitt för prenumerationen|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändra fakturerings profil för prenumerationen|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Avbryt Azure-prenumerationer|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

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
