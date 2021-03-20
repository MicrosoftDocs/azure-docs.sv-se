---
title: Visa och Sök efter din senaste inloggnings aktivitet från sidan mina inloggnings program Azure Active Directory | Microsoft Docs
description: Information om hur du visar och söker efter din senaste inloggnings aktivitet från sidan mina inloggningar i mitt konto-portalen.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096080"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Visa och Sök efter din senaste inloggnings aktivitet från sidan mina inloggningar

Du kan visa all din senaste inloggnings aktivitet för arbets-eller skol konton från sidan **Mina inloggningar** i **mitt konto** Portal. Genom att granska inloggnings historiken kan du söka efter ovanlig aktivitet genom att hjälpa dig att se:

- Om någon försöker gissa ditt lösen ord.
- Om en angripare har loggat in på ditt konto och från vilken plats.
- Vilka appar angriparen försökte få åtkomst till.

>[!Note]
> Om du ser ett fel när du loggar in med en personlig Microsoft-konto kan du fortfarande logga in med domän namnet för din organisation (t. ex. contoso.com) eller klient organisations **-ID: t** för din organisation från administratören i någon av följande URL: er:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Visa din senaste inloggningsaktivitet

1. Logga in på ditt arbets-eller skol konto och gå sedan till din https://myaccount.microsoft.com/ sida.

2. Välj **Mina inloggningar** i det vänstra navigerings fönstret eller Välj länken **Granska senaste aktiviteten** från **mitt inloggnings-** block.

    ![Sidan mitt konto, som visar markerade senaste aktivitets länkar](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expandera och granska vart och ett av inloggnings objekten och se till att du känner igen dem. Om du hittar ett inloggnings objekt som inte ser bekant ut ändrar du ditt lösen ord för att skydda kontot om det är komprometterat.

    ![Sidan senaste aktivitet med utökad inloggnings information](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Om du ser en lyckad inloggning

Ibland kan en lyckad inloggning från en okänd plats, webbläsare eller operativ system visas när du läser din egen normala inloggnings aktivitet. Okända inloggningar kan betyda att en angripare har fått åtkomst till ditt konto. Om du ser aktivitet som du inte auktoriserat rekommenderar vi att du omedelbart ändrar ditt lösen ord och sedan går till [säkerhets information](https://mysignins.microsoft.com/security-info) för att uppdatera säkerhets inställningarna.

Innan du fastställer något är felaktigt kontrollerar du att du inte ser ett falskt positivt objekt (där objektet ser tveksamt ut, men är OK). Vi fastställer till exempel din ungefärliga plats och mappar baserat på din IP-adress. Mobila nätverk är särskilt svåra att hitta eftersom de ibland dirigerar trafik genom avlägsna platser. Även om du loggar in med din mobila enhet i Washington-tillstånd kan platsen visa att inloggningen kommer från Kalifornien. Vi föreslår starkt att du bara kontrollerar informationen bortom platsen. Se till att både operativ systemet, webbläsaren och appen är så bra.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Om du ser en misslyckad inloggning

Om du ser en misslyckad inloggning, kan det betyda att du felskrivit dina autentiseringsuppgifter. Det kan också betyda att en angripare försökte gissa ditt lösen ord. För att svara på den här risken behöver du inte ändra ditt lösen ord, men vi rekommenderar att du registrerar dig för Azure AD Multi-Factor Authentication (MFA). Med Multi-Factor Authentication, även om hackaren gissar ditt lösen ord, räcker det inte att komma åt kontot.

![Inloggnings panelen misslyckades](media/my-account-portal-sign-ins-page/unsuccessful.png)

Om du ser en misslyckad inloggning, med en anteckning under **session-aktivitet** som säger, `Additional verification failed, invalid code` innebär det att autentiseringsuppgifterna för primär autentisering lyckades, men Multi-Factor Authentication misslyckades. Det här tillståndet kan betyda att en angripare felaktigt gissar ditt lösen ord men inte kunde skicka Multi-Factor Authentication-utmaningen. Vi rekommenderar att du fortfarande ändrar ditt lösen ord, eftersom angriparen kanske redan har det och går till sidan [säkerhets information](https://mysignins.microsoft.com/security-info) för att uppdatera säkerhets inställningarna.

## <a name="search-for-specific-sign-in-activity"></a>Sök efter en speciell inloggnings aktivitet

Du kan söka efter din senaste inloggnings aktivitet med någon av de tillgängliga uppgifterna. Du kan till exempel söka efter din senaste inloggnings aktivitet efter operativ system, plats, app och så vidare.

1. På sidan **Granska senaste aktivitet** anger du den information som du vill söka efter i **Sök** fältet. Skriv till exempel om `Unsuccessful` du vill söka efter all misslyckad inloggnings aktivitet som samlas in av appen mitt konto.

2. Välj **Sök** knappen för att börja söka.

    ![Sidan senaste aktivitet, visar markerat Sök fält, Sök knapp och resultat](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Bekräfta ovanlig aktivitet

Inloggningar som flaggats som ovanlig aktivitet kan bekräftas i panelen för aktiviteten på sidan **Mina inloggningar** .

![Ovanlig inloggnings panel för bekräftelse på att du har gjort eller inte försökt logga in](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Nästa steg

När du har visat din senaste inloggnings aktivitet kan du:

- Visa eller hantera din [säkerhets information](./security-info-setup-signin.md).

- Visa eller hantera dina anslutna [enheter](my-account-portal-devices-page.md).

- Visa eller hantera dina [organisationer](my-account-portal-organizations-page.md).

- Se hur din organisation [använder dina sekretess-relaterade data](my-account-portal-privacy-page.md).

- Ändra [Portal inställningarna för mitt konto](my-account-portal-settings.md)