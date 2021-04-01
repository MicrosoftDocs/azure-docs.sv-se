---
title: Konfigurera en e-postadress som verifierings metod – Azure AD
description: Hur du konfigurerar din säkerhets information (för hands version) för att verifiera din identitet med hjälp av en e-postadress som verifierings metod.
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
ms.openlocfilehash: 4f0ea55149fc8cfeeb6ef719d67346019b81078a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744474"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>Konfigurera en e-postadress som verifierings metod

Du kan följa de här stegen för att lägga till din metod för lösen ords återställning. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser något e-postalternativ är det möjligt att din organisation inte tillåter att du använder det här alternativet för din metod för lösen ords återställning. I så fall måste du välja en annan metod eller kontakta din organisations supportavdelning om du behöver mer hjälp.

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Konfigurera din e-postadress på sidan säkerhets information

Beroende på din organisations inställningar kanske du kan använda din e-postadress som en av dina säkerhets metoder.

>[!Note]
>Vi rekommenderar att du använder en e-postadress som inte kräver nätverks lösen ordet för att komma åt. Om du inte ser e-postalternativet är det möjligt att din organisation inte tillåter att du använder ett e-postmeddelande för verifiering. Om så är fallet måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.

### <a name="to-set-up-your-email-address"></a>Konfigurera din e-postadress

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **e-post** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod, med e-post valt](media/security-info/securityinfo-myprofile-addemail.png)

4. På sidan **e-post** anger du din e-postadress (till exempel alain@gmail.com ) och väljer sedan **Nästa**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >E-postadressen får inte vara din e-postadress till arbetet eller skolan.

5. Skriv koden som skickas till din angivna e-postadress och välj sedan **Nästa**.

    ![Lägg till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-emailcode.png)

    Din säkerhets information uppdateras och du kan använda din e-postadress för att verifiera din identitet när du använder lösen ords återställning.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Ta bort din e-postadress från säkerhets informationens metoder

Om du inte längre vill använda din e-postadress som metod för säkerhets information kan du ta bort den från sidan **säkerhets information** .

>[!Important]
>Om du tar bort din e-postadress av misstag finns det inget sätt att ångra. Du måste lägga till metoden igen enligt stegen i avsnittet [Konfigurera din e-postadress](#set-up-your-email-address-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-email-address"></a>Ta bort din e-postadress

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid alternativet **e-post** .

    ![Länk för att ta bort telefon metoden från säkerhets information](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Välj **Ja** i bekräftelse rutan om du vill ta bort **e-** postkontot. När e-postkontot har tagits bort tas det bort från din säkerhets information och försvinner från sidan **säkerhets information** .

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **Säkerhets frågor.** Svara på vissa säkerhets frågor som har skapats av administratören för din organisation. Det här alternativet är bara tillgängligt för lösen ords återställning och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar dina säkerhets frågor finns i artikeln [Konfigurera säkerhets information för att använda säkerhets frågor](security-info-setup-questions.md) .

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
