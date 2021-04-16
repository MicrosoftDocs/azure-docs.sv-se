---
title: SMS-baserad användar inloggning för Azure Active Directory
description: Lär dig hur du konfigurerar och gör det möjligt för användare att logga in på Azure Active Directory via SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84d55e2d3a2f49a870c1e57eeed3c5c0caeba4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530412"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Konfigurera och aktivera användare för SMS-baserad autentisering med hjälp av Azure Active Directory 

För att förenkla och skydda inloggningen till program och tjänster Azure Active Directory (Azure AD) flera autentiseringsalternativ. Med SMS-baserad autentisering kan användarna logga in utan att ange eller ens känna till deras användarnamn och lösenord. När deras konto har skapats av en identitetsadministratör kan de ange sitt telefonnummer i inloggningsuppmaning. De får en autentiseringskod via sms som de kan ange för att slutföra inloggningen. Den här autentiseringsmetoden förenklar åtkomsten till program och tjänster, särskilt för Frontline-arbetare.

Den här artikeln visar hur du aktiverar SMS-baserad autentisering för utvalda användare eller grupper i Azure AD.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration skapar [du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory som är associerad med din prenumeration.
    * Om det behövs [skapar du en Azure Active Directory eller][create-azure-ad-tenant] [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver *globala administratörsbehörigheter* i din Azure AD-klientorganisation för att aktivera SMS-baserad autentisering.
* Varje användare som är aktiverad i principen för autentiseringsmetod för SMS måste vara licensierad, även om de inte använder den. Varje aktiverad användare måste ha någon av följande Azure AD-, EMS- Microsoft 365-licenser:
    * [Microsoft 365 (M365) F1 eller F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 eller E5][ems-licensing] [eller Microsoft 365 (M365) E3 eller E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för SMS-baserad autentisering:

* SMS-baserad autentisering är för närvarande inte kompatibel med Azure AD Multi-Factor Authentication.
* Förutom för Teams är SMS-baserad autentisering inte kompatibel med interna Office-program.
* SMS-baserad autentisering rekommenderas inte för B2B-konton.
* Federerade användare autentiseras inte i hemklientorganisationen. De autentiserar bara i molnet.

## <a name="enable-the-sms-based-authentication-method"></a>Aktivera den SMS-baserade autentiseringsmetoden

Det finns tre huvudsteg för att aktivera och använda SMS-baserad autentisering i din organisation:

* Aktivera principen för autentiseringsmetod.
* Välj användare eller grupper som kan använda den SMS-baserade autentiseringsmetoden.
* Tilldela ett telefonnummer för varje användarkonto.
    * Det här telefonnumret kan tilldelas i Azure Portal (som visas i den här artikeln) och *i Min personal* eller *Mitt konto*.

Först ska vi aktivera SMS-baserad autentisering för din Azure AD-klientorganisation.

1. Logga in på [Azure Portal][azure-portal] som *global administratör.*
1. Sök efter och välj **Azure Active Directory**.
1. På navigeringsmenyn till vänster i fönstret Azure Active Directory väljer du **Säkerhet > Autentiseringsmetoder > Princip** för autentiseringsmetod .

    [![Bläddra till och välj fönstret Autentiseringsmetodprincip i Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. I listan över tillgängliga autentiseringsmetoder väljer du **Textmeddelande.**
1. Ange **Aktivera** till *Ja.*

    ![Aktivera textautentisering i principfönstret för autentiseringsmetod](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Du kan välja att aktivera SMS-baserad autentisering *för Alla användare* eller Välj *användare* och grupper. I nästa avsnitt aktiverar du SMS-baserad autentisering för en testanvändare.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Tilldela autentiseringsmetoden till användare och grupper

När SMS-baserad autentisering är aktiverat i din Azure AD-klient väljer du nu vissa användare eller grupper som ska tillåtas att använda den här autentiseringsmetoden.

1. I fönstret autentiseringsprincip för textmeddelande anger du **Mål** till *Välj användare.*
1. Välj lägg **till användare eller grupper** och välj sedan en testanvändare eller grupp, till exempel *Contoso-användare* eller *Contoso SMS-användare.*

    [![Välj användare eller grupper för att aktivera för SMS-baserad autentisering i Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. När du har valt användare eller grupper väljer du **Välj** och sparar sedan **den uppdaterade** principen för autentiseringsmetod.

Varje användare som är aktiverad i principen för autentiseringsmetod för SMS måste vara licensierad, även om de inte använder den. Kontrollera att du har rätt licenser för de användare som du aktiverar i principen för autentiseringsmetod, särskilt när du aktiverar funktionen för stora grupper av användare.

## <a name="set-a-phone-number-for-user-accounts"></a>Ange ett telefonnummer för användarkonton

Användare har nu aktiverats för SMS-baserad autentisering, men deras telefonnummer måste associeras med användarprofilen i Azure AD innan de kan logga in. Användaren kan [ange det här telefonnumret själva](../user-help/sms-sign-in-explainer.md) i Mitt *konto,* eller så kan du tilldela telefonnumret med hjälp av Azure Portal. Telefonnummer kan anges av *globala administratörer, autentiseringsadministratörer* eller  *privilegierade autentiseringsadministratörer.*

När ett telefonnummer har angetts för SMS-sign är det också tillgängligt för användning med [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] och lösenordsåterställning via [självbetjäning.][tutorial-sspr]

1. Sök efter och välj **Azure Active Directory**.
1. På navigeringsmenyn till vänster i fönstret Azure Active Directory väljer du **Användare**.
1. Välj den användare som du aktiverade för SMS-baserad autentisering i föregående avsnitt, till exempel *Contoso-användare,* och välj sedan **Autentiseringsmetoder**.
1. Välj **+ Lägg till autentiseringsmetod** och välj *sedan* Telefonnummer i listrutan **Välj metod.**

    Ange användarens telefonnummer, inklusive landskoden, till exempel *+1 xxxxxxxxx*. Den Azure Portal verifierar att telefonnumret har rätt format.

    I listrutan *Telefontyp* väljer du Sedan *Mobil,* *Alternativ mobil* eller *Annan* efter behov.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Ange ett telefonnummer för en användare i Azure Portal som ska användas med SMS-baserad autentisering":::

    Telefonnumret måste vara unikt i din klientorganisation. Om du försöker använda samma telefonnummer för flera användare visas ett felmeddelande.

1. Om du vill tillämpa telefonnumret på en användares konto väljer du Lägg **till**.

När det har etablerats visas en bockmarkering *för SMS-inloggning aktiverat.*

## <a name="test-sms-based-sign-in"></a>Testa SMS-baserad inloggning

Testa användarkontot som nu är aktiverat för SMS-baserad inloggning genom att utföra följande steg:

1. Öppna ett nytt InPrivate- eller Incognito-webbläsarfönster för att [https://www.office.com][office]
1. Välj Logga in i det övre **högra hörnet.**
1. Vid inloggningsupp prompten anger du det telefonnummer som är associerat med användaren i föregående avsnitt och väljer **sedan Nästa.**

    ![Ange ett telefonnummer i inloggningsupp prompten för testanvändaren](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Ett SMS skickas till det angivna telefonnumret. Slutför inloggningsprocessen genom att ange den 6-siffriga koden som angavs i textmeddelandet vid inloggningsuppiseringen.

    ![Ange bekräftelsekoden som skickas via SMS till användarens telefonnummer](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Användaren är nu inloggad utan att behöva ange ett användarnamn eller lösenord.

## <a name="troubleshoot-sms-based-sign-in"></a>Felsöka SMS-baserad inloggning

Följande scenarier och felsökningssteg kan användas om du har problem med att aktivera och använda SMS-baserad inloggning.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefonnummer som redan har angetts för ett användarkonto

Om en användare redan har registrerat sig för Azure AD Multi-Factor Authentication och/eller lösenordsåterställning via självbetjäning (SSPR) har de redan ett telefonnummer kopplat till sitt konto. Det här telefonnumret är inte automatiskt tillgängligt för användning med SMS-baserad inloggning.

En användare som redan har ett telefonnummer för sitt konto visas med knappen Aktivera *för SMS-inloggning* på sidan **Min** profil. Välj den här knappen så är kontot aktiverat för användning med SMS-baserad inloggning och den tidigare Azure AD Multi-Factor Authentication- eller SSPR-registreringen.

Mer information om slutanvändarupplevelsen finns i [ANVÄNDArgränssnittet för SMS-inloggning för telefonnummer.](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fel vid försök att ange ett telefonnummer på ett användarkonto

Om du får ett felmeddelande när du försöker ange ett telefonnummer för ett användarkonto i Azure Portal du följande felsökningssteg:

1. Kontrollera att du är aktiverad för den SMS-baserade inloggningen.
1. Bekräfta att användarkontot är aktiverat i principen *för autentiseringsmetod för* TEXT-meddelande.
1. Se till att du anger telefonnumret med rätt formatering, enligt valideringen i Azure Portal (till exempel *+1 4251234567*).
1. Kontrollera att telefonnumret inte används någon annanstans i din klientorganisation.
1. Kontrollera att det inte finns något röstnummer inställt på kontot. Om ett röstnummer har angetts tar du bort och försöker med telefonnumret igen.

## <a name="next-steps"></a>Nästa steg

Ytterligare sätt att logga in på Azure AD utan lösenord, till exempel Microsoft Authenticator-appen eller FIDO2-säkerhetsnycklar, finns i Alternativ för lösenordsfri autentisering [för Azure AD.][concepts-passwordless]

Du kan också använda Microsoft Graph REST API för [att][rest-enable] aktivera [eller inaktivera][rest-disable] SMS-baserad inloggning.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
