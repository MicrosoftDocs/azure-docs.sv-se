---
title: Felsöka problem när du registrerar ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter
description: Lösa ett problem vid försök att registrera ett nytt konto i Microsoft Azure-portalen eller kontocentret.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: v-miegge
ms.openlocfilehash: 015e6058236190a04df152573621f0e57030d23b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054169"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Felsöka problem när du registrerar ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter

Ett problem kan uppstå när du försöker registrera ett nytt konto i Microsoft Azure-portalen eller Azure-kontocenter. Den här korta guiden vägleder dig genom registreringsprocessen och diskuterar några vanliga problem i varje steg.

> [!NOTE]
> Om du redan har ett befintligt konto och behöver hjälp med att felsöka inloggningsproblem kan du läsa [Felsöka problem med inloggning för Azure-prenumeration](./troubleshoot-sign-in-issue.md).

## <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar registreringen bör du först kontrollera följande:

- De uppgifter du angav för din Azure-kontoprofil (däribland e-postadress, gatuadress och telefonnummer för kontakt) är korrekta.
- Kreditkortsuppgifterna stämmer.
- Du har inte redan ett Microsoft-konto där du använder samma uppgifter.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Guidad genomgång av Azure-registrering

Azure-registrering består av fyra delar:

- Om dig
- Identitetsverifiering via telefon
- Identitetsverifiering via kort
- Avtal

Den här genomgången visar exempel på korrekt information när du registrerar dig för ett Azure-konto. Varje avsnitt innehåller också information om vanliga problem och hur du kan lösa dem.

## <a name="about-you"></a>Om dig

![Om dig](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Kända problem och lösningar

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Meddelandet ”Det går inte att fortsätta med registreringen på grund av ett problem med ditt konto. Kontakta faktureringssupporten.” visas.

Följ dessa anvisningar för att lösa problemet:

1.  Logga in på [Azure-kontocenter](https://account.azure.com/Profile) med hjälp av kontoadministratörens autentiseringsuppgifter.
1.  Välj **Redigera information**.
1.  Kontrollera att alla adressfält är ifyllda och giltiga.
1.  När du registrerar dig för Azure-prenumerationen är det viktigt att du kontrollerar att faktureringsadressen för registreringen av ditt kreditkort stämmer överens med dina bankuppgifter.

Om du fortsätter att få meddelandet kan du försöka att registrera dig via en annan webbläsare.

Hur går det med InPrivate-surfning?

#### <a name="free-trial-is-not-available"></a>Kostnadsfri utvärderingsversion är inte tillgänglig

Har du använt en Azure-prenumeration tidigare? Azures användningsvillkor begränsar aktivering av kostnadsfri utvärderingsversion endast för en användare som är nybörjare på Azure. Om du har haft någon annan typ av Azure-prenumeration kan du inte aktivera en kostnadsfri utvärderingsversion. Överväg att registrera dig för en [Betala per användning-prenumeration](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>Meddelandet ”Du är inte berättigad till en Azure-prenumeration” visas

Lös det här problemet genom att kontrollera om följande saker stämmer:

- De uppgifter du angav för din Azure-kontoprofil (däribland e-postadress, gatuadress och telefonnummer för kontakt) är korrekta.
- Kreditkortsuppgifterna stämmer.
- Du har inte redan ett Microsoft-konto där samma uppgifter används.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>Meddelandet ”Din aktuella kontotyp stöds inte” visas

Det här problemet kan uppstå om kontot är registrerat i en [ohanterad Azure AD-katalog](../../active-directory/enterprise-users/directory-self-service-signup.md) och inte finns i din organisations Azure AD-katalog.
Lös det här problemet genom att registrera Azure-kontot med hjälp av ett annat konto eller ta över den ohanterade AD-katalogen. Mer information finns i [Ta över en ohanterad katalog som administratör i Azure Active Directory](../../active-directory/enterprise-users/domains-admin-takeover.md).

## <a name="identity-verification-by-phone"></a>Identitetsverifiering via telefon

![Identitetsverifiering via telefon](./media/troubleshoot-azure-sign-up/2.png)
 
När du får SMS:et eller telefonsamtalet anger du den kod som du får i textrutan.

### <a name="common-issues-and-solutions"></a>Kända problem och lösningar

#### <a name="no-verification-text-message-or-phone-call"></a>Inget SMS eller telefonsamtal för verifiering

Även om verifieringsprocessen normalt är snabb kan det ta upp till fyra minuter innan du får en verifieringskod.

Här följer några ytterligare tips:

- Du kan använda valfritt telefonnummer för verifieringen, bara det uppfyller kraven. Det telefonnummer som du anger för verifiering lagras inte som ett kontaktnummer för kontot.
  - Det går inte att använda ett VoiP-telefonnummer för telefonverifieringsprocessen.
  - Kontrollera att din telefon kan ta emot samtal eller SMS från ett USA-baserat telefonnummer.
- Dubbelkontrollera det telefonnummer som du anger, inklusive den landskod som du väljer i den nedrullningsbara menyn.
- Om din telefon inte får textmeddelanden (SMS) kan du prova alternativet **Ring mig**.

## <a name="identity-verification-by-card"></a>Identitetsverifiering via kort

![Identitetsverifiering via kort](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Kända problem och lösningar

#### <a name="credit-card-declined-or-not-accepted"></a>Kreditkort nekas eller accepterats inte

Virtuella eller förbetalda kredit- eller debetkort accepteras inte som betalningsmedel för Azure-prenumerationer. Om du vill se fler potentiella orsaker till att ditt kort nekas kan du läsa [Felsöka ett nekat kort vid Azure-registreringen](./troubleshoot-declined-card.md).

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Kreditkortsformuläret stöder inte min faktureringsadress

Din faktureringsadress måste finnas i det land eller den region som du väljer i avsnittet **Om dig**. Kontrollera att du har valt rätt land.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Förloppsindikatorn låser sig i avsnittet Identitetsverifiering via kort

För att du ska kunna slutföra identitetsverifieringen via kort måste cookies från tredje part tillåtas i webbläsaren.

Använd följande steg för att uppdatera webbläsarens inställningar för cookies.

1. Uppdatera cookie-inställningarna.
   - Om du använder **Chrome**:
     - Välj **Inställningar** > **Visa avancerade inställningar** > **Sekretess** > **Innehållsinställningar**. Avmarkera **Blockera cookies från tredje part och platsdata**.

   - Om du använder **Microsoft Edge**:
     - Välj **Inställningar** > **Visa avancerade inställningar** > **Cookies** > **Blockera inte cookies**.

1. Uppdatera sidan för Azure-registrering och kontrollera om problemet är löst.
1. Om uppdateringen inte löste problemet avslutar du och startar om webbläsaren, och försöker igen.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Jag såg en avgift på mitt kostnadsfria utvärderingskonto

Det kan finnas ett litet tillfälligt reservationsbelopp på ditt kreditkortskonto efter att du har registrerat dig. Detta reservationsbelopp tas bort inom tre till fem dagar. Om du har funderingar kring hantering av kostnader kan du läsa mer om att [analysera oväntade avgifter](../understand/analyze-unexpected-charges.md).

## <a name="agreement"></a>Avtal

Slutför avtalet.

## <a name="other-issues"></a>Andra problem

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>Det går inte att aktivera Azures förmåns plan som Visual Studio, BizSpark, BizSparkPlus eller MPN

Kontrollera att du använder rätt inloggningsuppgifter. Kontrollera sedan förmånsprogrammet för att ta reda på om du är berättigad.
- Visual Studio
  - Kontrol lera status för din status på [Visual Studio-kontots sida](https://my.visualstudio.com/Benefits).
  - Kontakta [Visual Studio Subscription support](https://visualstudio.microsoft.com/subscriptions/support/)om du inte kan verifiera din status.
- Microsoft for Startups
  - Logga in på [Microsoft for Startups-portalen](https://startups.microsoft.com/#start-two) för att kontrollera din berättigandestatus för Microsoft for Startups.
  - Om du inte kan verifiera statusen kan du få hjälp på [forumen för Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Logga in på [MPN-portalen](https://mspartner.microsoft.com/Pages/Locale.aspx) för att verifiera din berättigandestatus. Om du har rätt [molnplattformskompetenser](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx) kan du vara berättigad till ytterligare förmåner.
  - Om du inte kan verifiera din status kontaktar du [MPN-supporten](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Det går inte att aktivera ny Azure In Open-prenumeration

För att kunna skapa en Azure In Open-prenumeration måste du ha en giltig OSA-nyckel (Online Service Activation) som har minst en Azure In Open-token associerad. Om du inte har en OSA-nyckel kontaktar du en av de Microsoft-partner som anges i [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](./troubleshoot-declined-card.md)
- [Problem med prenumerationsinloggning](./troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](./no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Läs mer om Azure Cost Management

- [Dokumentation om Azure Cost Management och fakturering](../index.yml)