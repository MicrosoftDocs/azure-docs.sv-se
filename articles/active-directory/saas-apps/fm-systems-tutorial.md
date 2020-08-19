---
title: 'Självstudie: Azure Active Directory integrering med FM: system | Microsoft Docs'
description: 'Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FM: system.'
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 2d6ad7daf68d9cd3ffbf4d489e3c170eef4dbe7f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554948"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Självstudie: Azure Active Directory integrering med FM: system

I den här självstudien får du lära dig att integrera FM: system med Azure Active Directory (Azure AD).
Att integrera FM: system med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till FM: system.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade på FM: system (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FM: system behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* FM: den aktiverade prenumerationen på system med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FM: system stöder **IDP** -INITIERAd SSO

## <a name="adding-fmsystems-from-the-gallery"></a>Lägga till FM: system från galleriet

Om du vill konfigurera en integrering av FM: system i Azure AD måste du lägga till FM: system från galleriet till din lista över hanterade SaaS-appar.

**Gör så här för att lägga till FM: system från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **fm: system**, väljer **fm: system** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![FM: system i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med FM: system som baseras på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera är det en länk relation mellan en Azure AD-användare och en relaterad användare i FM: system måste upprättas.

Om du vill konfigurera och testa enkel inloggning med FM: system i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera fm: system enkel inloggning](#configure-fmsystems-single-sign-on)** – konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa fm: system test User](#create-fmsystems-test-user)** – om du vill ha en motsvarighet till Britta Simon i FM: system som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med FM: system i Azure AD:

1. På sidan **fm: Systems** program integration på [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![FM: information om enkel inloggning för system domäner och URL: er](common/both-replyurl.png)

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`
    
    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Kontakta [fm: system klient support teamet](https://fmsystems.com/support-services/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera fm: system** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-fmsystems-single-sign-on"></a>Konfigurera FM: system enkel inloggning

Om du vill konfigurera enkel inloggning på **fm: Systems** sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [fm: system support team](https://fmsystems.com/support-services/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till FM: system.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **fm: system**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **fm: system**.

    ![Länken FM: Systems i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-fmsystems-test-user"></a>Skapa FM: system test användare

1. Logga in på din FM: Systems företags webbplats som administratör i ett webbläsarfönster.

2. Gå till **system administration \> hantera \> \> användar listan för säkerhets användare**.
   
    ![System administration](./media/fm-systems-tutorial/ic795905.png "System administration")

3. Klicka på **Skapa ny användare**.
   
    ![Skapa ny användare](./media/fm-systems-tutorial/ic795906.png "Skapa ny användare")

4. I avsnittet **Skapa användare** utför du följande steg:
   
    ![Skapa användare](./media/fm-systems-tutorial/ic795907.png "Skapa användare")
   
    a. Skriv **användar namnet**, **lösen ordet**, **Bekräfta lösen ordet**, **e-postadressen** och **anställnings-ID: t** för ett giltigt Azure Active Directory konto som du vill etablera i de relaterade text rutorna.
   
    b. Klicka på **Nästa**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen FM: system på åtkomst panelen bör du loggas in automatiskt på FM: system som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

