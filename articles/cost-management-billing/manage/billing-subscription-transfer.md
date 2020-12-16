---
title: Överföra faktureringsägarskapet för en Azure-prenumeration
description: Beskriver hur du överför faktureringsägarskap för en Azure-prenumeration till ett annat konto.
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: d37225f988cc906bdd92467afcf3f286220eebda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027874"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto

Den här artikeln innehåller de steg som behövs för att överföra faktureringsägarskap för en Azure-prenumeration till ett annat konto. Innan du överför faktureringsägarskapet för en prenumeration bör du läsa [Om att överföra faktureringsägarskap för en Azure-prenumeration](../understand/subscription-transfer.md).

Om du vill behålla faktureringsägarskapet men ändra prenumerationstypen kan du läsa mer i [Växla din Azure-prenumeration till ett annat erbjudande](switch-azure-offer.md). Information om hur du kontrollerar vem som kan komma åt resurser i prenumerationen finns i avsnittet om [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md).

Om du är Enterprise-avtalskund (EA) kan din företagsadministratör överföra faktureringsägarskapet för dina prenumerationer mellan konton. Mer information finns i [Ändra Azure-prenumeration eller -kontoinnehavare](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Endast faktureringsadministratören för ett konto kan överföra ägarskapet för en prenumeration.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Överföra faktureringsägarskapet för en Azure-prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för det faktureringskonto som har den prenumeration som du vill överföra. Om du inte är säker på om du är administratör, eller om du behöver ta reda på vem som är det, kan du läsa [Fastställa administratör för faktureringskonto](../understand/subscription-transfer.md#whoisaa).
1. Sök efter **Kostnadshantering + fakturering**.  
   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Välj **Prenumerationer** i den vänstra rutan. Beroende på din åtkomst kan du behöva välja ett faktureringsomfång och sedan välja **Prenumerationer** eller **Azure-prenumerationer**.
1. Välj **Överför faktureringsägarskapet** för den prenumeration som du vill överföra.  
   ![Välja den prenumeration som ska överföras](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Ange e-postadressen till en användare som är faktureringsadministratör för det konto som ska bli den nya ägaren till prenumerationen.
1. Om du överför din prenumeration till ett konto i en annan Azure AD-klientorganisation väljer du huruvida du vill flytta prenumerationen till det nya kontots klientorganisation. Mer information finns i [Överföra en prenumeration till ett konto i en annan Azure AD-klientorganisation](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Om du väljer att flytta prenumerationen till det nya kontots Azure AD-klient tas alla [tilldelningar av Azure-roller](../../role-based-access-control/role-assignments-portal.md) som ger åtkomst till resurser i prenumerationen bort permanent. Endast den användare i det nya kontot som godkänner din överföringsbegäran får åtkomst till att hantera resurser i prenumerationen. Du kan även avmarkera alternativet **Azure AD-klientorganisation för prenumeration** om du vill överföra faktureringsägarskapet utan att flytta prenumerationen till det nya kontots klientorganisation. Om du gör det bevaras befintliga Azure-rolltilldelningar för åtkomst till Azure-resurser.  
    ![Sidan för att skicka överföring](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Välj **Skicka överföringsbegäran**.
1. Användaren får ett e-postmeddelande med instruktioner för att granska din överföringsförfrågan.  
   ![E-postmeddelande om överföring av prenumeration skickas till mottagaren](./media/billing-subscription-transfer/billing-receiver-email.png)
1. För att godkänna överföringsbegäran väljer användaren länken i e-postmeddelandet och följer anvisningarna. Användaren väljer sedan en betalningsmetod som ska användas för att betala för prenumerationen. Användare som inte har ett Azure-konto måste registrera sig för ett nytt konto.  
   ![Webbplats för första prenumerationsöverföring](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Webbplats för andra prenumerationsöverföring](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Webbplats för tredje prenumerationsöverföring](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Lyckades! Prenumerationen har nu överförts.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Överföra en prenumeration till ett konto för en annan Azure AD-klientorganisation

En Azure Active Directory-klientorganisation (AD) skapas åt dig när du registrerar dig för Azure. Klientorganisationen representerar ditt konto. Du använder klientorganisationen för att hantera åtkomst till dina prenumerationer och resurser.

När du skapar en ny prenumeration värdhanteras den i ditt kontos Azure AD-klientorganisation. Om du vill ge andra åtkomst till din prenumeration eller dess resurser måste du bjuda in dem till att gå med i din klientorganisation. Detta hjälper dig att kontrollera åtkomsten till dina prenumerationer och resurser.

När du överför faktureringsägarskap för din prenumeration till ett konto i en annan Azure AD-klientorganisation kan du flytta prenumerationen till det nya kontots klientorganisation. Om du gör det kommer alla användare, grupper eller tjänsthuvudnamn som hade [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) för att hantera prenumerationer och deras resurser att förlora sin åtkomst. Endast den användare i det nya kontot som godkänner din överföringsbegäran får åtkomst till att hantera resurserna. För att de användare som ursprungligen hade åtkomst ska fortsätta ha åtkomst måste den nya ägaren lägga till dessa användare i prenumerationen manuellt. Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Överföra prenumerationer på Visual Studio och Partner Network

Prenumerationer på Visual Studio och Microsoft Partner Network är associerade med månatlig återkommande Azure-kredit. När du överför dessa prenumerationer är din kredit inte tillgänglig på målfaktureringskontot. Prenumerationen använder krediten i målfaktureringskontot. Vi tar exemplet att Bob överför en Visual Studio Enterprise-prenumeration till Janes konto den 9 september och Jane godkänner överföringen. När överföringen är klar börjar prenumerationen använda kredit i Janes konto. Krediten nollställs den nionde dagen varje månad.

## <a name="next-steps-after-accepting-billing-ownership"></a>Nästa steg när faktureringsägarskapet har godkänts

Om du har godkänt faktureringsägarskapet för en Azure-prenumeration rekommenderar vi att du läser dessa nästa steg:

1. Granska och uppdatera Azure-rolltilldelningarna för tjänstadministratörer och medadministratörer. Mer information finns i [Lägga till eller ändra administratörer för en Azure-prenumeration](add-change-subscription-administrator.md) och [Lägga till eller ta bort Azure-rolltilldelningar från Azure-portalen](../../role-based-access-control/role-assignments-portal.md).
1. Uppdatera autentiseringsuppgifter som är associerade med den här prenumerationens tjänster, däribland:
   1. Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Åtkomstnycklar för tjänster såsom Storage. Mer information finns i [Om Azure Storage-konton](../../storage/common/storage-account-create.md)
   1. Autentiseringsuppgifter för fjärråtkomst för tjänster såsom Azure Virtual Machines.
1. Om du arbetar med en partner kan du överväga att uppdatera partner-ID i prenumerationen. Du kan uppdatera partner-ID i [Azure-portalen](https://portal.azure.com). Mer information finns i [Länka ett partner-ID till dina Azure-konton](link-partner-id.md)

## <a name="troubleshooting"></a>Felsökning

Använd följande felsökningsinformation om det uppstår problem med överföring av prenumerationer.

### <a name="the-transfer-subscription-option-is-unavailable"></a>Alternativet ”Överför prenumeration” är inte tillgängligt

<a name="no-button"></a> 

Prenumerationsöverföring via självbetjäning är inte tillgänglig för ditt faktureringskonto. För närvarande stöder vi inte överföring av faktureringsägarskap för prenumerationer i Enterprise-avtalskonton (EA) i Azure-portalen. Dessutom har inte Microsoft Customer Agreement-konton som skapas vid arbete med en Microsoft-representant stöd för överföring av faktureringsägarskap.

###  <a name="not-all-subscription-types-can-transfer"></a>Det är inte alla typer av prenumerationer som kan överföras

Det är inte alla prenumerationstyper som stöder överföring av faktureringsägarskap. En lista över prenumerationstyper som stöder överföring finns i [Prenumerationstyper som stöds](../understand/subscription-transfer.md#supported-subscription-types)

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Fel med nekad åtkomst visas vid försök att överföra ägarskap av prenumerationsfakturering

Det här felet uppstår om du försöker överföra en prenumeration på Microsoft Azure-plan och du inte har den behörighet som krävs. För att kunna överföra en prenumeration på Microsoft Azure-plan måste du vara ägare eller deltagare i det fakturaavsnitt som prenumerationen faktureras till. Mer information finns i [Hantera prenumerationer för fakturaavsnitt](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Granska och uppdatera Azure-rolltilldelningarna för tjänstadministratörer och medadministratörer. Mer information finns i [Lägga till eller ändra administratörer för en Azure-prenumeration](add-change-subscription-administrator.md) och [Lägga till eller ta bort Azure-rolltilldelningar från Azure-portalen](../../role-based-access-control/role-assignments-portal.md).