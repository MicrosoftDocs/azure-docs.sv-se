---
title: Ändra kontaktuppgifter för ett Azure-faktureringskonto
description: Beskriver hur du ändrar kontaktuppgifterna för ditt Azure-faktureringskonto
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 776c207387ee55b998615131baf77e6c13655b49
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033946"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Ändra kontaktuppgifter för ett Azure-faktureringskonto

Den här artikeln innehåller information om att uppdatera kontaktuppgifterna för ett *faktureringskonto* i Azure-portalen. Anvisningarna för att uppdatera kontaktinformationen varierar beroende på typen av faktureringskonto. Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](view-all-accounts.md). Ett Azure-faktureringskonto är skilt från ditt Azure-användarkonto och [Microsoft-konto](https://account.microsoft.com/).

Om du vill uppdatera din Azure Active Directory-användarprofilinformation kan endast en användaradministratör göra dessa ändringar. Kontakta användaradministratören om du inte har tilldelats rollen som användaradministratör. Mer information om att ändra en användares profil finns i [Lägga till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Såld till-adress* – Såld till-adressen är adressen och kontaktinformationen för organisationen eller personen som är ansvarig för ett faktureringskonto. Den visas i alla fakturor som genereras för faktureringskontot.

*Fakturera till-adress* – Såld till-adressen är adressen och kontaktinformationen för organisationen eller personen som är ansvarig för fakturorna som genereras för ett faktureringskonto. För ett faktureringskonto för ett Microsoft Online-prenumerationsprogram (MOSP) finns det en faktureringsadress som visas på alla fakturor som genereras för kontot. För ett faktureringskonto för ett Microsoft-kundavtal (MCA) finns det en faktureringsadress för varje faktureringsprofil som visas i fakturan som genereras för faktureringsprofilen.

*Kontakta e-postadressen för e-post för tjänster och marknadsföring* – Du kan ange en e-postadress som skiljer sig från den e-postadress som du loggar in med för att få viktiga meddelanden om fakturering, tjänster och rekommendationer om ditt Azure-konto. Tjänstmeddelanden som via e-post, till exempel angående brådskande säkerhetsproblem, prisändringar eller större ändringar av tjänster som används av ditt konto, skickas alltid till din inloggningsadress.

## <a name="update-an-mosp-billing-account-address"></a>Uppdatera en MOSP-faktureringskontoadress

1. Logga in på Azure-portalen med hjälp av e-postadressen, som har kontoadministratörsbehörighet för kontot.
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar var du söker i Azure-portalen efter kostnadshantering + fakturering](./media/change-azure-account-profile/search-cmb.png)
1. Välj **Egenskaper** till vänster.  
    ![Skärmbild som visar egenskaper för MOSP-faktureringskonto](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Välj **Uppdatera faktureringsadressen** för att uppdatera adresserna för såld till och fakturera till. Ange den nya adressen och välj sedan **Spara**.  
    ![Skärmbild som visar uppdateringsadressen för MOSP-faktureringskontot](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Uppdatera en såld till-adress för ett MCA-faktureringskonto

1. Logga in på Azure-portalen med hjälp av e-postadressen, som har en ägar- eller deltagarroll på faktureringskontot för ett Microsoft-kundavtal.
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar var du söker i Azure-portalen](./media/change-azure-account-profile/search-cmb.png)
1. Välj **Egenskaper** på vänster sida och välj sedan **Uppdatering såld till**.  
    ![Skärmbild som visar egenskaperna för ett MCA-faktureringskonto där du kan ändra Sålt till-adressen](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Ange den nya adressen och välj **Spara**.  
    ![Skärmbild som visar uppdatering av Sålt till-adressen för ett MCA-konto](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Vissa konton kräver ytterligare verifiering innan deras sälj till kan uppdateras. Om ditt konto kräver manuellt godkännande blir du ombedd att kontakta Azure-supporten.

## <a name="update-an-mca-billing-account-address"></a>Uppdatera en MCA-faktureringskontoadress

1. Logga in på Azure-portalen med hjälp av e-postadressen, som har en ägar- eller deltagarroll på ett faktureringskonto eller en faktureringsprofil för en MCA.
1. Sök efter **Kostnadshantering + fakturering**.  
1. Välj **Faktureringsprofiler** till vänster.
1. Välj en faktureringsprofil för att uppdatera faktureringsadressen.  
    ![Skärmbild som visar sidan Faktureringsprofiler, där du väljer en faktureringsprofil](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Välj **Egenskaper** till vänster.
1. Välj **Uppdatera adress**.  
    ![Skärmbild som visar var du uppdaterar adressen](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Ange den nya adressen och välj sedan **Spara**.  
    ![Skärmbild som visar uppdatering av adressen](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>E-postmeddelanden med information om tjänster och marknadsföring

Du uppmanas att verifiera eller uppdatera din e-postadress var 90:e dag på Azure-portalen. Microsoft skickar meddelanden till den här e-postadressen med Azure-kontoinformation för:

- Tjänstmeddelanden
- Säkerhetsaviseringar
- Faktureringsändamål
- Support
- Marknadsföringskommunikation
- Rekommendationer för bästa praxis, baserat på din Azure-användning

Ange den e-postadress dit du vill få meddelanden om ditt konto. När du anger en e-postadress väljer du att ta emot kommunikation från Microsoft.

![Exempel på uppmaning om uppdatering av kontaktuppgifter](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Ändra din kontakt-e-postadress

Du kan ändra din kontakt-e-postadress med hjälp av någon av följande metoder. Om du uppdaterar kontaktens e-postadress uppdateras inte den e-postadress som du loggar in med.

1. Om du är kontoadministratör för ett MOSP-konto följer du anvisningarna i [Uppdatera en MOSP-faktureringskontoadress](#update-an-mosp-billing-account-address) och väljer **Uppdatera kontaktinformationen** i det sista steget. Ange sedan den nya e-postadressen.
1. Gå till avsnittet [Kontaktinformation](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) på Azure-portalen och ange den nya e-postadressen. 
1. På Azure-portalen väljer du ikonen med dina initialer eller din bild. Välj sedan snabbmenyn ( **...** ). Välj **Mina kontaktuppgifter** på menyn och ange den nya e-postadressen.

![Exempel på uppdatering av en e-postadress i Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Avanmäla sig för e-postmeddelanden med marknadsföringsinnehåll

Så här avanmäler du dig för e-postmeddelanden med marknadsföringsinnehåll:

1. Gå till [formuläret för begäran](https://account.microsoft.com/profile/permissions-link-request) för att skicka en begäran med e-postadressen för din profil. Du får en länk via e-post för att uppdatera dina inställningar.
1. Välj länken för att öppna sidan **Hantera kommunikationsbehörigheter**. På den här sidan visas de typer av marknadsföringskommunikation som har valts för e-postadressen. Avmarkera de avsnitt som du vill avanmäla dig från och välj sedan **Spara**.  
    ![Exempel på sidan för hantering av kommunikationsbehörigheter](./media/change-azure-account-profile/manage-communication-permissions.png)

När du avanmäler dig för marknadsföringskommunikationen får du fortfarande tjänstmeddelanden, baserat på ditt konto.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Uppdatera e-postadressen som du loggar in med

Du kan inte uppdatera e-postadressen som du använder för att komma åt ditt konto. Men om du har ett faktureringskonto för ett MOSP kan du registrera dig för ett annat konto med den nya e-postadressen och överföra ägarskapet för dina prenumerationer till nästa konto. För ett MCA-faktureringskonto kan du ge den nya e-postadressen behörighet till ditt konto.

## <a name="update-your-credit-card"></a>Uppdatera ditt kreditkort

Information om hur du uppdaterar ditt kreditkort finns i [Change the credit card used to pay for an Azure subscription](change-credit-card.md) (Ändra det kreditkort som används för att betala för en Azure-prenumeration).

## <a name="update-your-country-or-region"></a>Uppdatera ditt land eller din region

Det finns inte stöd för att ändra land eller region för ett befintligt konto. Du kan dock skapa ett nytt konto i ett annat land eller en annan region och sedan kontakta Azure-supporten för att överföra din prenumeration till det nya kontot.

## <a name="change-the-subscription-name"></a>Ändra prenumerationsnamnet

1. Logga in på Azure-portalen, välj **Prenumeration** i den vänstra rutan och välj sedan den prenumeration som du vill byta namn på.
1. Välj **Översikt** och välj sedan **Byt namn** i kommandofältet.  
    ![Exempel på namnbyte av Azure-prenumeration](./media/change-azure-account-profile/rename-sub.png)
1. När du har ändrat namnet väljer du **Spara**.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa dina faktureringskonton](view-all-accounts.md)
