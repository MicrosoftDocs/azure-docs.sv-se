---
title: 'Självstudie: Azure Active Directory integrering med RightScale | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RightScale.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013eadedc00dee23a09eff89147406cc14f017ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516597"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Självstudie: Azure Active Directory integrering med RightScale

I den här självstudien får du lära dig hur du integrerar RightScale med Azure Active Directory (Azure AD).
Genom att integrera RightScale med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till RightScale.
* Du kan göra det möjligt för användarna att logga in automatiskt till RightScale (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RightScale behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* RightScale-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* RightScale stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-rightscale-from-the-gallery"></a>Lägga till RightScale från galleriet

Om du vill konfigurera integreringen av RightScale i Azure AD måste du lägga till RightScale från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till RightScale från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **RightScale**, väljer **RightScale** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![RightScale i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med RightScale baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i RightScale upprättas.

Om du vill konfigurera och testa enkel inloggning med RightScale i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RightScale enkel inloggning](#configure-rightscale-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa RightScale test User](#create-rightscale-test-user)** – om du vill ha en motsvarighet till Britta Simon i RightScale som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med RightScale i Azure AD:

1. Välj **enkel inloggning** på sidan **RightScale** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Skärm bild som visar sidan grundläggande SAML-konfiguration.](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Skärm bild som visar ytterligare U R LS där du kan ange ett tecken på U R L.](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://login.rightscale.com/`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera RightScale** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rightscale-single-sign-on"></a>Konfigurera RightScale Single Sign-On

1. För att få SSO konfigurerat för ditt program måste du logga in på RightScale-klienten som administratör.

2. I menyn högst upp klickar du på fliken **Inställningar** och väljer **enkel inloggning**.

    ![Skärm bild som visar enstaka Sign-On som valts från inställningar.](./media/rightscale-tutorial/tutorial_rightscale_001.png)

3. Klicka på knappen **nytt** för att lägga till **dina SAML Identity providers**.

    ![Skärm bild som visar de nya knappar som är markerade för att lägga till en SAML Identity-Provider.](./media/rightscale-tutorial/tutorial_rightscale_002.png)

4. I text rutan med **visnings namn** skriver du in företagets namn.

    ![Skärm bild som visar var du anger ett visnings namn.](./media/rightscale-tutorial/tutorial_rightscale_003.png)

5. Välj **Tillåt RightScale-initierad SSO med ett identifierings tips** och ange **domän namnet** i text rutan nedan.

    ![Skärm bild som visar var du kan ange en inloggnings metod.](./media/rightscale-tutorial/tutorial_rightscale_004.png)

6. Klistra in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal till **SAML SSO-slutpunkten** i RightScale.

    ![Skärm bild som visar var du kan ange en SAML-S O-slutpunkt.](./media/rightscale-tutorial/tutorial_rightscale_006.png)

7. Klistra in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal till **SAML-EntityID** i RightScale.

    ![Skärm bild som visar var du kan ange en SAML-enhet I D.](./media/rightscale-tutorial/tutorial_rightscale_008.png)

8. Klicka på knappen **webbläsare** för att ladda upp det certifikat som du laddade ned från Azure Portal.

    ![Skärm bild som visar var du kan ange ditt SAML-signeringscertifikat.](./media/rightscale-tutorial/tutorial_rightscale_009.png)

9. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RightScale.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **RightScale**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **RightScale**.

    ![RightScale-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rightscale-test-user"></a>Skapa RightScale test användare

I det här avsnittet skapar du en användare som heter Britta Simon i RightScale. Arbeta med [RightScale-klientens support team](mailto:support@rightscale.com) för att lägga till användare i RightScale-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen RightScale på åtkomst panelen, bör du loggas in automatiskt på den RightScale som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)