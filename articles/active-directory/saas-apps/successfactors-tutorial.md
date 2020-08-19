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
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 48524bec352d2fa9c169a1345e52ad4c789e59d3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552128"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SuccessFactors

I den här självstudien får du lära dig hur du integrerar SuccessFactors med Azure Active Directory (Azure AD). När du integrerar SuccessFactors med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SuccessFactors.
* Gör det möjligt för användarna att logga in automatiskt till SuccessFactors med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SuccessFactors för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SuccessFactors stöder **SP** -initierad SSO.
* När du har konfigurerat SuccessFactors kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>Lägga till SuccessFactors från galleriet

För att konfigurera integreringen av SuccessFactors till Azure AD behöver du lägga till SuccessFactors från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SuccessFactors** i sökrutan.
1. Välj **SuccessFactors** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurera och testa Azure AD SSO för SuccessFactors

Konfigurera och testa Azure AD SSO med SuccessFactors med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SuccessFactors.

Om du vill konfigurera och testa Azure AD SSO med SuccessFactors, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera SUCCESSFACTORS SSO](#configure-successfactors-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SuccessFactors test User](#create-successfactors-test-user)** -om du vill ha en motsvarighet till B. Simon i SuccessFactors som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **SuccessFactors** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 

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

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SuccessFactors** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
    1. I **Namn**-fältet skriver du `B.Simon`.  
    1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
    1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
    1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SuccessFactors.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **SuccessFactors**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-successfactors-sso"></a>Konfigurera SuccessFactors SSO

1. I ett annat webbläsarfönster loggar du in på din **SuccessFactors-administratörsportal** som administratör.

2. Gå till **Application Security** (Programsäkerhet) och sedan till **funktionen för enkel inloggning**.

3. Placera valfritt värde i **Reset Token** (Återställ token) och klicka på **Save Token** (Spara Token) för att aktivera enkel inloggning med SAML.

    ![Konfigurera enkel inloggning på appsidan][11]

    > [!NOTE]
    > Det här värdet används som på/av-växel. Om ett värde sparas är enkel inloggning med SAML PÅ. Om ett tomt värde sparas är enkel inloggning med SAML AV.

4. Gå till skärmbilden nedan och utför följande åtgärder:

    ![Konfigurera enkel inloggning på appsidan][12]
  
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

    ![Konfigurera enkel inloggning på appsidan][13]

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

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SuccessFactors-panelen i åtkomstpanelen bör du automatiskt loggas in på SuccessFactors som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SuccessFactors med Azure AD](https://aad.portal.azure.com)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du SuccessFactors med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
