---
title: 'Självstudie: Azure Active Directory integrering med Help Scout | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Help Scout.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 16783d344c6a0dedec4e24d49150e0b4b2edbe3e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548157"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Självstudie: Azure Active Directory integrering med Help Scout

I den här självstudien lär du dig att integrera Help Scout med Azure Active Directory (AD Azure).
Integreringen av Help Scout med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Help Scout.
* Du kan göra så att dina användare loggas in automatiskt på Help Scout (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Help Scout behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Help Scout-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Help Scout har stöd för **SP- och IDP**-initierad enkel inloggning
* Help Scout har stöd för **just-in-time**-användaretablering

## <a name="adding-help-scout-from-the-gallery"></a>Lägga till att Help Scout från galleriet

För att konfigurera integreringen av Help Scout i Azure AD måste du lägga till Help Scout från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Help Scout** i sökrutan.
1. Välj **Hjälp Scout** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med hjälp av en test användare som heter **B. Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Help Scout upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Help Scout behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera hjälp Scout SSO](#configure-help-scout-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa Help Scout test User](#create-help-scout-test-user)** -om du vill ha en motsvarighet till B. Simon i Help Scout som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Help Scout:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Help Scout** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

1. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Help Scout-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. **Identifierare** är **Audience URI (Service Provider Entity ID)** (URI för målgrupp (Entitets-ID för serviceprovider)) från Help Scout. Den börjar med `urn:`

    b. **Svars-URL** är den **Post-back URL (Assertion Consumer Service URL)** (Återskickande-URL (URL för Assertion Consumer Service)) från Help Scout. Den börjar med `https://` 

    > [!NOTE]
    > Värdena i dessa URL:er är bara exempel. Du behöver uppdatera dessa värden från faktisk svars-URL och identifierare. Du får dessa värden från fliken **Enkel inloggning** under avsnittet Autentisering, som beskrivs senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Help Scout-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du en URL: `https://secure.helpscout.net/members/login/`

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Help Scout** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Syftet med det här avsnittet är att skapa en test användare i Azure Portal som kallas B. Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **B. Simon**.
  
    b. I fältet **användar namn** skriver du **B. Simon \@ yourcompanydomain. extension**  
    Till exempel B.Simon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till hjälp Scout.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Help Scout**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Help Scout**.

    ![Länken för Help Scout i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-help-scout-sso"></a>Konfigurera hjälp Scout SSO

1. Om du vill automatisera konfigurationen i hjälpen Scout måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera hjälp Scout** så att du kommer till hjälp Scout-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på hjälp Scout. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera hjälp Scout manuellt öppnar du ett nytt webbläsarfönster och loggar in på företags webbplatsen för Help Scout som administratör och utför följande steg:

1. Klicka på **Hantera** från den översta menyn och välj sedan **Företag** från den nedrullningsbara menyn.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings1.png)

1. Välj **Autentisering** från det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings2.png)

1. Då öppnas avsnittet SAML-inställningar. Utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings3.png)

    a. Kopiera värdet för **Post-back URL (Assertion Consumer Service URL)** (Återskickande-URL (URL för Assertion Consumer Service)) och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Kopiera värdet för **Audience URI (Service Provider Entity ID)** (URI för målgrupp (Entitets-ID för serviceprovider)) och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Växla på **Aktivera SAML** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/helpscout-tutorial/settings4.png)

    a. I text rutan för **enkel inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. Klicka på **Ladda upp certifikat** för att ladda upp det **certifikat (Base64)** som laddats ned från Azure-portalen.

    c. Ange din organisations e-postdomän, t.ex. `contoso.com`, i textrutan **Email Domains** (E-postdomäner). Du kan avgränsa flera domäner med ett kommatecken. Användare eller administratörer för Help Scout som går in i den specifika domänen på [inloggningssidan för Help Scout](https://secure.helpscout.net/members/login/) dirigeras till Identitetsprovider för att autentisera med sina autentiseringsuppgifter.

    d. Slutligen kan du växla på **Force SAML Sign-on** (Framtvinga SAML-inloggning) om du vill att användare endast ska logga in på Help Scout med den här metoden. Om du ändå vill låta alternativet att logga in med deras Help Scout-autentiseringsuppgifter vara kvar kan du lämna detta avstängt. Även om det här aktiveras kan en Kontoägare alltid logga in på Help Scout med sitt kontolösenord.

    e. Klicka på **Spara**.

### <a name="create-help-scout-test-user"></a>Skapa Help Scout-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i hjälpen Scout. Help Scout har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Help Scout skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Help Scout-panelen i åtkomstpanelen bör du automatiskt loggas in på Help Scout som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Försök använda Scout med Azure AD](https://aad.portal.azure.com/)