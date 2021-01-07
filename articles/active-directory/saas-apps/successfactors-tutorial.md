---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SuccessFactors | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SuccessFactors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: fba29f77d67c5b571253b842f865ae29a6ccfacd
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968389"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SuccessFactors

I den här självstudien får du lära dig hur du integrerar SuccessFactors med Azure Active Directory (Azure AD). När du integrerar SuccessFactors med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SuccessFactors.
* Gör det möjligt för användarna att logga in automatiskt till SuccessFactors med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SuccessFactors för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SuccessFactors stöder **SP** -initierad SSO.

## <a name="adding-successfactors-from-the-gallery"></a>Lägga till SuccessFactors från galleriet

För att konfigurera integreringen av SuccessFactors till Azure AD behöver du lägga till SuccessFactors från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SuccessFactors** i sökrutan.
1. Välj **SuccessFactors** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurera och testa Azure AD SSO för SuccessFactors

Konfigurera och testa Azure AD SSO med SuccessFactors med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SuccessFactors.

Utför följande steg för att konfigurera och testa Azure AD SSO med SuccessFactors:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera SUCCESSFACTORS SSO](#configure-successfactors-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa SuccessFactors test User](#create-successfactors-test-user)** -om du vill ha en motsvarighet till B. Simon i SuccessFactors som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **SuccessFactors** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med något av följande mönster:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. I text rutan **identifierare** skriver du en URL med något av följande mönster:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. I text rutan **svars-URL** skriver du en URL med något av följande mönster:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för SuccessFactors-klienten](https://www.sap.com/support.html).

4. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SuccessFactors** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SuccessFactors.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **SuccessFactors**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-successfactors-sso"></a>Konfigurera SuccessFactors SSO

1. I ett annat webbläsarfönster loggar du in på din **SuccessFactors-administratörsportal** som administratör.

2. Gå till **Application Security** (Programsäkerhet) och sedan till **funktionen för enkel inloggning**.

3. Placera valfritt värde i **Reset Token** (Återställ token) och klicka på **Save Token** (Spara Token) för att aktivera enkel inloggning med SAML.

    ![Skärm bild som visar fliken program säkerhet med funktioner för enkel inloggning som anropas där du kan ange en token.][11]

    > [!NOTE]
    > Det här värdet används som på/av-växel. Om ett värde sparas är enkel inloggning med SAML PÅ. Om ett tomt värde sparas är enkel inloggning med SAML AV.

4. Gå till skärmbilden nedan och utför följande åtgärder:

    ![Skärm bild som visar fönstret för SAML-baserade S O där du kan ange de värden som beskrivs.][12]
  
    a. Markera alternativknappen **SAML v2 SSO** (Enkel inloggning med SAML v2)
  
    b. Ange **SAML Asserting Party Name** (Namn på försäkrande part för SAML) (till exempel SAML-utfärdare plus företagsnamn).

    c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    d. Välj **Assertion** (Försäkran) för **Require Mandatory Signature** (Kräv obligatorisk signatur).

    e. Välj **Enabled** (Aktiverad) för **Enable SAML Flag** (Aktivera SAML-flagga).

    f. Välj **No** (Nej) för **Login Request Signature(SF Generated/SP/RP)** (Signatur för inloggningsbegäran (SF-genererad/SP/RP)).

    ex. Välj **Browser/Post Profile** (Webbläsar-/postprofil) för **SAML Profile** (SAML-profil).

    h. Välj **No** (Nej) för **Enforce Certificate Valid Period** (Tillämpa giltighetsperiod för certifikat).

    i. Kopiera innehållet i den nedladdade certifikatfilen från Azure-portalen och klistra in det i textrutan **SAML Verifying Certificate** (Verifieringscertifikat för SAML).

    > [!NOTE] 
    > Certifikatinnehållet måste ha starttaggar och sluttaggar för certifikat.

5. Gå till SAML-V2 och utför sedan följande steg:

    ![Skärm bild som visar fönstret SAML v2 S P initierat utloggning där du kan ange de värden som beskrivs.][13]

    a. Välj **Yes** (Ja) för **Support SP-initiated Global Logout** (Stöd SP-initierad global utloggning).

    b. I textrutan **Global Logout Service URL (LogoutRequest destination)** (Tjänst-URL för global utloggning (LogoutRequest-destination)) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. Välj **No** (Nej) för **Require sp must encrypt all NameID element** (Kräv att SP krypterar alla NameID-element).

    d. Välj **unspecified** (ospecificerad) för **NameID Format** (NameID-format).

    e. Välj **Ja** för **Enable sp initiated login (AuthnRequest)** (Aktivera SP-initierad inloggning (AuthnRequest)).

    f. I textrutan **Send request as Company-Wide issuer** (Skicka begäran som företagsomfattande utfärdare) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

6. Utför de här stegen om du göra användarnamnen för inloggning skiftlägeskänsliga.

    ![Konfigurera enkel inloggning][29]

    a. Gå till **Company Settings** (Företagsinställningar) (nästan längst ned).

    b. Markera kryssrutan nära **Enable Non-Case-Sensitive Username** (Aktivera ej skiftlägeskänsligt användarnamn).

    c. Klicka på **Spara**.

    > [!NOTE]
    > Om du försöker aktivera detta så kontrollerar systemet om det skapa ett duplicerat SAML-inloggningsnamn. Ett exempel kan vara om kunden har användarnamnen User1 och user1. Om skiftlägeskänslighet tas bort blir dessa namn dubbletter. Systemet visar ett felmeddelande och aktiverar inte funktionen. Kunden måste ändra något av användarnamnen så att de stavas olika.

### <a name="create-successfactors-test-user"></a>Skapa SuccessFactors-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på SuccessFactors måste de tillhandahållas i SuccessFactors. När det gäller SuccessFactors är etablering en manuell aktivitet.

För att skapa användare i SuccessFactors behöver du kontakta [SuccessFactors-supportteamet](https://www.sap.com/support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SuccessFactors-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till SuccessFactors-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SuccessFactors i Mina appar omdirigeras det till SuccessFactors-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SuccessFactors kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png