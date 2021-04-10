---
title: Aktivera kombinerad säkerhets informations registrering – Azure Active Directory
description: Lär dig hur du fören klar slutanvändaren med kombinerat Azure AD Multi-Factor Authentication och registrering av lösen ords återställning via självbetjäning.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcfaef2f518028762958477a5b0d326acc237d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938436"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Aktivera kombinerad säkerhets informations registrering i Azure Active Directory

Före kombinerad registrering har användare registrerat autentiseringsmetoder för Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning (SSPR) separat. Personer förväxlade över att liknande metoder användes för Azure AD Multi-Factor Authentication och SSPR, men de var tvungna att registrera sig för båda funktionerna. Med kombinerad registrering kan användarna registrera sig en gång och få fördelarna med både Azure AD Multi-Factor Authentication och SSPR.

> [!NOTE]
> Från den 15 augusti 2020 aktive ras alla nya Azure AD-klienter automatiskt för kombinerad registrering. 

För att se till att du förstår funktionerna och effekterna innan du aktiverar den nya upplevelsen, se de [kombinerade principerna för registrering av säkerhets information](concept-registration-mfa-sspr-combined.md).

![Kombinerad säkerhets informations registrering förbättrad upplevelse](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Slutför de här stegen för att aktivera kombinerad registrering:

1. Logga in på Azure Portal som en användar administratör eller global administratör.
2. Gå till **Azure Active Directory**  >  **användar inställningar**  >  **hantera användar funktions förhands gransknings inställningar**.
3. Under **användare kan använda den kombinerade säkerhets informations registreringen**, väljer du att aktivera för en **vald** grupp användare eller för **alla** användare.

   ![Aktivera den kombinerade säkerhets informations upplevelsen för användare](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sina telefonnummer eller mobilappar via den nya upplevelsen använda dem för Azure AD Multi-Factor Authentication och SSPR, om dessa metoder är aktiverade i Azure AD Multi-Factor Authentication-och SSPR-principerna.
>
> Om du sedan inaktiverar den här upplevelsen krävs det att användare som går till den tidigare SSPR-registrerings sidan `https://aka.ms/ssprsetup` måste utföra Multi-Factor Authentication innan de kan komma åt sidan.

Om du har konfigurerat *plats till zon tilldelnings lista* i Internet Explorer måste följande platser finnas i samma zon:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Om du vill skydda när och hur användare registrerar sig för Azure AD Multi-Factor Authentication och lösen ords återställning via självbetjäning, kan du använda användar åtgärder i principen för villkorlig åtkomst. Den här funktionen kan vara aktive rad i organisationer som vill att användare ska kunna registrera sig för Azure AD Multi-Factor Authentication och SSPR från en central plats, till exempel en betrott nätverks plats under en automatisk registrering.

> [!NOTE]
> Den här principen gäller bara när en användare får åtkomst till en kombinerad registrerings sida. Den här principen tillämpar inte MFA-registrering när en användare kommer åt andra program.
>
> Du kan skapa en princip för MFA-registrering med hjälp av [Azure Identity Protection – Konfigurera MFA-principen](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Mer information om hur du skapar betrodda platser i villkorlig åtkomst finns i [Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Utför följande steg för att skapa en princip som gäller för alla valda användare som försöker registrera med hjälp av den kombinerade registrerings upplevelsen, och blockerar åtkomsten om de inte ansluter från en plats som har marker ATS som ett betrott nätverk:

1. I **Azure Portal** bläddrar du till **Azure Active Directory**  >  **säkerhet** för  >  **villkorlig åtkomst**.
1. Välj **+ ny princip**.
1. Ange ett namn för principen, till exempel *kombinerad säkerhets informations registrering på betrodda nätverk*.
1. Under **Tilldelningar** väljer du **Användare och grupper**. Välj de användare och grupper som du vill att den här principen ska tillämpas på och välj sedan **Slutför**.

   > [!WARNING]
   > Användare måste aktive ras för kombinerad registrering.

1. Under **molnappar eller åtgärder** väljer du **användar åtgärder**. Markera **Registrera säkerhets information** och välj sedan **OK**.

    ![Skapa en princip för villkorlig åtkomst för att kontrol lera registreringen av säkerhets information](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Konfigurera följande alternativ under **villkor**  >  **platser**:
   1. Konfigurera **Ja**.
   1. Ta med **vilken plats som helst**.
   1. Undanta **alla betrodda platser**.
1. Välj **färdig** i fönstret *platser* och välj sedan **färdig** i fönstret *villkor* .
1. Under **åtkomst kontroller**  >  **beviljas** väljer du **blockera åtkomst** och **väljer** sedan.
1. Ange **Aktivera princip** till **På**.
1. Om du vill slutföra principen väljer du **skapa**.

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp kan du läsa mer i [Felsöka kombinerad säkerhets informations registrering](howto-registration-mfa-sspr-combined-troubleshoot.md) eller ta reda [på vad är plats villkoret i villkorlig åtkomst för Azure AD?](../conditional-access/location-condition.md)

När användarna har Aktiver ATS för kombinerad registrering kan du [Aktivera lösen ords återställning via självbetjäning](tutorial-enable-sspr.md) och [aktivera Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Om det behövs kan du läsa om hur du [tvingar användare att registrera autentiseringsmetoder igen](howto-mfa-userdevicesettings.md#manage-user-authentication-options).
