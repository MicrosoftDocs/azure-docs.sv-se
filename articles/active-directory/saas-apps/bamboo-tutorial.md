---
title: 'Självstudie: Azure Active Directory integrering med SAML SSO för Bamboo med hjälp av resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Bamboo med resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: ed587e8ee54aeb36b6790314e849f38b1bab1007
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549408"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Självstudie: Azure Active Directory integrering med SAML SSO för Bamboo med hjälp av resolution GmbH

I den här självstudien får du lära dig att integrera SAML SSO för Bamboo med hjälp av resolution GmbH med Azure Active Directory (Azure AD).
Att integrera SAML SSO för Bamboo med hjälp av resolution GmbH med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till SAML SSO för Bamboo med hjälp av resolution GmbH.
* Du kan göra det möjligt för användarna att logga in automatiskt till SAML SSO för Bamboo med hjälp av resolution GmbH (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAML SSO för Bamboo med hjälp av resolution GmbH behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SAML SSO för Bamboo med enkel inloggning aktive rad prenumeration i resolution GmbH

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för Bamboo av resolution GmbH stöder **SP-och IDP** -INITIERAd SSO
* SAML SSO för Bamboo efter resolution GmbH stöder **just-in-Time** User-etablering

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Lägga till SAML SSO för Bamboo med hjälp av resolution GmbH från galleriet

Om du vill konfigurera integreringen av SAML SSO för Bamboo med lösnings-GmbH i Azure AD, måste du lägga till SAML SSO för Bamboo via resolution GmbH från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAML SSO för Bamboo med hjälp av resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAML SSO för Bamboo efter resolution GmbH**, väljer **SAML SSO för Bamboo efter resolution GmbH** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SAML SSO för Bamboo med resolution GmbH i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med SAML SSO för Bamboo efter resolution GmbH baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SAML SSO för Bamboo av resolution GmbH upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med SAML SSO för Bamboo med hjälp av resolution GmbH måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Bamboo med enkel inloggning i resolution GmbH för](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** att konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAML SSO för Bamboo efter resolution GmbH test User](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** -om du vill ha en motsvarighet till Britta Simon i SAML SSO för Bamboo med resolution GmbH som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med SAML SSO för Bamboo med hjälp av resolution GmbH:

1. I [Azure Portal](https://portal.azure.com/)på sidan **SAML SSO för Bamboo efter lösning GmbH** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![SAML SSO för Bamboo efter resolution GmbH-domän och enkel inloggnings information för URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SAML SSO för Bamboo efter resolution GmbH-domän och enkel inloggnings information för URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SAML SSO för Bamboo efter resolution GmbH-klientens support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera SAML SSO för Bamboo efter resolution GmbH** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Konfigurera SAML SSO för Bamboo med enkel inloggning i resolution GmbH

1. Logga in på din SAML SSO för Bamboo av resolution GmbH företags webbplats som administratör.

1. Klicka på **inställnings**tillägg på höger sida av huvud verktygsfältet  >  **Add-ons**.

    ![Inställningarna](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Gå till avsnittet säkerhet, klicka på **SAML-SingleSignOn** på Meny raden.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. På **konfigurationssidan för SAML SIngleSignOn-plugin-programmet** klickar du på **Lägg till IdP**.

    ![Lägg till IdP](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. På sidan **Välj din SAML-identitetsprovider** utför du följande steg:

    ![Identitetsprovidern](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Välj **IdP-typ** as **AZURE AD**.

    b. I textrutan **Namn** skriver du namnet.

    c. I textrutan **Beskrivning** skriver du beskrivningen.

    d. Klicka på **Nästa**.

1. Klicka på **Nästa**på sidan **konfiguration av identitetsprovider** .

    ![Identitetskonfigurationen](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. På sidan **Importera SAML IdP-metadata** klickar du på **Läs in fil** för att ladda upp den **METADATA XML**-fil som du har laddat ned från Azure-portalen.

    ![IdP-metadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klicka på **Nästa**.

1. Klicka **Spara inställningar**.

    ![Spara](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAML SSO för Bamboo med hjälp av resolution GmbH.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SAML SSO för Bamboo med resolution GmbH**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SAML SSO för Bamboo efter resolution GmbH**.

    ![Bamboo efter resolution GmbH-länken för SAML SSO i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Skapa SAML SSO för Bamboo av test användaren för resolution GmbH

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i SAML SSO för Bamboo med hjälp av resolution GmbH. SAML SSO för Bamboo efter resolution GmbH stöder just-in-Time-etablering och även användare kan skapas manuellt, kontakta [SAML SSO för Bamboo med support från resolution GmbH klient support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) enligt ditt krav.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SAML SSO för Bamboo efter resolution GmbH på åtkomst panelen, bör du loggas in automatiskt på SAML SSO för Bamboo efter resolution GmbH som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
