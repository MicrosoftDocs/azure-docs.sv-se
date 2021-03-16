---
title: SMS-baserad användar inloggning för Azure Active Directory
description: Lär dig hur du konfigurerar och gör det möjligt för användare att logga in på Azure Active Directory med SMS
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
ms.openlocfilehash: 8e9052502eba71f025bb6724278b7001173c5217
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491625"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Konfigurera och aktivera användare för SMS-baserad autentisering med hjälp av Azure Active Directory 

Azure Active Directory (Azure AD) tillhandahåller flera autentiseringsalternativ för att förenkla och säkra inloggning till program och tjänster. SMS-baserad autentisering gör det möjligt för användarna att logga in utan att behöva ange användar namn och lösen ord. När kontot har skapats av en identitets administratör kan de ange sitt telefonnummer vid inloggnings meddelandet. De får en autentiseringsnyckel via SMS som de kan ange för att slutföra inloggningen. Den här autentiseringsmetoden fören klar åtkomsten till program och tjänster, särskilt för Frontline-anställda.

Den här artikeln visar hur du aktiverar SMS-baserad autentisering för utvalda användare eller grupper i Azure AD.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera SMS-baserad autentisering.
* Varje användare som är aktive rad i principen för autentiseringsmetoden för SMS måste vara licensierad, även om de inte använder den. Varje aktive rad användare måste ha någon av följande Azure AD-, EMS-Microsoft 365-licenser:
    * [Microsoft 365 (M365) F1 eller F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 eller E5][ems-licensing] eller [Microsoft 365 (M365) E3 eller E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för SMS-baserad autentisering:

* SMS-baserad autentisering är för närvarande inte kompatibel med Azure AD Multi-Factor Authentication.
* Förutom för team är SMS-baserad autentisering inte kompatibel med inbyggda Office-program.
* SMS-baserad autentisering rekommenderas inte för B2B-konton.
* Federerade användare autentiseras inte i hem klienten. De autentiseras bara i molnet.

## <a name="enable-the-sms-based-authentication-method"></a>Aktivera den SMS-baserade autentiseringsmetoden

Det finns tre huvudsakliga steg för att aktivera och använda SMS-baserad autentisering i din organisation:

* Aktivera principen för autentiserings metoden.
* Välj användare eller grupper som kan använda den SMS-baserade autentiseringsmetoden.
* Tilldela ett telefonnummer för varje användar konto.
    * Det här telefonnumret kan tilldelas i Azure Portal (som visas i den här artikeln) och i *min personal* eller *mitt konto*.

Först ska vi aktivera SMS-baserad autentisering för Azure AD-klienten.

1. Logga in på [Azure Portal][azure-portal] som *Global administratör*.
1. Sök efter och välj **Azure Active Directory**.
1. I navigerings menyn till vänster i fönstret Azure Active Directory väljer du **säkerhets > autentiseringsmetoder > princip för autentiserings metod**.

    [![Bläddra till och välj principen för autentiseringsmetod i Azure Portal.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. I listan över tillgängliga autentiseringsmetoder väljer du **textmeddelande**.
1. Ange **Tillåt** till *Ja*.

    ![Aktivera textautentisering i princip fönstret för autentiseringsmetoder](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Du kan välja att aktivera SMS-baserad autentisering för *alla användare* eller *välja användare* och grupper. I nästa avsnitt aktiverar du SMS-baserad autentisering för en test användare.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Tilldela autentiseringsmetoden till användare och grupper

Med SMS-baserad autentisering aktive rad i din Azure AD-klient väljer du nu vissa användare eller grupper som ska kunna använda den här autentiseringsmetoden.

1. I fönstret princip för SMS-autentisering anger du **mål** för *utvalda användare*.
1. Välj att **lägga till användare eller grupper** och välj sedan en test användare eller grupp, till exempel *contoso-användare* eller *contoso SMS-användare*.

    [![Välj användare eller grupper som ska aktive ras för SMS-baserad autentisering i Azure Portal.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. När du har valt användare eller grupper väljer du **Välj** och sedan **Spara** den uppdaterade principen för autentisering.

Varje användare som är aktive rad i principen för autentiseringsmetoden för SMS måste vara licensierad, även om de inte använder den. Kontrol lera att du har rätt licenser för de användare som du aktiverar i principen för autentiseringsmetod, särskilt när du aktiverar funktionen för stora grupper av användare.

## <a name="set-a-phone-number-for-user-accounts"></a>Ange ett telefonnummer för användar konton

Användare är nu aktiverade för SMS-baserad autentisering, men deras telefonnummer måste kopplas till användar profilen i Azure AD innan de kan logga in. Användaren kan [Ange det här telefonnumret](../user-help/sms-sign-in-explainer.md) i *mitt konto*, eller så kan du tilldela telefonnumret med hjälp av Azure Portal. Telefonnummer kan ställas in av *globala administratörer*, *autentisera administratörer* eller *privilegierade autentiserings administratörer*.

När ett telefonnummer anges för SMS-signera, är det också tillgängligt för användning med [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] och [lösen ords återställning][tutorial-sspr]via självbetjäning.

1. Sök efter och välj **Azure Active Directory**.
1. I navigerings menyn till vänster i fönstret Azure Active Directory väljer **du användare**.
1. Välj den användare som du har aktiverat för SMS-baserad autentisering i föregående avsnitt, till exempel *contoso-användare*, och välj **autentiseringsmetoder**.
1. Välj **+ Lägg till autentiseringsmetod** och välj sedan **telefonnummer** i den nedrullningsbara menyn *Välj metod* .

    Ange användarens telefonnummer, inklusive lands koden, t. ex. *+ 1 xxxxxxxxx*. Azure Portal verifierar att telefonnumret har rätt format.

    Gå sedan till List rutan *telefon typ* och välj *mobil*, *alternativ mobil* eller *annan* efter behov.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Ange ett telefonnummer för en användare i Azure Portal som ska användas med SMS-baserad autentisering":::

    Telefonnumret måste vara unikt i din klient organisation. Om du försöker använda samma telefonnummer för flera användare visas ett fel meddelande.

1. Om du vill använda telefonnumret på ett användar konto väljer du **Lägg till**.

När registreringen är klar visas en bock för att SMS- *inloggningen ska vara aktive rad*.

## <a name="test-sms-based-sign-in"></a>Testa SMS-baserad inloggning

Utför följande steg för att testa det användar konto som nu är aktiverat för SMS-baserad inloggning:

1. Öppna ett nytt InPrivate-eller Incognito-webbläsarfönster för att [https://www.office.com][office]
1. Välj **Logga** in i det övre högra hörnet.
1. I inloggnings meddelandet anger du det telefonnummer som är associerat med användaren i föregående avsnitt och väljer sedan **Nästa**.

    ![Ange ett telefonnummer vid inloggnings frågan för test användaren](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Ett textmeddelande skickas till det angivna telefonnumret. Slutför inloggnings processen genom att ange den 6-siffriga kod som anges i textmeddelandet vid inloggnings frågan.

    ![Ange bekräftelse koden som skickas via SMS till användarens telefonnummer](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Användaren är nu inloggad utan att behöva ange ett användar namn eller lösen ord.

## <a name="troubleshoot-sms-based-sign-in"></a>Felsök SMS-baserad inloggning

Följande scenarier och fel söknings steg kan användas om du har problem med att aktivera och använda SMS-baserad inloggning.

### <a name="phone-number-already-set-for-a-user-account"></a>Telefonnumret har redan angetts för ett användar konto

Om en användare redan har registrerat sig för Azure AD Multi-Factor Authentication och/eller självbetjäning för återställning av lösen ord (SSPR) har de redan ett telefonnummer som är kopplat till sitt konto. Det här telefonnumret kan inte automatiskt användas med SMS-baserad inloggning.

En användare som har ett telefonnummer som redan har angetts för sitt konto visas en knapp för att *Aktivera SMS-inloggning* på **min profil** sida. Välj den här knappen så är kontot aktiverat för användning med SMS-baserad inloggning och föregående Azure AD Multi-Factor Authentication-eller SSPR-registrering.

Mer information om slut användar upplevelsen finns i SMS- [inloggning användar upplevelse för telefonnummer](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fel vid försök att ange ett telefonnummer för ett användar konto

Om du får ett fel meddelande när du försöker ange ett telefonnummer för ett användar konto i Azure Portal kan du läsa följande fel söknings steg:

1. Se till att du är aktive rad för den SMS-baserade inloggningen.
1. Bekräfta att användar kontot är aktiverat i principen för autentiseringsmetoden för *SMS* .
1. Se till att ange telefonnumret med rätt formatering, som verifieras i Azure Portal (till exempel *+ 1 4251234567*).
1. Kontrol lera att telefonnumret inte används någon annan stans i din klient organisation.
1. Kontrol lera att det inte finns något röst nummer angivet för kontot. Om ett röst nummer anges tar du bort och försöker igen.

## <a name="next-steps"></a>Nästa steg

Ytterligare sätt att logga in på Azure AD utan lösen ord, till exempel Microsoft Authenticator app-eller FIDO2-säkerhetsnycklar, finns i [alternativ för lösen ords lös autentisering för Azure AD][concepts-passwordless].

Du kan också använda Microsoft Graph REST API beta för att [Aktivera][rest-enable] eller [inaktivera][rest-disable] SMS-baserad inloggning.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
