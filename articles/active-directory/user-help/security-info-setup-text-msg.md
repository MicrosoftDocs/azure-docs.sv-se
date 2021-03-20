---
title: Konfigurera SMS-meddelanden som verifierings metod – Azure AD
description: Hur du konfigurerar din säkerhets information (förhands gransknings sida) för att verifiera din identitet med hjälp av SMS som din verifierings metod.
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
ms.openlocfilehash: c68e01e0eb7c926f47c99b16efa87d23a10b6711
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537041"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>Konfigurera SMS-meddelanden som din verifierings metod

Du kan följa de här stegen för att lägga till dina metoder för verifiering och återställning av lösen ord. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

Om du uppmanas att ställa in det här direkt efter att du har loggat in på ditt arbets-eller skol konto, se anvisningarna i artikeln [Konfigurera din säkerhets information från inloggnings sidan](security-info-setup-signin.md) .

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser något telefon alternativ är det möjligt att din organisation inte tillåter att du använder det här alternativet för verifiering. I så fall måste du välja en annan metod eller kontakta din organisations supportavdelning om du behöver mer hjälp.

## <a name="set-up-text-messages-from-the-security-info-page"></a>Konfigurera textmeddelanden från sidan säkerhets information

Beroende på din organisations inställningar kanske du kan använda SMS som en av dina säkerhets metoder. Alternativet textmeddelande är en del av alternativet telefon, så du kan ställa in allt på samma sätt som du skulle för ditt telefonnummer, men i stället för att låta Microsoft ringa dig väljer du att använda ett textmeddelande.

>[!Note]
>Om du vill ta emot ett telefonsamtal i stället för ett SMS följer du stegen i artikeln [Konfigurera säkerhets information för att använda telefonsamtal](security-info-setup-phone-number.md) .

### <a name="to-set-up-text-messages"></a>Konfigurera textmeddelanden

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **telefon** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod, med valt telefonnummer](media/security-info/securityinfo-myprofile-addphonetext.png)

4. På sidan **telefon** anger du telefonnumret för din mobila enhet, väljer **text mig en kod** och väljer sedan **Nästa**.

    ![Skärm bild som visar sidan "telefon", med "text mig a code" vald.](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. Skriv in koden som skickas till dig via SMS till din mobila enhet och välj sedan **Nästa**.

    ![Lägg till telefonnummer och välj textmeddelanden](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    Sidan ändras för att visa din framgång.

    ![Lyckad avisering, ansluta telefonnumret, välja att ta emot textmeddelanden och ditt konto](media/security-info/securityinfo-myprofile-phonetext-success.png)

    Din säkerhets information uppdateras och du kan använda SMS-meddelanden för att verifiera din identitet när du använder tvåstegsverifiering eller lösen ords återställning. Om du vill göra SMS-meddelanden till standard metoden, se avsnittet [ändra din standard säkerhets information](#change-your-default-security-info-method) i den här artikeln.

## <a name="delete-text-messaging-from-your-security-info-methods"></a>Ta bort SMS-meddelanden från dina säkerhets metoder

Om du inte längre vill använda SMS som en metod för säkerhets information kan du ta bort det från sidan **säkerhets information** .

>[!Important]
>Om du tar bort textmeddelanden av misstag finns det inget sätt att ångra det. Du måste lägga till metoden igen enligt stegen i avsnittet [Konfigurera SMS](#set-up-text-messages-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-text-messaging"></a>Ta bort SMS-meddelanden

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid alternativet **telefon** .

    ![Länk för att ta bort telefon-och text meddelande metoden från säkerhets information](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. Klicka på **Ja** i bekräftelse rutan för att ta bort **telefonnumret** . När ditt telefonnummer har tagits bort tas det bort från din säkerhets information och försvinner från sidan **säkerhets information** . Om **telefonen** är standard-metoden ändras standard till en annan tillgänglig metod.

## <a name="change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

Om du vill att SMS ska vara standard metoden som används när du loggar in på ditt arbets-eller skol konto med hjälp av tvåstegsverifiering eller för förfrågningar om lösen ords återställning, kan du ställa in det på sidan **säkerhets information** .

### <a name="to-change-your-default-security-info-method"></a>Ändra din standard metod för säkerhets information

1. På sidan **säkerhets information** väljer du länken **ändra** bredvid standard information om **inloggnings metoden** .

    ![Ändra länk för standard inloggnings metod](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. Välj **telefon-text (*_your_phone_number_*)** i list rutan med tillgängliga metoder och välj sedan **Bekräfta**.

    ![Välj metod för standard inloggning](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    Standard metoden som används för inloggnings ändringar till **telefon-text (*_your_phone_number_*)**.

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

- **Säkerhets frågor.** Svara på vissa säkerhets frågor som har skapats av administratören för din organisation. Det här alternativet är bara tillgängligt för lösen ords återställning och inte för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar dina säkerhets frågor finns i artikeln [Konfigurera säkerhets information för att använda säkerhets frågor](security-info-setup-questions.md) .

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
