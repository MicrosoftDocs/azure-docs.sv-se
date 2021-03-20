---
title: Registrera autentiseringsinformation för att återställa ditt eget lösen ord – Azure AD
description: Registrera din verifierings metod information för återställning av lösen ord i Azure AD, så att du kan återställa ditt eget lösen ord utan Administratörs hjälp.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799034"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>Registrera din verifierings metod information för att återställa ditt eget lösen ord

Om du har glömt lösen ordet till ditt arbets-eller skol konto, fick du aldrig ett lösen ord från din organisation eller har låsts av ditt konto, kan du använda din säkerhets information och din mobila enhet för att återställa lösen ordet till ditt arbets-eller skol konto.

Administratören måste aktivera den här funktionen så att du kan registrera din information och återställa ditt eget lösen ord. Om du inte ser alternativet **glömt mitt lösen ord** betyder det att administratören inte har aktiverat funktionen för din organisation. Om du tror att det är fel kan du kontakta supportavdelningen om du behöver hjälp.

>[!Important]
>Den här artikeln är avsedd för användare som försöker använda registrera sig för självbetjäning för återställning av lösen ord. Det innebär att du kan återställa ditt eget arbets-eller skol lösen ord (till exempel alain@contoso.com ), utan att behöva administratörens hjälp. Om du är administratör för att få information om hur du aktiverar lösen ords återställning via självbetjäning för dina anställda eller andra användare kan du läsa mer i [Distribuera Azure AD självbetjäning återställning av lösen ord och andra artiklar](../authentication/howto-sspr-deployment.md).

## <a name="set-up-your-password-reset-verification-method"></a>Konfigurera verifierings metoden för lösen ords återställning

1. Öppna webbläsaren på din enhet och gå till [sidan säkerhets information](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Beroende på hur din administratör har konfigurerat din organisation, kommer ett eller flera av följande alternativ att vara tillgängliga så att du kan ställa in den som säkerhets verifierings metod. Om det finns flera tillgängliga alternativ rekommenderar vi starkt att du använder mer än en som säkerhets verifierings metod, om en av dina metoder blir otillgänglig.

    - **App för autentisering.** Välj att använda Microsoft Authenticator-appen eller någon annan Authenticator-app som säkerhets verifierings metod. Mer information om hur du konfigurerar appen finns i [Konfigurera appen Microsoft Authenticator som din verifierings metod](security-info-setup-auth-app.md).

    - **SMS-meddelanden.** Välj att skicka text meddelanden till din mobila enhet. Mer information om hur du konfigurerar textmeddelanden finns i [Konfigurera SMS-meddelanden som din verifierings metod](security-info-setup-text-msg.md).

    - **Telefonsamtal.** Välj att hämta ett telefonsamtal till ditt registrerade telefonnummer. Mer information om hur du konfigurerar telefonsamtal finns i [Konfigurera ett telefonnummer som verifierings metod](security-info-setup-phone-number.md).

    - **Säkerhets nyckel.** Välj att använda en Microsoft-kompatibel säkerhets nyckel. Mer information finns i [Konfigurera en säkerhets nyckel som verifierings metod](security-info-setup-security-key.md).

    - **E-postadress.** Välj att använda en alternativ e-postadress som kan användas utan lösen ord som du glömt eller saknar. Detta fungerar bara för lösen ords återställning, inte som en säkerhets verifierings metod. Mer information om hur du konfigurerar en e-postadress finns i [Konfigurera en e-postadress som verifierings metod](security-info-setup-email.md).

    - **Säkerhets frågor.** Välj att konfigurera och svara på fördefinierade säkerhets frågor som administratören har konfigurerat. Detta fungerar bara för lösen ords återställning, inte som en säkerhets verifierings metod. Mer information om säkerhets frågor finns i [Konfigurera säkerhets frågor som din verifierings metod](security-info-setup-questions.md).

3. När du har valt och konfigurerat dina metoder väljer du **Slutför** för att slutföra processen.

    > [!Note]
    > Information som lagts till för ditt telefonnummer eller din e-postadress delas inte med din organisations globala katalog. De enda personer som kan se den här informationen är du och din administratör. Du kan bara se svaren på dina säkerhets frågor.

## <a name="common-problems-and-their-solutions"></a>Vanliga problem och lösningar

 Här följer några vanliga fel och deras lösningar:

| Felmeddelande |  Möjlig lösning |
| --- | --- | --- |
| Kontakta administratören.<br>Vi har upptäckt att lösen ordet för ditt användar konto inte hanteras av Microsoft. Därför kan vi inte automatiskt återställa ditt lösen ord.<br>Kontakta IT-personalen om du vill ha mer hjälp.| Om du får det här fel meddelandet när du har skrivit ditt användar-ID, innebär det att din organisation hanterar ditt lösen ord internt och inte vill att du återställer ditt lösen ord från länken **kan inte komma åt ditt konto** . Om du vill återställa lösen ordet i den här situationen måste du kontakta din organisations supportavdelning eller be administratören om hjälp. |
| Ditt konto har inte Aktiver ATS för lösen ords återställning.<br>Din IT-personal har tyvärr inte konfigurerat ditt konto för användning med den här tjänsten.<br>Om du vill kan vi kontakta en administratör i din organisation för att återställa lösen ordet åt dig. | Om du får det här fel meddelandet när du har skrivit ditt användar-ID, innebär det att antingen din organisation inte har aktiverat funktionen för lösen ords återställning eller om du inte får använda den. Om du vill återställa lösen ordet i den här situationen måste du välja länken **kontakta en administratör** . När du klickar på länken skickas ett e-postmeddelande till din organisations supportavdelning eller administratör, så att du vet att du vill återställa lösen ordet. |
| Vi kunde inte verifiera ditt konto.<br>Om du vill kan vi kontakta en administratör i din organisation för att återställa lösen ordet åt dig. | Om du får det här fel meddelandet när du har skrivit ditt användar-ID, innebär det att din organisation har aktiverat lösen ords återställning och att du kan använda det, men som du inte har registrerat för tjänsten. I så fall måste du kontakta din organisations supportavdelning eller administratör för att återställa ditt lösen ord. Information om hur du registrerar dig för lösen ords återställning när du är tillbaka på enheten finns i processen ovan i den här artikeln. |

## <a name="next-steps"></a>Nästa steg

- [Ändra lösen ordet med hjälp av lösen ords återställning via självbetjäning](active-directory-passwords-update-your-own-password.md)

- [Sidan säkerhets information](https://mysignins.microsoft.com/security-info)

- [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

- [När du inte kan logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)