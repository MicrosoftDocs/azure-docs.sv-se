---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för BitBucket med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Bitbucket by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: fd35b1d0dd080164b015c8210e12fd404bf0a5b3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547298"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för BitBucket med hjälp av resolution GmbH

I den här självstudien lär du dig att integrera SAML SSO for Bitbucket by resolution GmbH med Azure Active Directory (Azure AD).
Integreringen av SAML SSO for Bitbucket by resolution GmbH med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till SAML SSO for Bitbucket by resolution GmbH.
* Du kan göra så att dina användare automatiskt loggas in på SAML SSO for Bitbucket by resolution GmbH (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAML SSO for Bitbucket by resolution GmbH behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO for Bitbucket by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO for Bitbucket by resolution GmbH stöder **SP- och IDP**-initierad enkel inloggning
* SAML SSO for Bitbucket by resolution GmbH stöder **just in time**-initierad enkel inloggning


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Lägga till SAML SSO for Bitbucket by resolution GmbH från galleriet

För att konfigurera integreringen av SAML SSO for Bitbucket by resolution GmbH med Azure AD måste du lägga till SAML SSO for Bitbucket by resolution GmbH från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAML SSO for Bitbucket by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAML SSO for Bitbucket by resolution GmbH**, väljer **SAML SSO for Bitbucket by resolution GmbH** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![SAML SSO for Bitbucket by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SAML SSO for Bitbucket by resolution GmbH baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Bitbucket by resolution GmbH upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med SAML SSO for Bitbucket by resolution GmbH behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAML SSO for Bitbucket by resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAML SSO for Bitbucket by resolution GmbH-testanvändare](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** – för att ha en motsvarighet för Britta Simon i SAML SSO for Bitbucket by resolution GmbH som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med SAML SSO for Bitbucket by resolution GmbH:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **SAML SSO for Bitbucket by resolution GmbH**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP**-initierat läge:

    ![Information om enkel inloggning med Bitbucket by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Bitbucket by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Konfigurera enkel inloggning för SAML SSO for Bitbucket by resolution GmbH

1. Logga in på din SAML SSO for Bitbucket by resolution GmbH-företagswebbplats som administratör.

2. Till höger i huvudverktygsfältet klickar du på **Inställningar**.

3. Gå till avsnittet KONTON och klicka på **SAML SingleSignOn** på menyraden.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. På **konfigurationssidan för SAML SIngleSignOn-plugin-programmet** klickar du på **Lägg till IdP**. 

    ![Lägg till IdP](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. På sidan **Välj din SAML-identitetsprovider** utför du följande steg:

    ![Identitetsprovidern](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Välj **IdP-typ** as **AZURE AD**.

    b. I textrutan **Namn** skriver du namnet.

    c. I textrutan **Beskrivning** skriver du beskrivningen.

    d. Klicka på **Nästa**.

6. På **konfigurationssidan för identitetsprovider** klickar du på **Nästa**.

    ![Identitetskonfigurationen](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  På sidan **Importera SAML IdP-metadata** klickar du på **Läs in fil** för att ladda upp den **METADATA XML**-fil som du har laddat ned från Azure-portalen.

    ![IdP-metadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Klicka på **Nästa**.

9. Klicka **Spara inställningar**.

    ![Spara](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till SAML SSO for Bitbucket by resolution GmbH.

1. I Azure-portalen väljer du **Företagsprogram** följt av **Alla program** och sedan **SAML SSO for Bitbucket by resolution GmbH**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **SAML SSO for Bitbucket by resolution GmbH**.

    ![SAML SSO for Bitbucket by resolution GmbH-länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Skapa SAML SSO for Bitbucket by resolution GmbH-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH stöder just-in-time-etablering och manuellt skapande av användare. Kontakta [kundsupporten för SAML SSO for Bitbucket by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) enligt dina behov.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAML SSO for Bitbucket by resolution GmbH-panelen i åtkomstpanelen bör du automatiskt loggas in på SAML SSO for Bitbucket by resolution GmbH som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

