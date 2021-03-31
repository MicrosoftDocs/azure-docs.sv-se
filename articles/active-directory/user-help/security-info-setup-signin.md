---
title: Konfigurera din säkerhets information från en inloggnings tolk – Azure AD
description: Hur du konfigurerar din säkerhets information för ditt arbets-eller skol konto, när du har tillfrågats från din organisations inloggnings sida.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/07/2020
ms.author: curtand
ms.openlocfilehash: 016e0de26bf3a9513940be161cfecf6d1c2b9d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798507"
---
# <a name="set-up-your-security-info-from-a-sign-in-prompt"></a>Konfigurera din säkerhets information från en inloggnings tolk

Du kan följa de här stegen om du uppmanas att konfigurera säkerhetsinformation omedelbart efter att du har loggat in på ditt arbets- eller skolkonto.

Du ser bara den här frågan om du inte har konfigurerat den säkerhets information som krävs av din organisation. Om du tidigare har konfigurerat din säkerhetsinformation men vill göra ändringar kan du följa stegen i de olika metodbaserade instruktionsartiklarna. Mer information finns i [översikten om att lägga till eller uppdatera säkerhetsinformation](./security-info-setup-auth-app.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="security-verification-versus-password-reset-authentication"></a>Säkerhets verifiering jämfört med autentisering av lösen ords återställning

Metoder för säkerhets information används för både dubbelriktad säkerhets verifiering och för lösen ords återställning. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhets nyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="sign-in-to-your-work-or-school-account"></a>Logga in på ditt arbets- eller skolkonto

När du har loggat in på ditt arbets- eller skolkonto konto visas en uppmaning om att du behöver ange mer information innan du tillåts komma åt ditt konto.

![Uppmaning som ber om mer information](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Konfigurera säkerhetsinformation med hjälp av guiden

Följ de här stegen för att konfigurera säkerhetsinformation för ditt arbets-eller skolkonto från uppmaningen.

>[!Important]
>Det här är bara ett exempel på processen. Beroende på organisationens krav kan administratören ha konfigurerat olika verifieringsmetoder som du behöver konfigurera under den här processen. I det här exemplet kräver vi två metoder, Microsoft Authenticator-appen och ett mobiltelefonnummer för verifieringssamtal eller SMS.

1. När du har valt **Nästa** från uppmaningen öppnas **guiden om att hålla ditt konto säkert**, som visar den första metod som din administratör eller organisation kräver att du konfigurerar. I det här exemplet är det Microsoft Authenticator-appen.

   > [!Note]
   > Om du vill använda en annan autentiseringsapp än Microsoft Authenticator-appen väljer du länken **Jag vill använda en annan autentiseringsapp**.
   >
   > Om din organisation låter dig välja en annan metod än autentiseringsappen kan du välja länken **Jag vill konfigurera en annan metod**.

    ![Guiden för att hålla ditt konto säkert, som visar sidan för nedladdning av autentiseringsappen](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Välj **Ladda ned nu** för att ladda ned och installera Microsoft Authenticator-appen på din mobila enhet, och välj sedan **Nästa**. Mer information om hur du laddar ned och installerar appen finns i [Ladda ned och installera Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

    ![Guiden för att hålla ditt konto säkert, som visar autentiserarsidan för att konfigurera kontot](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Ha sidan **Konfigurera ditt konto** öppen medan du konfigurerar Microsoft Authenticator-appen på din mobila enhet.

4. Öppna Microsoft Authenticator-appen, välj att tillåta meddelanden (om du uppmanas), välj **Lägg till konto** från ikonen **Customize and control** (Anpassa och styr) uppe till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

5. Gå tillbaka till sidan **Konfigurera ditt konto** på datorn och välj sedan **Nästa**.

    Sidan **Skanna QR-koden** visas.

    ![Skanna QR-koden med hjälp av Authenticator-appen](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. Skanna den angivna koden med Microsoft Authenticator-appens QR-kodläsare, som infördes på din mobila enhet efter att du skapade arbets- eller skolkontot i steg 5.

    Authenticator-appen bör lägga till ditt arbets- eller skolkonto utan att någon ytterligare information krävs från dig. Men om QR-koden inte kan läsa koden kan du välja att **inte skanna QR-bilden** och manuellt ange koden och webb adressen i Microsoft Authenticator-appen. Mer information om att lägga till en kod manuellt finns i [Lägga till ett konto i appen manuellt](user-help-auth-app-add-account-manual.md).

7. Välj **Nästa** på sidan **Skanna QR-koden** på datorn.

    Ett meddelande skickas till Microsoft Authenticator-appen på din mobila enhet för att testa ditt konto.

    ![Testa ditt konto med Authenticator-appen](media/security-info/securityinfo-prompt-test-app.png)

8. Godkänn meddelandet i Microsoft Authenticator-appen och välj sedan **Nästa**.

    ![Meddelande om att det lyckades; appen och ditt konto ansluts](media/security-info/securityinfo-prompt-auth-app-success.png)

    Din säkerhetsinformation uppdateras till att använda Microsoft Authenticator-appen som standard för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

9. På sidan för **telefonkonfiguration** väljer du om du vill få ett SMS eller ett telefonsamtal och väljer sedan **Nästa**. För det här exemplet använder vi SMS, så du måste använda ett telefonnummer för en enhet som kan ta emot SMS.

    ![Börja konfigurera telefonnumret för SMS](media/security-info/securityinfo-prompt-text-msg.png)

    Ett SMS skickas till ditt telefonnummer. Om föredrar att få ett telefonsamtal är processen densamma. Men då får du ett telefonsamtal med instruktioner i stället för ett SMS.

10. Ange den kod som visas i det SMS som skickades till din mobila enhet och välj sedan **Nästa**.

    ![Testa ditt konto med SMS:et](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Granska meddelandet om att det lyckades och välj sedan **Klar**.

    ![Meddelande om att det lyckades](media/security-info/securityinfo-prompt-call-answered-success.png)

    Din säkerhetsinformation uppdateras till att använda SMS som reservmetod för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

12. Granska sidan **Success** (Lyckades) för att kontrollera att du har konfigurerat både Microsoft Authenticator-appen och en telefonmetod (SMS eller telefonsamtal) för din säkerhetsinformation, och välj sedan **Klar**.

    ![Sidan om att guiden har slutförts](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Om din organisation kräver att du använder applösenord, kan du se ytterligare ett avsnitt i den här guiden, där du kan konfigurera den. Om du ser ett tredje avsnitt, som kallas **applösenord**, måste du fylla i det innan du kan slutföra guiden. Anvisningar om hur du lägger till ett applösenord finns i avsnittet [Hantera applösenord](#manage-your-app-passwords) i den här artikeln.

### <a name="manage-your-app-passwords"></a>Hantera dina applösenord

Vissa appar, till exempel Outlook 2010, stöder inte tvåstegsverifiering. Detta saknar stöd innebär att om du använder tvåstegsverifiering i din organisation fungerar inte appen. För att komma runt det här problemet kan du skapa ett automatiskt genererat lösen ord som ska användas med varje icke-webbapp, separat från det normala lösen ordet.

>[!Note]
>Om du inte ser det här alternativet i guiden innebär det att administratören inte har konfigurerat det. Om detta inte är konfigurerat, men du vet att du behöver använda applösenord, kan du följa stegen i [Konfigurera applösenord på sidan säkerhets information](security-info-app-passwords.md).

När du använder applösenord är det viktigt att komma ihåg:

- Applösenord skapas automatiskt och anges bara en gång per app.

- Det finns en gräns på 40 lösen ord per användare. Om du försöker skapa en efter den gränsen uppmanas du att ta bort ett befintligt lösen ord innan du får skapa det nya.

- Använd ett applösenord per enhet, inte per app. Du kan till exempel skapa ett enda lösen ord för alla appar på din bärbara dator och sedan ett nytt lösen ord för alla appar på Skriv bordet.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Så här lägger du till applösenord i inloggnings guiden

1. När du har slutfört de föregående avsnitten i guiden väljer du **Nästa** och slutför **appens lösen ord** .

2. Skriv namnet på den app som behöver lösen ordet, till exempel `Outlook 2010` , och välj sedan **Nästa**.

    ![Lägg till appens lösen ords namn i guiden](media/security-info/app-password-app-password.png)

3. Kopiera lösen ords koden från skärmen **applösenord** och klistra in den i appens **lösen ord** (i det här exemplet Outlook 2010).

    ![Sidan applösenord med lösen ord för kopiering](media/security-info/app-password-copy-password.png)

4. När du har kopierat lösen ordet och klistrat in det i appen går du tillbaka till den här guiden för att se till att all information om inloggnings metoden är korrekt och väljer sedan **slutförd**.

    ![Sidan applösenord, med kompletterings meddelande](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill ändra, ta bort, eller uppdatera standardmetoder för säkerhetsinformation kan du läsa:

    - [Konfigurera säkerhets information för en Authenticator-app](security-info-setup-auth-app.md).

    - [Konfigurera säkerhetsinformation för SMS](security-info-setup-text-msg.md).

    - [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

    - [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

    - [Konfigurera säkerhets information för att använda fördefinierade säkerhets frågor](security-info-setup-questions.md).

- Information om hur du loggar in med din angivna metod finns i [Så loggar du in](user-help-sign-in.md).

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).