---
title: Aktivera kombinerad registrering av säkerhetsinformation – Azure Active Directory
description: Lär dig hur du förenklar slutanvändarens upplevelse med kombinerad Azure AD Multi-Factor Authentication och registrering av lösenordsåterställning via självbetjäning.
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
ms.openlocfilehash: e6eba5ac4ed61847596e12f56544e6d07dca8075
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829583"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Aktivera kombinerad registrering av säkerhetsinformation i Azure Active Directory

Före kombinerad registrering registrerade användarna autentiseringsmetoder för Azure AD Multi-Factor Authentication och lösenordsåterställning via självbetjäning (SSPR) separat. Personer var förvirrade över att liknande metoder användes för Azure AD Multi-Factor Authentication och SSPR, men de var tvungna att registrera sig för båda funktionerna. Med kombinerad registrering kan användarna nu registrera sig en gång och få fördelarna med både Azure AD Multi-Factor Authentication och SSPR.

> [!NOTE]
> Från och med 15 augusti 2020 aktiveras alla nya Azure AD-klienter automatiskt för kombinerad registrering. Klienter som skapas efter det här datumet kan inte använda de äldre registreringsarbetsflödena.

Om du vill se till att du förstår funktionerna och effekterna innan du aktiverar den nya upplevelsen kan du gå till [Begrepp för kombinerad registrering av säkerhetsinformation.](concept-registration-mfa-sspr-combined.md)

![Förbättrad upplevelse vid kombinerad registrering av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>Aktivera kombinerad registrering

Utför följande steg för att aktivera kombinerad registrering:

1. Logga in på Azure Portal som användaradministratör eller global administratör.
2. Gå till **Azure Active Directory**  >  **Användarinställningar Hantera inställningar** för  >  **förhandsgranskning av användarfunktion.**
3. Under **Användare kan använda den kombinerade registreringsupplevelsen för säkerhetsinformation** väljer du att aktivera för en **vald** grupp med användare eller **för Alla** användare.

   ![Aktivera den kombinerade säkerhetsinformationsupplevelsen för användare](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> När du har aktiverat kombinerad registrering kan användare som registrerar eller bekräftar sitt telefonnummer eller sin mobilapp via den nya upplevelsen använda dem för Azure AD Multi-Factor Authentication och SSPR, om dessa metoder är aktiverade i Azure AD Multi-Factor Authentication- och SSPR-principerna.
>
> Om du sedan inaktiverar den här funktionen måste användare som går till den tidigare SSPR-registreringssidan på utföra `https://aka.ms/ssprsetup` multifaktorautentisering innan de kan komma åt sidan.

Om du har konfigurerat listan *Plats till zontilldelning* i Internet Explorer måste följande platser finnas i samma zon:

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>Principer för villkorlig åtkomst för kombinerad registrering

Om du vill skydda när och hur användare registrerar sig för Azure AD Multi-Factor Authentication och lösenordsåterställning via självbetjäning kan du använda användaråtgärder i principen för villkorsstyrd åtkomst. Den här funktionen kan aktiveras i organisationer som vill att användarna ska registrera sig för Azure AD Multi-Factor Authentication och SSPR från en central plats, till exempel en betrodd nätverksplats under HR-registrering.

> [!NOTE]
> Den här principen gäller endast när en användare kommer åt en kombinerad registreringssida. Den här principen framtvingar inte MFA-registrering när en användare kommer åt andra program.
>
> Du kan skapa en MFA-registreringsprincip med hjälp av [Azure Identity Protection – Konfigurera MFA-princip](../identity-protection/howto-identity-protection-configure-mfa-policy.md).

Mer information om hur du skapar betrodda platser i villkorsstyrd åtkomst finns i Vad [är platsvillkoret i Azure Active Directory villkorlig åtkomst?](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Skapa en princip för att kräva registrering från en betrodd plats

Utför följande steg för att skapa en princip som gäller för alla valda användare som försöker registrera sig med hjälp av den kombinerade registreringsupplevelsen och blockerar åtkomst om de inte ansluter från en plats som markerats som betrott nätverk:

1. I den **Azure Portal** bläddrar du till **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Välj **+ Ny princip.**
1. Ange ett namn för den här principen, till exempel *Kombinerad registrering av säkerhetsinformation på betrodda nätverk.*
1. Under **Tilldelningar** väljer du **Användare och grupper**. Välj de användare och grupper som du vill att principen ska gälla för och välj sedan **Klar.**

   > [!WARNING]
   > Användarna måste vara aktiverade för kombinerad registrering.

1. Under **Molnappar eller åtgärder** väljer du **Användaråtgärder.** Markera **Registrera säkerhetsinformation** och välj sedan **Klar.**

    ![Skapa en princip för villkorlig åtkomst för att kontrollera registreringen av säkerhetsinformation](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. Under   >  **Villkorsplatser** konfigurerar du följande alternativ:
   1. Konfigurera **Ja.**
   1. Ta **med valfri plats.**
   1. Undanta **Alla betrodda platser.**
1. Välj **Klar** i *fönstret Platser* och välj sedan **Klar** i *fönstret* Villkor.
1. Under **Åtkomstkontroller**  >  **Bevilja** väljer du **Blockera åtkomst** och sedan **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Slutför principen genom att välja **Skapa**.

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp kan du läsa [felsöka kombinerad registrering av säkerhetsinformation](howto-registration-mfa-sspr-combined-troubleshoot.md) eller läsa Vad är [platsvillkoret i villkorsstyrd åtkomst i Azure AD?](../conditional-access/location-condition.md)

När användarna har aktiverats för [](tutorial-enable-sspr.md) kombinerad registrering kan du aktivera lösenordsåterställning via självbetjäning [och aktivera Azure AD Multi-Factor Authentication.](tutorial-enable-azure-mfa.md)

Om det behövs kan du lära dig [hur du tvingar användare att registrera autentiseringsmetoder på ett annat sätt.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)
