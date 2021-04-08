---
title: Konfigurera säkerhets frågor som verifierings metod – Azure AD
description: Hur du konfigurerar din säkerhets information (förhands gransknings sida) för att verifiera din identitet med fördefinierade säkerhets frågor som din verifierings metod.
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
ms.openlocfilehash: 0c55f15d12ddc51573f59f56c742a8cac525af0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83744437"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Konfigurera säkerhets frågor som verifierings metod

Du kan följa de här stegen för att lägga till din metod för lösen ords återställning. När du har konfigurerat den första gången kan du gå tillbaka till sidan **säkerhets** information för att lägga till, uppdatera eller ta bort din säkerhets information.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>Om du inte ser alternativet säkerhets frågor är det möjligt att din organisation inte tillåter att du använder det här alternativet för din metod för lösen ords återställning. I så fall måste du välja en annan metod eller kontakta din organisations supportavdelning om du behöver mer hjälp.

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

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Konfigurera dina säkerhets frågor från sidan säkerhets information

Beroende på din organisations inställningar kanske du kan välja och svara på några säkerhets frågor som en av dina säkerhets metoder. Administratören ställer in antalet säkerhets frågor som du måste välja och svara på.

Om du använder säkerhets frågor rekommenderar vi att du använder dem tillsammans med en annan metod. Säkerhets frågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på dina frågor.

> [!Note]
> Säkerhets frågor lagras privat och säkert på ett användar objekt i katalogen och kan bara besvaras av dig under registreringen. Det finns inget sätt för administratören att läsa eller ändra dina frågor eller svar.
>
> Om du inte ser alternativet säkerhets frågor är det möjligt att din organisation inte tillåter att du använder säkerhets frågor för verifiering. Om så är fallet måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.
>
> Administratörs konton får inte använda säkerhets frågor som en metod för lösen ords återställning. Om du är inloggad som ett konto på administratörs nivå visas inte dessa alternativ.

### <a name="to-set-up-your-security-questions"></a>Konfigurera dina säkerhets frågor

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

    ![Sidan min profil, som visar markerade länkar för säkerhets information](media/security-info/securityinfo-myprofile-page.png)

2. Välj **säkerhets information** i det vänstra navigerings fönstret eller från länken i **säkerhets informations** blocket och välj sedan **Lägg till metod** på sidan **säkerhets information** .

    ![Sidan säkerhets information med markerat Lägg till metod-alternativ](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. På sidan **Lägg till en metod** väljer du **säkerhets frågor** i list rutan och väljer sedan **Lägg till**.

    ![Rutan Lägg till metod med säkerhets frågor valda](media/security-info/securityinfo-myprofile-addquestions.png)

4. På sidan **säkerhets frågor** väljer du och svarar på dina säkerhets frågor och väljer sedan **Spara**.

    ![Lägg till telefonnummer och välj telefonsamtal](media/security-info/securityinfo-myprofile-securityquestions.png)

    Din säkerhets information uppdateras och du kan använda dina säkerhets frågor för att verifiera din identitet när du använder lösen ords återställning.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Ta bort säkerhets frågor från dina metoder för säkerhets information

Om du inte längre vill använda dina säkerhets frågor som säkerhets information kan du ta bort dem från sidan **säkerhets information** .

>[!Important]
>Om du tar bort dina säkerhets frågor av misstag finns det inget sätt att ångra det. Du måste lägga till metoden igen enligt stegen i avsnittet [Konfigurera dina säkerhets frågor](#set-up-your-security-questions-from-the-security-info-page) i den här artikeln.

### <a name="to-delete-your-security-questions"></a>Ta bort dina säkerhets frågor

1. På sidan **säkerhets information** väljer du länken **ta bort** bredvid alternativet **säkerhets frågor** .

    ![Länk för att ta bort telefon metoden från säkerhets information](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Välj **Ja** i bekräftelse rutan om du vill ta bort dina **säkerhets frågor**. När dina säkerhets frågor har tagits bort tas metoden bort från din säkerhets information och försvinner från sidan **säkerhets information** .

## <a name="additional-security-info-methods"></a>Ytterligare metoder för säkerhets information

Du har ytterligare alternativ för hur din organisation kontaktar dig för att verifiera din identitet, baserat på vad som you're försöker göra. Följande alternativ är tillgängliga:

- **Authenticator-app.** Hämta och Använd en Authenticator-app för att få ett meddelande om godkännande eller en slumpmässigt genererad godkännande kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner om hur du konfigurerar och använder Microsoft Authenticator-appen finns i [Konfigurera säkerhets information så att en autentiserare används](security-info-setup-auth-app.md).

- **Mobil enhets text.** Ange ditt mobila enhets nummer och få en text kod som du kan använda för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett textmeddelande (SMS) finns i [Konfigurera säkerhets information för att använda SMS (text messaging)](security-info-setup-text-msg.md).

- **Mobil enhets-eller arbets telefon samtal.** Ange ditt mobila enhets nummer och få ett telefonsamtal för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med ett telefonnummer finns i [Konfigurera säkerhets information så att telefonsamtal används](security-info-setup-phone-number.md).

- **Säkerhets nyckel.** Registrera din Microsoft-kompatibla säkerhets nyckel och Använd den tillsammans med en PIN-kod för tvåstegsverifiering eller lösen ords återställning. Stegvisa instruktioner för hur du verifierar din identitet med en säkerhets nyckel finns i [Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md).

- **E-postadress.** Ange din e-postadress till arbetet eller skolan för att få ett e-postmeddelande om lösen ords återställning. Det här alternativet är inte tillgängligt för tvåstegsverifiering. Stegvisa instruktioner om hur du konfigurerar din e-post finns i [Konfigurera säkerhets information för att använda e-post](security-info-setup-email.md).

    >[!Note]
    >Om några av dessa alternativ saknas beror det förmodligen på att din organisation inte tillåter dessa metoder. Om så är fallet måste du välja en tillgänglig metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="next-steps"></a>Nästa steg

- Återställ lösenordet om du har tappat eller glömt det från [portalen för lösenordsåterställning](https://passwordreset.microsoftonline.com/) eller följ stegen i artikeln [Återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
