---
title: Vanliga frågor och svar (FAQ) för Azure Active Directory B2C
description: Svar på vanliga frågor om Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85ce79644ead939eaeffa5cfffeb495da15e7fe5
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270673"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: vanliga frågor och svar

På den här sidan besvaras vanliga frågor om Azure Active Directory B2C (Azure AD B2C). Fortsätt att söka efter uppdateringar igen.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Varför kan jag inte komma åt Azure AD B2C-tillägget i Azure Portal?

Det finns två vanliga orsaker till varför Azure AD-tillägget inte fungerar för dig. Azure AD B2C kräver att din användar roll i katalogen är global administratör. Kontakta administratören om du anser att du ska ha åtkomst. Om du har globala administratörs behörigheter kontrollerar du att du är i en Azure AD B2C katalog och inte en Azure Active Directory katalog. Du kan se instruktioner för att [skapa en Azure AD B2C klient](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan jag använda Azure AD B2C funktioner i min befintliga, anställd-baserade Azure AD-klient?

Azure AD och Azure AD B2C är separata produkt erbjudanden och kan inte samverka i samma klient organisation. En Azure AD-klient representerar en organisation. En Azure AD B2C klient representerar en samling identiteter som ska användas med förlitande parters program. Genom att lägga till en **ny OpenID Connect-Provider** under **Azure AD B2C > identitets leverantörer** eller med anpassade principer, kan Azure AD B2C FEDERERa till Azure AD som tillåter autentisering av anställda i en organisation.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan jag använda Azure AD B2C för att tillhandahålla social inloggning (Facebook och Google +) i Office 365?

Azure AD B2C kan inte användas för att autentisera användare för Microsoft Office 365. Azure AD är Microsofts lösning för att hantera medarbetarnas åtkomst till SaaS-appar och har funktioner som har utformats för detta ändamål som licensiering och villkorlig åtkomst. Azure AD B2C tillhandahåller en identitets-och åtkomst hanterings plattform för att skapa webb-och mobil program. När Azure AD B2C har kon figurer ATS för att federera till en Azure AD-klient hanterar Azure AD-klienten medarbetares åtkomst till program som förlitar sig på Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Vad är lokala konton i Azure AD B2C? Hur skiljer de sig från arbets-eller skol konton i Azure AD?

I en Azure AD-klient är det användare som tillhör klient inloggningen med en e-postadress till formuläret `<xyz>@<tenant domain>` . `<tenant domain>`Är en av de verifierade domänerna i klient organisationen eller den första `<...>.onmicrosoft.com` domänen. Den här typen av konto är ett arbets-eller skol konto.

I en Azure AD B2C klient vill de flesta appar att användaren loggar in med valfri e-postadress (till exempel,, joe@comcast.net bob@gmail.com sarah@contoso.com eller jim@live.com ). Den här typen av konto är ett lokalt konto. Vi stöder också godtyckliga användar namn som lokala konton (till exempel Johan, Robert, Sarah eller Jim). Du kan välja en av dessa två lokala konto typer när du konfigurerar identitets leverantörer för Azure AD B2C i Azure Portal. I Azure AD B2C klient väljer du **identitets leverantörer**, väljer **lokalt konto**och väljer sedan **användar namn**.

Användar konton för program kan skapas via ett registrerings användar flöde, inloggnings-eller inloggnings användar flöde, Microsoft Graph-API eller i Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Vilka leverantörer av sociala identitet stöder du nu? Vilka planerar du att stödja i framtiden?

Vi stöder för närvarande flera sociala identitets leverantörer, till exempel Amazon, Facebook, GitHub (för hands version), Google, LinkedIn, Microsoft-konto (MSA), QQ (för hands version), Twitter, WeChat (för hands version) och Weibo (för hands version). Vi utvärderar att lägga till stöd för andra populära sociala identitets leverantörer baserat på kundens efter frågan.

Azure AD B2C stöder också [anpassade principer](custom-policy-overview.md). Med anpassade principer kan du skapa en egen princip för alla identitets leverantörer som stöder [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) eller SAML. Kom igång med anpassade principer genom att checka ut vårt [Start paket för anpassad princip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan jag konfigurera omfattningar för att samla in mer information om användare från olika sociala identitets leverantörer?

Nej. Standard omfattningarna som används för vår uppsättning sociala identitets leverantörer är:

* Facebook: e-post
* Google +: e-post
* Microsoft-konto: e-OpenID e-postprofil
* Amazon:-profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Måste mitt program köras på Azure för att det ska fungera med Azure AD B2C?

Nej, du kan vara värd för ditt program var som helst (i molnet eller lokalt). Allt det behöver för att interagera med Azure AD B2C är möjligheten att skicka och ta emot HTTP-förfrågningar på offentligt tillgängliga slut punkter.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Jag har flera Azure AD B2C klienter. Hur kan jag hantera dem på Azure Portal?

Innan du öppnar Azure AD B2C på den vänstra menyn i Azure Portal måste du växla till den katalog som du vill hantera. Växla kataloger genom att klicka på din identitet längst upp till höger i Azure Portal och välj sedan en katalog i list rutan som visas.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hur gör jag för att anpassa e-postbekräftelser (innehållet och fältet från:) som skickats av Azure AD B2C?

Du kan använda [funktionen företags anpassning](../active-directory/fundamentals/customize-branding.md) för att anpassa innehållet i e-postbekräftelser. Mer specifikt kan dessa två element i e-postmeddelandet anpassas:

* **Banderoll-logo typ**: visas längst ned till höger.
* **Bakgrunds färg**: visas längst upp.

    ![Skärm bild av en anpassad verifierings-e-post](./media/faq/company-branded-verification-email.png)

E-postsignaturen innehåller Azure AD B2C klientens namn som du angav när du först skapade Azure AD B2C klienten. Du kan ändra namnet med hjälp av följande anvisningar:

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör.
1. Öppna **Azure Active Directory**-bladet.
1. Klicka på fliken **Egenskaper** .
1. Ändra fältet **namn** .
1. Klicka på **Spara** längst upp på sidan.

Det finns för närvarande inget sätt att ändra fältet från: i e-postmeddelandet.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hur kan jag migrera mina befintliga användar namn, lösen ord och profiler från databasen till Azure AD B2C?

Du kan använda Microsoft Graph-API: et för att skriva migrerings verktyget. Mer information finns i [guiden](user-migration.md) för användarmigrering.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Vilket användar flöde för lösen ord används för lokala konton i Azure AD B2C?

Lösen ordet för det Azure AD B2C användar flödet för lokala konton baseras på principen för Azure AD. Azure AD B2C's-registrering, registrering eller inloggning och användar flöden för återställning av lösen ord använder sig av "stark" lösen ords säkerhet och upphör aldrig att gälla lösen ord. Mer information finns i [lösen ords principer och begränsningar i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

Information om konto utelåsning och lösen ord finns i [Hantera hot mot resurser och data i Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan jag använda Azure AD Connect för att migrera konsument identiteter som lagras på lokala Active Directory till Azure AD B2C?

Nej, Azure AD Connect har inte utformats för att fungera med Azure AD B2C. Överväg att använda [Microsoft Graph-API: et](manage-user-accounts-graph-api.md) för användarmigrering. Mer information finns i [guiden](user-migration.md) för användarmigrering.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kan min app öppna Azure AD B2C sidor i en iFrame?

Nej, av säkerhets skäl kan Azure AD B2C sidor inte öppnas i en iFrame. Tjänsten kommunicerar med webbläsaren för att förhindra iFrames. Säkerhets communityn i allmänhet och OAUTH2-specifikationen rekommenderar att du använder iFrames för identitets upplevelser på grund av risken för klicknings uttag.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Fungerar Azure AD B2C med CRM-system som Microsoft Dynamics?

Integrering med Microsoft Dynamics 365-portalen är tillgänglig. Se [Konfigurera Dynamics 365-portalen för att använda Azure AD B2C för autentisering](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Fungerar Azure AD B2C med SharePoint lokalt 2016 eller tidigare?

Azure AD B2C är inte avsett för det externa SharePoint-scenariot för partner delning; Se [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) i stället.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Ska jag använda Azure AD B2C eller B2B för att hantera externa identiteter?

Läs [jämföra B2B-samarbete och B2C i Azure AD](../active-directory/b2b/compare-with-b2c.md) för att lära dig mer om hur du använder lämpliga funktioner i dina externa identitets scenarier.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Vilka rapporterings-och gransknings funktioner tillhandahåller Azure AD B2C? Är de samma som i Azure AD Premium?

Nej, Azure AD B2C har inte stöd för samma uppsättning rapporter som Azure AD Premium. Det finns dock många commonalities:

* **Inloggnings rapporter** innehåller en post för varje inloggning med reducerad information.
* **Gransknings rapporter** omfattar både admin-aktivitet och program aktivitet.
* **Användnings rapporterna** innehåller antalet användare, antal inloggningar och volymen MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan jag lokalisera användar gränssnittet för sidor som hanteras av Azure AD B2C? Vilka språk stöds?

Ja, se [språk anpassning](user-flow-language-customization.md). Vi tillhandahåller översättningar för 36-språk och du kan åsidosätta valfri sträng för att passa dina behov.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Kan jag använda mina egna URL: er på mina registrerings-och inloggnings sidor som hanteras av Azure AD B2C? Kan jag till exempel ändra URL: en från contoso.b2clogin.com till login.contoso.com?

Inte för tillfället. Den här funktionen finns i vår översikt. Att verifiera din domän på fliken **domäner** i Azure Portal att inte uppnå det här målet. Men med b2clogin.com erbjuder vi en [neutral toppnivå domän](b2clogin.md)och därför kan det externa utseendet implementeras utan omnämnande av Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hur gör jag för att ta bort Azure AD B2C klienten?

Följ dessa steg om du vill ta bort din Azure AD B2C-klient.

Du kan använda vår nya enhetlig **Appregistreringar** upplevelse eller äldre  **program (äldre)** . [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregtraining)

#### <a name="app-registrations"></a>[Appregistreringar](#tab/app-reg-ga/)

1. Logga in på [Azure Portal](https://portal.azure.com/) som *prenumerations administratör*. Använd samma arbets-eller skol konto eller samma Microsoft-konto som du använde för att registrera dig för Azure.
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Ta bort alla **användar flöden (principer)** i Azure AD B2C klient organisationen.
1. Välj **Appregistreringar**och välj sedan fliken **alla program** .
1. Ta bort alla program som du har registrerat.
1. Ta bort **B2C-Extensions-appen**.
1. Under **Hantera** väljer du **Användare**.
1. Välj varje användare i tur (exkludera den *prenumerations administratörs* användare som du för närvarande är inloggad som). Välj **ta bort** längst ned på sidan och välj **Ja** när du uppmanas till det.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Under **Hantera**väljer du **användar inställningar**.
1. Om det är tillgängligt väljer du **Nej**under **LinkedIn-konto anslutningar**och väljer sedan **Spara**.
1. Under **Hantera**, väljer du **Egenskaper**
1. Under **Åtkomsthantering för Azure-resurser** väljer du **Ja** och väljer sedan **Spara**.
1. Logga ut från Azure Portal och logga sedan in igen för att uppdatera åtkomsten.
1. Välj **Azure Active Directory** på menyn till vänster.
1. På sidan **Översikt** väljer du **ta bort klient organisation**. Slutför processen genom att följa anvisningarna på skärmen.

#### <a name="applications-legacy"></a>[Program (bakåtkompatibelt)](#tab/applications-legacy/)

1. Logga in på [Azure Portal](https://portal.azure.com/) som *prenumerations administratör*. Använd samma arbets-eller skol konto eller samma Microsoft-konto som du använde för att registrera dig för Azure.
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Ta bort alla **användar flöden (principer)** i Azure AD B2C klient organisationen.
1. Ta bort alla **program (äldre)** som du registrerade i Azure AD B2C-klienten.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Under **Hantera** väljer du **Användare**.
1. Välj varje användare i tur (exkludera den *prenumerations administratörs* användare som du för närvarande är inloggad som). Välj **ta bort** längst ned på sidan och välj **Ja** när du uppmanas till det.
1. Välj **Appregistreringar** under **Hantera**.
1. Välj **Visa alla program**
1. Välj programmet med namnet **B2C-Extensions-app**, Välj **ta bort**och välj sedan **Ja** när du uppmanas att göra det.
1. Under **Hantera**väljer du **användar inställningar**.
1. Om det är tillgängligt väljer du **Nej**under **LinkedIn-konto anslutningar**och väljer sedan **Spara**.
1. Under **Hantera**, väljer du **Egenskaper**
1. Under **Åtkomsthantering för Azure-resurser** väljer du **Ja** och väljer sedan **Spara**.
1. Logga ut från Azure Portal och logga sedan in igen för att uppdatera åtkomsten.
1. Välj **Azure Active Directory** på menyn till vänster.
1. På sidan **Översikt** väljer du **ta bort katalog**. Slutför processen genom att följa anvisningarna på skärmen.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan jag få Azure AD B2C som en del av Enterprise Mobility Suite?

Nej, Azure AD B2C är en Azure-tjänst enligt principen betala per användning och är inte en del av Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hur gör jag för att rapportera problem med Azure AD B2C?

Se [fil support begär Anden för Azure Active Directory B2C](support-options.md).
