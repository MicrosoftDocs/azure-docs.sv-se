---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med arbets yta på Facebook | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 3f66da38d3303b47c2a9b6cefeee19af6bf64ec1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725514"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med arbets ytan efter Facebook

I den här självstudien får du lära dig hur du integrerar arbets ytan i Facebook med Azure Active Directory (Azure AD). När du integrerar arbets ytan av Facebook med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till arbets platsen av Facebook.
* Gör det möjligt för användarna att logga in automatiskt på arbets platsen av Facebook med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på arbets plats av Facebook-enkel inloggning (SSO).

> [!NOTE]
> Facebook har två produkter, Workplace Standard (kostnadsfri) och Workplace Premium (betalprodukt). Alla Workplace Premium-klientorganisationer kan konfigurera integrering med SCIM och enkel inloggning utan att det påverkar kostnaden eller de licenser som behövs. Enkel inloggning och SCIM är inte tillgängliga i Workplace Standard-instanser.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Workplace by Facebook har stöd för **SP**-initierad enkel inloggning
* Workplace by Facebook har stöd för **just-in-time-etablering**
* Workplace by Facebook har stöd för **[automatisk användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)**
* Arbets platsen av Facebook Mobile Application kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.


## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Lägga till Workplace by Facebook från galleriet

För att konfigurera integrering av Workplace by Facebook med Azure AD behöver du lägga till Workplace by Facebook från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **arbets yta efter Facebook** i sökrutan.
1. Välj **arbets plats per Facebook** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Konfigurera och testa Azure AD SSO för arbets yta på Facebook

Konfigurera och testa Azure AD SSO med arbets ytan på Facebook med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på arbets ytan av Facebook.

Utför följande steg för att konfigurera och testa Azure AD SSO med arbets ytan på Facebook:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera arbets ytan efter Facebook SSO](#configure-workplace-by-facebook-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa arbets plats efter Facebook-testanvändare](#create-workplace-by-facebook-test-user)** – om du vill ha en motsvarighet till B. Simon på arbets ytan av Facebook som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **arbets plats av Facebook** -programintegration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. Skriv en URL med följande mönster i text rutan **inloggnings-URL** (som finns i arbets platsen som mottagarens URL): `https://.workplace.com/work/saml.php`

    b. Skriv en URL med följande mönster i text rutan **identifierare (enhets-ID)** (finns i arbets ytan som mål webb adress): `https://www.workplace.com/company/`

    c. Skriv en URL med följande mönster i text rutan **svars-URL** (hittades i arbets plats som intygs mottagar tjänst): `https://.workplace.com/work/saml.php`

    > [!NOTE]
    > De här värdena är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. På sidan autentisering på instrument panelen för företagets företags instrument panel finns de korrekta värdena för din arbets plats community, detta beskrivs senare i självstudien.

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På sidan **Konfigurera arbets plats efter Facebook** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets platsen av Facebook.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **arbets plats efter Facebook**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurera arbets yta efter Facebook SSO

1. Om du vill automatisera konfigurationen inom arbets ytan efter Facebook måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klickar du på **Konfigurera arbets plats av Facebook** för att dirigera dig till arbets platsen efter Facebook-program. Därifrån anger du de autentiseringsuppgifter som krävs för att logga in på arbets platsen på Facebook. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera arbets ytan på Facebook manuellt öppnar du ett nytt webbläsarfönster och loggar in på din arbets plats av Facebook företags webbplats som administratör och utför följande steg:

    > [!NOTE]
    > Som en del av SAML-autentiseringsprocessen kan Workplace by Facebook utnyttja frågesträngar på upp till 2,5 kB i storlek för att skicka parametrar till Azure AD.

1. På den vänstra navigerings panelen navigerar du **till**  >  fliken **säkerhetsautentisering** .

    ![Administrationspanel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Kontrol lera alternativet **enkel inloggning (SSO)** .
    
    b. Klicka på **+ Lägg till ny SSO-Provider**.
    > [!NOTE]
    > Se till att markera kryss rutan för lösen ords inloggning. Administratörer kan behöva det här alternativet för inloggning medan certifikat förnyelsen utförs för att sluta bli utelåst.

1. Under fliken **Autentisering** väljer du **Enkel inloggning** och utför följande steg:

    ![Fliken Autentisering](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. I **namnet på SSO-providern** anger du SSO-instansnamnet som Azureadsso.

    b. I textrutan **SAML URL** klistrar du in det värde för **Login URL** (Inloggnings-URL) som du har kopierat från Azure-portalen.

    c. I text rutan **URL för SAML-utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. Öppna ditt **base-64-kodade certifikat** som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet i Urklipp och klistra sedan in den i textrutan **SAML Certificate** (SAML-certifikat).

    e. Kopiera **mål webb adressen** för din instans och klistra in den i text rutan **identifierare (entitets-ID)** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    f. Kopiera **mottagarens URL** för din instans och klistra in den i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    ex. Kopiera **ACS-URL: en (Assertion Consumer service)** för din instans och klistra in den i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    h. Rulla ned till slutet av avsnittet och klicka på knappen **Test SSO** (Testa enkel inloggning). Då visas ett popup-fönster med Azure AD-inloggningssidan. Ange dina autentiseringsuppgifter som vanligt för att autentisera.

    **Fel sökning:** Se till att e-postadressen som returneras tillbaka från Azure AD är samma som det arbets plats konto som du är inloggad med.

    i. När testet är klart rullar du längst ned på sidan och klickar på knappen **Spara**.

    j. Alla användare som använder Workplace ser nu Azure AD-inloggningssidan för autentisering.

1. **Omdirigering av SAML-utloggning (valfritt)** -

    Du kan även välja att konfigurera en SAML-utloggnings-URL som kan användas för att peka på utloggningssidan för Azure AD. När den här inställningen aktiveras och konfigureras omdirigeras användarna inte längre till utloggningssidan för Workplace. I stället omdirigeras de till den URL som lades till i inställningen för SAML-utloggningsomdirigering.

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentiserings frekvens

Du kan konfigurera Workplace att fråga efter en SAML-kontroll varje dag, var tredje dag, varje vecka, varannan vecka, varje månad eller aldrig.

> [!NOTE]
> Minimivärdet för SAML-kontrollen i mobilprogram är inställt på en vecka.

Du kan också tvinga fram en SAML-återställning för alla användare med knappen: Kräv SAML-autentisering för alla användare nu.

### <a name="create-workplace-by-facebook-test-user"></a>Skapa Workplace by Facebook-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i arbets ytan av Facebook. Workplace by Facebook har stöd för just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd för dig i det här avsnittet. Om det inte finns någon användare i Workplace by Facebook skapas en ny när du försöker få åtkomst till Workplace by Facebook.

>[!Note]
>Om du behöver skapa en användare manuellt kan du kontakta [arbets ytan enligt Facebook-klientens support team](https://www.workplace.com/help/work/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till arbets platsen av Facebook-inloggnings-URL: en där du kan starta inloggnings flödet. 

* Gå till arbets platsen efter Facebook-inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen arbets yta efter Facebook i Mina appar omdirigeras den till arbets ytan efter Facebook-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testa SSO för arbets plats av Facebook (mobil)

1. Öppna arbets plats efter Facebook Mobile-program. På inloggnings sidan klickar du på **Logga** in.

    ![Inloggningen](./media/workplacebyfacebook-tutorial/test05.png)

2. Ange ditt företags-e-postmeddelande och klicka på **Fortsätt**.

    ![E-postmeddelandet](./media/workplacebyfacebook-tutorial/test02.png)

3. Klicka på **bara en gång**.

    ![En gång](./media/workplacebyfacebook-tutorial/test04.png)

4. Klicka på **Tillåt**.

    ![Alternativet Tillåt](./media/workplacebyfacebook-tutorial/test03.png)

5. När du har loggat in igen visas programmets start sida.    

    ![Start Sidan](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat arbets ytan av Facebook kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)