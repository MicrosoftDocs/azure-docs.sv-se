---
title: Vanliga frågor och svar om SaaS-API – Microsoft Commercial Marketplace
description: Läs om flera av integrerings kraven för Microsofts kommersiella marknads plats för att ge Azure-kunder möjlighet att prenumerera på SaaS-erbjudanden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/19/2021
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 760e7210d054e44dfec6d6a6e480baecd04d6807
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044132"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Vanliga frågor om API: er för SaaS-utförande

I den här artikeln beskrivs flera av integrerings kraven för Microsofts kommersiella marknads plats för att ge Azure-kunder möjlighet att prenumerera på SaaS-erbjudanden.

## <a name="discovery-experience"></a>Identifierings upplevelse

När ett SaaS-erbjudande har publicerats kan Azure-användare upptäcka det på Azure Marketplace. Dina kunder kan filtrera erbjudanden baserat på produkt typ (SaaS) och identifiera de SaaS-tjänster som de är intresserade av.

## <a name="purchase-experience"></a>Köp upplevelse

När en användare är intresse rad av en viss SaaS-tjänst kan användaren prenumerera på den från Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Vad innebär det för en Azure-användare att prenumerera på ett SaaS-erbjudande på Azure Marketplace?

Det innebär att en användare kan visa användnings villkoren och sekretess policyn som är associerad med SaaS-tjänsten och samtycker till att betala för den enligt de fakturerings villkor som du har angett av dig, utgivaren av SaaS-erbjudandet på Microsofts faktura. Användare kan använda sin befintliga betalnings profil i Azure för att betala för SaaS service-förbrukning.

Den här möjligheten är att bli fördelaktig på flera olika sätt. Kunder kan nu identifiera och prenumerera på en enda plats med hjälp av Microsoft Cloud plattform som en betrodd källa, utan att behöva Undersök varje ISV-programvara som den tänker använda. Kunder kan också använda sina befintliga betalnings profiler utan att behöva betala varje ISV-programvara separat.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Debiteras användaren automatiskt när erbjudandet prenumereras?

När du prenumererar på SaaS-erbjudandet har användaren enats om att betala för konsumtion av SaaS-tjänsten via Microsoft-plattformen. Avgifterna kommer dock bara att starta när erbjudandet förbrukas. Användaren måste gå till SaaS-erbjudandet och bekräfta att kontot skapas för att börja använda erbjudandet. Sedan meddelar du Microsoft att starta faktureringen för den här kunden SaaS-prenumerationen.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hur får du ett meddelande när en användare prenumererar på ditt SaaS-erbjudande?

När du prenumererar på ett erbjudande kan Azure-användaren identifiera och hantera alla erbjudanden i Azure. Som standard visas statusen för ett nyligen prenumererat SaaS-erbjudande som **etablering, som väntar på att utföras**. I det här läget uppmanas Azure-användaren att ange en åtgärd för att **Konfigurera kontot**, så att du kan bläddra till sin SaaS-prenumerations hanterings upplevelse i Azure Portal.

När användaren väljer **Konfigurera konto** omdirigeras de till SaaS-tjänstens webbplats. Utgivaren konfigurerade URL: en vid tidpunkten för publiceringen av erbjudandet. Den här sidan kallas utgivarens landnings sida. Azure-användare loggar in på SaaS-landnings sidan baserat på befintliga Azure Active Directory-autentiseringsuppgifter (Azure AD) i Azure.

> [!IMPORTANT]
> Du måste logga in på Köp användaren med Azure Active Directory enkel inloggning (Azure AD SSO) enligt [principen](/legal/marketplace/certification-policies?context=/azure/marketplace/context/context). `mail`Egenskapen för den användar resurs som hämtades från Microsoft Graph API ger dig kontakt information för Azure AD och `userPrincipalName` för MSA. Det är möjligt att fältet "mail" är tomt för Azure AD och att användaren inte har något registrerat e-postmeddelande. Om så är fallet rekommenderar vi att du identifierar och ber om ett e-postmeddelande för kontakter. Det här är din enda chans att få en kontakt med e-post för att nå en kund under eller efter den pågående kunden.

När Azure-användaren omdirigeras till landnings sidan läggs en token till i fråge-URL: en. Den här token är kort livs längd och giltig under 24 timmar. Du kan sedan identifiera förekomsten av denna token och anropa Microsofts API för att få mer kontext som är associerad med token.

![Kund prenumerations flöde](media/saas-metering-service-integration-flow-a.png)

Mer information om API-kontraktet för hantering av Transact-scenarier i livs cykeln för ett SaaS-erbjudande finns i [API för uppfyllande av SaaS](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Hur känner du till SaaS-erbjudandet som användaren prenumererar på i Azure?

Svaret på `Resolve` API: et inkluderar erbjudandet och planerings informationen som är associerad med SaaS-prenumerationen.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hur kan Azure-användaren ändra planen som är kopplad till den här Azure-prenumerationen?

* Azure-användaren kan ändra planen som är associerad med SaaS-prenumerationen direkt i SaaS-upplevelsen eller via Microsoft-plattformen.

* Konverteringar kan göras när som helst under fakturerings perioden. Du uppmanas att bekräfta eventuella konverteringar som börjar gälla när de har godkänts.

* Förbetald plan (**månatlig** eller **årlig**) taxa är proportionellt. Överförda överanvändning till tiden för konvertering debiteras i nästa faktura. Nya överförbrukningar kommer att genereras baserat på den nya planen.

>[!Note]
>Du kan blockera nedgradering om du inte vill stödja vissa konverterings Sök vägar.

Sekvensen nedan fångar flödet när en Azure-kund ändrar en plan i SaaS-upplevelsen:

![Ändra flöde för kund plan](media/saas-metering-service-integration-flow-b.png)

Sekvensen nedan fångar flödet när en Azure-kund ändrar en plan i Microsofts onlinebutik:

![Ändra flöde för kund Online Store plan](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hur kan Azure-användaren avbryta prenumerationen på den plan som är kopplad till Azure-prenumerationen?

En Azure-användare kan avbryta prenumerationen på ett köpt SaaS-erbjudande antingen direkt i SaaS-upplevelsen eller via Microsoft-plattformen. När användaren avbryter prenumerationen kommer de inte längre att debiteras från nästa fakturerings period.

Sekvensen nedan fångar flödet när en Azure-kund avbryter prenumerationen på SaaS-erbjudandet i SaaS-upplevelsen:

![Kunden avbryter prenumerationen på SaaS-upplevelsen](media/saas-metering-service-integration-flow-d.png)

Sekvensen nedan fångar flödet när Azure-användare avbryter prenumerationer i Microsofts onlinebutik:

![Kunden slutar prenumerera i Microsofts onlinebutik](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Nästa steg

[API:er för Marketplace Metering Service](./marketplace-metering-service-apis.md)
