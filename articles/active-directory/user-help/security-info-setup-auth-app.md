---
title: Konfigurera Microsoft Authenticator-appen som din verifierings metod – Azure AD
description: Hur du konfigurerar din säkerhets information (förhands gransknings sida) för att verifiera din identitet med hjälp av Microsoft Authenticator-appen som din verifierings metod.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c947bee0b702797a86d1e038f74c6c10e2b23eb4
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197481"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>Konfigurera Microsoft Authenticator-appen som din verifierings metod

Du kan följa de här stegen för att lägga till dina metoder för verifiering och återställning av lösen ord. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

Om du uppmanas att ställa in det här direkt efter att du har loggat in på ditt arbets-eller skol konto, se anvisningarna i artikeln [Konfigurera din säkerhets information från inloggnings sidan](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> Om du inte ser alternativet för Authenticator-appen är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I så fall måste du välja en annan metod eller kontakta din organisations supportavdelning om du behöver mer hjälp.

## <a name="security-vs-password-reset-verification"></a>Verifiering av säkerhet och lösen ords återställning

Metoder för säkerhets information används för både dubbelriktad säkerhets verifiering och för lösen ords återställning. Dock kan inte alla metoder användas för båda.

| Metod | Används för |
| ------ | -------- |
| Autentiseringsapp | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| SMS | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Telefonsamtal | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| Säkerhets nyckel | Tvåfaktorsverifiering och autentisering med återställning av lösenord. |
| E-postkonto | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |
| Säkerhetsfrågor | Endast autentisering med återställning av lösenord. Du måste välja en annan metod för tvåfaktorsverifiering. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>Konfigurera Microsoft Authenticator-appen från sidan säkerhets information

Beroende på din organisations inställningar kan du kanske använda en app för autentisering som en av dina metoder för säkerhets information. Du behöver inte använda Microsoft Authenticator-appen, och du kan välja en annan app under installationen. Den här artikeln använder dock Microsoft Authenticator-appen.

> [!IMPORTANT]
> Om du har konfigurerat Microsoft Authenticator-appen på fem olika enheter eller om du har använt fem maskinvaru-token, kan du inte konfigurera en sjätte, och du kan se följande fel meddelande:
> 
> **Du kan inte konfigurera Microsoft Authenticator eftersom du redan har fem Authenticator-appar eller maskinvaru-token. Kontakta administratören om du vill ta bort en av dina Authenticator-appar eller maskinvaru-token.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Konfigurera Microsoft Authenticator-appen

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** på den vänstra menyn eller med hjälp av länken i rutan **säkerhets information** . Om du redan har registrerat dig uppmanas du att utföra tvåstegsverifiering. Välj sedan **Lägg till metod** i rutan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **autentisera program** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod, där autentiserare-app har valts](media/security-info/securityinfo-myprofile-addauthapp.png)

4. På sidan **börja med att hämta appen** väljer du **Hämta nu** för att ladda ned och installera Microsoft Authenticator-appen på din mobila enhet. Välj sedan **Nästa**.

    Mer information om hur du laddar ned och installerar appen finns i [Ladda ned och installera Microsoft Authenticator-appen](user-help-auth-app-download-install.md).

    ![Börja med att hämta appens sida](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Om du vill använda en annan autentiseringsapp än Microsoft Authenticator-appen väljer du länken **Jag vill använda en annan autentiseringsapp**.
   >
   > Om din organisation låter dig välja en annan metod än autentiseringsappen kan du välja länken **Jag vill konfigurera en annan metod**.

5. Ha sidan **Konfigurera ditt konto** öppen medan du konfigurerar Microsoft Authenticator-appen på din mobila enhet.

    ![Konfigurera sidan autentiserare](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Öppna Microsoft Authenticator-appen, välj att tillåta meddelanden (om du uppmanas), välj **Lägg till konto** från ikonen **Customize and control** (Anpassa och styr) uppe till höger och välj sedan **Arbets- eller skolkonto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

7. Gå tillbaka till sidan **Konfigurera ditt konto** på datorn och välj sedan **Nästa**.

    Sidan **Skanna QR-koden** visas.

    ![Skanna QR-koden med hjälp av Authenticator-appen](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. Skanna den angivna koden med Microsoft Authenticator app QR Code Reader, som visas på din mobila enhet efter att du har skapat ditt arbets-eller skol konto i steg 6.

    Authenticator-appen bör lägga till ditt arbets- eller skolkonto utan att någon ytterligare information krävs från dig. Men om QR-kodläsaren inte kan läsa koden kan du välja länken **Jag kan inte skanna QR-koden** och manuellt ange koden och URL:en i Microsoft Authenticator-appen. Mer information om att lägga till en kod manuellt finns i [Lägga till ett konto i appen manuellt](user-help-auth-app-add-account-manual.md).

9. Välj **Nästa** på sidan **Skanna QR-koden** på datorn.

    Ett meddelande skickas till Microsoft Authenticator-appen på din mobila enhet för att testa ditt konto.

    ![Testa ditt konto med Authenticator-appen](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Godkänn meddelandet i Microsoft Authenticator-appen och välj sedan **Nästa**.

     ![Meddelande om att det lyckades; appen och ditt konto ansluts](media/security-info/securityinfo-myprofile-successauthapp.png)

     Din säkerhetsinformation uppdateras till att använda Microsoft Authenticator-appen som standard för att verifiera din identitet vid användning av tvåstegsverifiering eller återställning av lösenord.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>Ta bort din Authenticator-app från säkerhets informationens metoder

Om du inte längre vill använda din Authenticator-app som säkerhets informations metod kan du ta bort den från sidan **säkerhets information** . Detta fungerar för alla autentiserare-appar, inte bara Microsoft Authenticator-appen. När du har tagit bort appen måste du gå till Authenticator-appen på din mobila enhet och ta bort kontot.

>[!Important]
>Om du tar bort Authenticator-appen av misstag finns det inget sätt att ångra den. Du måste lägga till Authenticator-appen igen genom att följa stegen i avsnittet [Konfigurera Authenticator-appen](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-the-authenticator-app"></a>Ta bort Authenticator-appen

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid appen autentiserare.

    ![Länk för att ta bort Authenticator-appen från säkerhets information](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. Välj **Ja** i bekräftelse rutan om du vill ta bort Authenticator-appen. När du har tagit bort Authenticator-appen tas den bort från din säkerhets information och den försvinner från sidan **säkerhets information** . Om Authenticator-appen är din standard metod ändras standardinställningen till en annan tillgänglig metod.

3. Öppna appen autentiserare på din mobila enhet, Välj **Redigera konton** och ta sedan bort ditt arbets-eller skol konto från Authenticator-appen.

    Ditt konto tas bort helt från Authenticator-appen för tvåstegsverifiering och begär Anden om lösen ords återställning.

## <a name="change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

Om du vill att Authenticator-appen ska vara standard metoden som används när du loggar in på ditt arbets-eller skol konto med hjälp av tvåstegsverifiering eller för förfrågningar om lösen ords återställning, kan du ställa in den på sidan säkerhets **information** .

### <a name="to-change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

1. På sidan **säkerhets information** väljer du länken **ändra** bredvid standard information om **inloggnings metoden** .

    ![Ändra länk för standard inloggnings metod](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. Välj **Microsoft Authenticator-meddelande** i list rutan med tillgängliga metoder. Om du inte använder Microsoft Authenticator-appen väljer du alternativet **autentiserare eller maskinvarubaserad token** .

    ![Välj metod för standard inloggning](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. Välj **Bekräfta**.

    Standard metoden som används för inloggnings ändringar i Microsoft Authenticator-appen.

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

- **Säkerhets frågor.** Svara på vissa säkerhets frågor som har skapats av administratören för din organisation. Det här alternativet är bara tillgängligt för lösen ords återställning och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar dina säkerhets frågor finns i artikeln [Konfigurera säkerhets information för att använda säkerhets frågor](security-info-setup-questions.md) .

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Logga in med Microsoft Authenticator-appen genom att följa anvisningarna i artikeln [Logga in med tvåstegsverifiering eller säkerhets information](security-info-setup-signin.md) .

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
