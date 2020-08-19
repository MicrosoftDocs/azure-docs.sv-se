---
title: 'Självstudie: Azure Active Directory integrering med korn ALP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Korn Ferry ALP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 52de9ca9dbd4e8018224f15aa643d0756b0d29be
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549916"
---
# <a name="tutorial-azure-active-directory-integration-with-korn-ferry-alp"></a>Självstudie: Azure Active Directory integrering med korn färj-ALP

I den här självstudien lär du dig att integrera Korn Ferry ALP med Azure Active Directory (AD Azure).
När du integrerar Korn Ferry ALP med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Korn Ferry ALP.
* Du kan låta dina användare automatiskt loggas in på Korn Ferry ALP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Korn Ferry ALP behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Korn Ferry ALP-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Korn Ferry ALP stöder **SP**-initierad enkel inloggning

## <a name="adding-korn-ferry-alp-from-the-gallery"></a>Lägga till Korn Ferry ALP från galleriet

För att konfigurera integrering av Korn Ferry ALP i Azure AD behöver du lägga till Korn Ferry ALP från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Korn Ferry ALP från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Korn Ferry ALP**, väljer **Korn Ferry ALP** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Korn Ferry ALP i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Korn Ferry ALP baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Korn Ferry ALP upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Korn Ferry ALP måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Korn Ferry ALP](#configure-korn-ferry-alp-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Korn Ferry ALP-testanvändare](#create-korn-ferry-alp-test-user)** – för att ha en motsvarighet för Britta Simon i Korn Ferry ALP som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Korn Ferry ALP:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **Korn Ferry ALP**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Korn Ferry ALP-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    ```http
    https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>
    https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>
    https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>
    ```

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
    
    ```http
    https://intappextin01/portalweb/sso/client/audience?guid=<customerguid>
    https://qaassessment.kfnaqa.com/portalweb/sso/client/audience?guid=<customerguid>
    https://assessments.kornferry.com/portalweb/sso/client/audience?guid=<customerguid>
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för Korn Ferry ALP-klienten](mailto:noreply@kornferry.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-korn-ferry-alp-single-sign-on"></a>Konfigurera enkel inloggning för Korn Ferry ALP

Om du ska konfigurera enkel inloggning på **Korn Ferry ALP**-sidan måste du skicka **URL:en för appfederationsmetadata** till [Korn Ferry ALP-supportteamet](mailto:noreply@kornferry.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Korn Ferry ALP.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Korn Ferry ALP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Korn Ferry ALP**.

    ![Länken Korn Ferry ALP i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-korn-ferry-alp-test-user"></a>Skapa Korn Ferry ALP-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Korn Ferry ALP. Arbeta med [Korn Ferry ALP-supportteamet](mailto:noreply@kornferry.com) för att lägga till användare i Korn Ferry ALP-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Korn Ferry ALP-panelen på åtkomstpanelen bör du automatiskt loggas in i Korn Ferry ALP som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

