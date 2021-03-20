---
title: Säkerhetskopiera och återställa konton med Microsoft Authenticator app – Azure AD
description: Lär dig hur du säkerhetskopierar och återställer autentiseringsuppgifter för säkerhetskopierade konton med hjälp av Microsoft Authenticator-appen.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530909"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Säkerhetskopiera och återställa autentiseringsuppgifter för kontot med hjälp av Microsoft Authenticator-appen

**Gäller för:**

- iOS-enheter, köra version 5.7.0 och senare

- Android-enheter, som kör version 6.6.0 och senare

Microsoft Authenticator-appen säkerhetskopierar dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, till molnet. När du har säkerhetskopierat kan du också använda appen för att återställa din information på en ny enhet, vilket kan undvika att bli utelåst eller behöver återskapa konton.

Varje lagrings plats för säkerhets kopior kräver att du har en personlig Microsoft-konto, medan iOS även kräver att du har ett iCloud-konto. Du kan ha flera konton lagrade på samma plats. Du kan till exempel ha ett personligt konto, ett arbets-eller skol konto och ett personligt, icke-Microsoft-konto gilla för Facebook, Google och så vidare.

> [!IMPORTANT]
> Endast dina personliga och tredje parts konto uppgifter lagras, som innehåller ditt användar namn och konto verifierings koden som krävs för att bevisa din identitet. Vi lagrar inte övrig information som är kopplad till dina konton, inklusive e-post eller filer. Vi associerar inte heller eller delar dina konton på något sätt eller med någon annan produkt eller tjänst. Slutligen får IT-administratören ingen information om något av dessa konton.

## <a name="back-up-your-account-credentials"></a>Säkerhetskopiera dina kontoautentiseringsuppgifter

Innan du kan säkerhetskopiera dina autentiseringsuppgifter måste du ha:

- En personlig [Microsoft-konto](https://account.microsoft.com/account) som fungerar som ditt återställnings konto.

- **För iOS** måste du ha ett iCloud- [konto](https://www.icloud.com/) för den faktiska lagrings platsen.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Aktivera moln säkerhets kopiering för iOS-enheter

- På din iOS-enhet väljer du **Inställningar**, **säkerhets kopiering** och aktiverar sedan **iCloud-säkerhetskopiering**.

    Autentiseringsuppgifterna för ditt konto säkerhets kopie ras till ditt iCloud-konto.

    ![skärmen iOS-inställningar, som visar platsen för säkerhets kopierings inställningarna i iCloud](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Aktivera moln säkerhets kopiering för Android-enheter

- På din Android-enhet väljer du **Inställningar**, **säkerhets kopiering** och aktiverar sedan **moln säkerhets kopiering**.

    Autentiseringsuppgifterna för ditt konto säkerhets kopie ras till ditt moln konto.

    ![Skärmen Android-inställningar, som visar platsen för inställningarna för säkerhets kopiering](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Återställa autentiseringsuppgifterna för ditt konto på din nya enhet

Du kan återställa dina kontoautentiseringsuppgifter från ditt moln konto, men du måste först se till att det konto som du återställer inte finns i Microsoft Authenticator-appen. Om du till exempel återställer din personliga Microsoft-konto måste du se till att du inte har någon personlig Microsoft-konto som redan har kon figurer ATS i Authenticator-appen. Den här kontrollen är viktig så att vi kan se till att vi inte skriver över eller raderar ett befintligt konto av misstag.

### <a name="to-recover-your-information"></a>Så här återställer du din information

1. Öppna appen Microsoft Authenticator på din mobila enhet och välj **Starta återställning** längst ned på skärmen.

    ![Microsoft Authenticator app, som visar var du klickar på Starta återställning](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Logga in på ditt återställnings konto med samma personliga Microsoft-konto som du använde under säkerhets kopieringen.

    Dina kontoautentiseringsuppgifter återställs till den nya enheten.

När du har slutfört återställningen kan du märka att dina personliga Microsoft-konto verifierings koder i Microsoft Authenticator-appen skiljer sig mellan dina gamla och nya telefoner. Koderna är olika eftersom varje enhet har sina egna unika autentiseringsuppgifter, men båda är giltiga och fungerar samtidigt som du loggar in med den associerade telefonen.

## <a name="recover-accounts-requiring-more-verification"></a>Återställa konton som kräver mer verifiering

Om du använder push-meddelanden med dina personliga eller arbets-eller skol konton får du en avisering på skärmen som säger att du måste ange ytterligare verifiering innan du kan återställa din information. Eftersom push-meddelanden kräver användning av autentiseringsuppgifter som är knutna till din enhet och aldrig skickas via nätverket, måste du bevisa din identitet innan autentiseringsuppgiften skapas på enheten.

För personliga Microsoft-konton kan du bevisa din identitet genom att ange ditt lösen ord tillsammans med ett alternativt e-postadress eller telefonnummer. För arbets-eller skol konton måste du skanna en QR-kod som du fått av din konto leverantör.

### <a name="to-provide-more-verification-for-personal-accounts"></a>För att ge mer verifiering för personliga konton

1. På skärmen **konton** i Microsoft Authenticator-appen trycker du på det konto som du vill återställa för att öppna hela skärm visningen av kontot.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Skärm bild som visar Microsoft Authenticator-appen med de tillgängliga konto panelerna." border="true":::

1. Tryck på panelen för det konto som du återställer och tryck sedan på alternativet för att logga in för att återställa. Ange ditt lösen ord och bekräfta din e-postadress eller ditt telefonnummer som ytterligare verifiering.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Skärm bild som visar Microsoft Authenticator lösen ords kod med eng ång slö sen ord." border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>För att ge mer verifiering för arbets-eller skol konton

1. På skärmen **konton** i Microsoft Authenticator-appen trycker du på det konto som du vill återställa för att öppna hela skärm visningen av kontot.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Microsoft Authenticator app, som visar panelerna tillgängliga konton" border="true":::

1. I hel skärms läge trycker du på alternativet för att skanna en QR-kod för att helt återställa.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Autentiseraren visar ett eng ång slö sen ord som en verifierings kod" border="true":::

>[!NOTE]
>Mer information om QR-koder och hur du hämtar ett finns i [Kom igång med Microsoft Authenticator-appen](./user-help-auth-app-download-install.md) eller [Konfigurera säkerhets information för att använda en Authenticator-app](./security-info-setup-auth-app.md), baserat på om din administratör har aktiverat säkerhets information.
>
>Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Felsök problem med säkerhets kopiering och återställning

Det finns några skäl till varför säkerhets kopieringen kanske inte är tillgänglig

- **Ändra operativ system**: säkerhets kopian lagras i iCloud för iOS och i Microsofts moln lagrings leverantör för Android. Det innebär att säkerhets kopian inte är tillgänglig om du växlar mellan Android-och iOS-enheter. Om du gör växeln måste du manuellt återskapa dina konton i Microsoft Authenticator-appen.

- **Nätverks problem**: om du har problem med nätverks problem kontrollerar du att du är ansluten till nätverket och korrekt inloggad på ditt konto.

- **Konto problem**: om du har problem med kontona kontrollerar du att du är korrekt inloggad på ditt konto. För iOS innebär detta att du måste vara inloggad i iCloud med samma AppleID-konto som din iPhone.

- **Oavsiktlig borttagning**: det är möjligt att du har tagit bort ditt säkerhets kopierings konto från din tidigare enhet eller när du hanterat ditt moln lagrings konto. I så fall måste du manuellt återskapa ditt konto i appen.

- **Befintliga Microsoft Authenticator-konton**: om du redan har konfigurerat konton i Microsoft Authenticator-appen kommer appen inte att kunna återställa dina säkerhetskopierade konton. Förhindra återställning hjälper till att se till att konto informationen inte skrivs över med den inaktuella informationen. I så fall måste du ta bort eventuell befintlig konto information från de befintliga konton som kon figurer ATS i din Authenticator-app innan du kan återställa säkerhets kopian.

- **Säkerhets kopieringen är inaktuell**: om säkerhets kopierings informationen är inaktuell kan du uppmanas att uppdatera informationen genom att logga in på ditt Microsoft Recovery-konto igen. Ditt återställnings konto är det personliga Microsoft-konto som du ursprungligen använde för att lagra säkerhets kopian. Om det krävs en inloggning visas en röd prick på menyn eller åtgärds fältet, eller så visas ett utrops tecken där du uppmanas att logga in för att slutföra återställningen från säkerhets kopieringen. När du har valt lämplig ikon uppmanas du att logga in igen för att uppdatera din information.

## <a name="next-steps"></a>Nästa steg

Nu när du har säkerhetskopierat och återställt dina konto uppgifter till din nya enhet kan du fortsätta att använda appen Microsoft Authenticator för att verifiera din identitet. Mer information finns i [Logga in på dina konton med hjälp av Microsoft Authenticator-appen](user-help-sign-in.md).

## <a name="related-articles"></a>Relaterade artiklar

- [Vad är Microsoft Authenticator-appen?](user-help-auth-app-overview.md)

- [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md)

- [Multi-factor Authentication](/azure/multi-factor-authentication/)