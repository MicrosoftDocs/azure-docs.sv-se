---
title: Om att överföra faktureringsägarskap för en Azure-prenumeration
description: I den här artikeln beskrivs de saker du behöver känna till innan du överför faktureringsägarskapet för en Azure-prenumeration till ett annat konto.
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 746845004a7a1baf1cafb872aae9322177879b6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011110"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Om att överföra faktureringsägarskap för en Azure-prenumeration

I den här artikeln beskrivs de saker du behöver känna till innan du överför faktureringsägarskapet för en Azure-prenumeration till ett annat konto. 

Du vill kanske överföra faktureringsägarskapet för din Azure-prenumeration om du lämnar din organisation eller om du vill att din prenumeration ska faktureras till ett annat konto. Genom att överföra faktureringsägarskapet till ett annat konto kan du ge administratörerna i det nya kontot behörighet till faktureringsuppgifter. De kan ändra betalningsmetoden, visa avgifter och avbryta prenumerationen.

Om du vill behålla faktureringsägarskapet men ändra typen för din prenumeration kan du läsa mer i [Ändra din Azure-prenumeration till ett annat erbjudande](../manage/switch-azure-offer.md). Information om hur du kontrollerar vem som kan komma åt resurser i prenumerationen finns i avsnittet om [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Om du är Enterprise-avtalskund (EA) kan ditt företags administratörer överföra faktureringsägarskapet för dina prenumerationer mellan konton.

Endast faktureringsadministratören för ett konto kan överföra ägarskapet för en prenumeration.

## <a name="determine-if-you-are-a-billing-administrator"></a>Ta reda på om du är fakturerings administratör

<a name="whoisaa"></a>

För att kunna utföra överföringen letar du reda på den person som har åtkomst till att hantera faktureringen för ett konto. Personen har behörighet att komma åt fakturering på [Azure-portalen](https://portal.azure.com) och utföra olika faktureringsuppgifter, till exempel skapa prenumerationer, visa och betala fakturor eller uppdatera betalningsmetoder.

### <a name="check-if-you-have-billing-access"></a>Kontrol lera om du har fakturerings behörighet

1. Om du vill identifiera konton som du har fakturerings behörighet för, går du till [Cost Management + fakturerings sida i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).

2. Välj **fakturerings konton** på menyn till vänster.

3. Sidan lista med **fakturerings omfång** visar alla prenumerationer där du har åtkomst till fakturerings informationen.

### <a name="check-by-subscription"></a>Check efter prenumeration

1. Om du inte är säker på vem kontoadministratören är för en prenumeration kan du gå till sidan [Prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). 

2. Välj den prenumeration som du vill kontrol lera.

3. Under rubriken **Inställningar** väljer du **Egenskaper**. I rutan **konto administratör** kan du förstå vem som är konto administratör för prenumerationen.

   > [!NOTE]
   > Alla typer av prenumerationer visar inte egenskaperna.

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Prenumerationsöverföring i Azure-portalen är tillgänglig för de prenumerationstyper som anges nedan. För närvarande stöds inte överföring för prenumerationerna [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/offers/ms-azr-0044p/) och [Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). En tillfällig lösning beskrivs i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md). För överföring av andra prenumerationer, som supportplaner, [kontaktar du Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Prenumeranter på Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN-plattformar](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Använda EA-portalen.

<sup>2</sup> Stöds endast för konton som skapas vid registreringen på Azure-webbplatsen.

## <a name="resources-transferred-with-subscriptions"></a>Resurser som överförs med prenumerationer

Alla dina resurser såsom virtuella datorer, diskar och webbplatser överförs till det nya kontot. Om du överför en prenumeration till ett konto i en annan Azure AD-klientorganisation kommer dock eventuella [administratörsroller](../manage/add-change-subscription-administrator.md) och [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) i prenumerationen inte att överföras. [Appregistreringar](../../active-directory/develop/quickstart-register-app.md) och andra klientorganisationsspecifika tjänster överförs inte heller med prenumerationen.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Överföra kontoägarskap till ett annat land eller en annan region

Tyvärr kan du inte överföra prenumerationer mellan länder eller regioner med hjälp av Azure-portalen. De kan dock överföras om du öppnar en Azure-supportbegäran. Om du vill skapa en supportbegäran [kontaktar du supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Överföra en prenumeration från ett konto till ett annat

Om du är administratör för två konton kan du överföra en prenumeration mellan dina konton. Tekniskt sett betraktas dina konton som konton för två olika användare. Därmed kan du överföra prenumerationer mellan dina konton.
Om du vill se de steg som krävs för att överföra din prenumeration kan du läsa [Överföra faktureringsägarskapet för en Azure-prenumeration](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Överföring av en prenumeration bör inte leda till avbrottstid

Om du överför en prenumeration till ett konto i samma Azure AD-klientorganisation påverkas inte de resurser som körs i prenumerationen. Kontextinformation som sparas i PowerShell uppdateras däremot inte, så du kan behöva rensa den eller ändra inställningarna. Om du överför prenumerationen till ett konto i en annan klientorganisation och väljer att flytta prenumerationen till klientorganisationen kommer alla användare, grupper och tjänsthuvudnamn som hade [tilldelats roller i Azure](../../role-based-access-control/role-assignments-portal.md) för att komma åt resurser i prenumerationen att förlora sin åtkomst. Detta kan resultera i stilleståndstid i tjänsten.

## <a name="new-account-usage-and-billing-history"></a>Ny kontoanvändning och faktureringshistorik

Den enda information som är tillgänglig för användare vad gäller det nya kontot är den senaste månadens kostnad för din prenumeration. Resten av användnings- och faktureringshistoriken överförs inte med prenumerationen.

## <a name="manually-migrate-data-and-services"></a>Migrera data och tjänster manuellt

När du överför en prenumeration följer dess resurser med. Om du inte kan överföra ägarskapet för prenumerationen kan du migrera dess resurser manuellt. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Återstående prenumerationskrediter 

Om du har en Visual Studio- eller Microsoft Partner Network-prenumeration får du månatliga krediter. Din kredit överförs inte med prenumerationen i det nya kontot. Den användare som godkänner överföringsbegäran behöver ha en Visual Studio-licens för att godkänna överföringsbegäran. Prenumerationen använder den Visual Studio-kredit som är tillgänglig i användarens konto. Mer information finns i [Överföra prenumerationer på Visual Studio och Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Användare behåller åtkomst till överförda resurser

Tänk på att användare med åtkomst till resurser i en prenumeration behåller sin åtkomst när ägarskapet överförs. [Administratörsroller](../manage/add-change-subscription-administrator.md) och [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) kan dock tas bort. Åtkomsten förloras när kontot är i en annan Azure AD-klientorganisation än prenumerationens klientorganisation och användaren som skickade överföringsbegäran flyttar prenumerationen till ditt kontos klientorganisation. 

I Azure-portalen kan du se de användare som har Azure-rolltilldelningar för att komma åt resurser i prenumerationen. Gå till [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Välj sedan den prenumeration som du vill kontrollera och välj sedan **Åtkomstkontroll (IAM)** i den vänstra rutan. Välj därefter **Rolltilldelningar** överst på sidan. På sidan för rolltilldelningar visas alla användare som har åtkomst i prenumerationen.

Även om [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) tas bort under överföringen kan användare under det ursprungliga ägarkontot fortfarande ha åtkomst till prenumerationen via andra säkerhetsmekanismer, t.ex.:

* Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../../cloud-services/cloud-services-certs-create.md).
* Åtkomstnycklar för tjänster som Storage. Mer information finns i [Om Azure Storage-konton](../../storage/common/storage-account-create.md).
* Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa åtkomsten till resurser bör denne överväga att uppdatera eventuella hemligheter som är associerade med tjänsten. De flesta resurser kan uppdateras. Logga in på [Azure-portalen](https://portal.azure.com) och välj sedan **Alla resurser** i hubbmenyn. Välj sedan resursen. På resurssidan väljer du sedan **Inställningar**. Där kan du visa och uppdatera befintliga hemligheter.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Du betalar för användning när du får ägarskap

Ditt konto ansvarar för betalning för all användning som rapporteras från tiden för överföring och framåt. Det kan finnas en viss användning som ägde rum före överföringen men som rapporterades efteråt. Användningen ingår i fakturan för ditt konto.

## <a name="use-a-different-payment-method"></a>Använda en annan betalningsmetod

När du godkänner överföringsbegäran kan du välja en befintlig betalningsmetod som är kopplad till ditt konto eller lägga till en ny betalningsmetod.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Överför prenumerationsägarskap för Enterprise-avtal

Företagsadministratörer kan uppdatera kontoägarskapet för alla konton, även efter att en ursprunglig kontoinnehavare inte längre ingår i organisationen. Mer information om hur du överför Azure Enterprise-avtalskonton finns i [Azure Enterprise-överföringar](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Överföra faktureringsägarskapet för en Azure-prenumeration](../manage/billing-subscription-transfer.md)
