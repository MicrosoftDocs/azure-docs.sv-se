---
title: 'Självstudie: Azure Active Directory integration med Rensa granskning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Clear Review.
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
ms.openlocfilehash: 06c4945c6fee82b3ce622304351c3ad6d9ae282d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544714"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Självstudie: Azure Active Directory integrering med tydlig granskning

I den här självstudien lär du dig att integrera Clear Review med Azure Active Directory (AD Azure).
När du integrerar Clear Review med Azure AD får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till Clear Review.
* Du kan låta dina användare automatiskt loggas in på Clear Review (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Clear Review behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Clear Review-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Clear Review har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-clear-review-from-the-gallery"></a>Lägga till Clear Review från galleriet

För att konfigurera integrering av Clear Review i Azure AD måste du lägga till Clear Review från galleriet i din lista över hanterade SaaS-appar.

**För att lägga till Clear Review från galleriet utför du följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Clear Review**, väljer **Clear Review** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Clear Review i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Clear Review baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Clear Review upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Clear Review måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Clear Review](#configure-clear-review-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Clear Review-testanvändare](#create-clear-review-test-user)** – för att ha en motsvarighet för Britta Simon i Clear Review som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Clear Review:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Clear Review** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med Clear Review-domäner och -URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<customer name>.clearreview.com/sso/acs/`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Clear Review-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<customer name>.clearreview.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för Clear Review-klienten](https://clearreview.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Rensa gransknings programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Rensa gransknings programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

7. Utför följande steg i dialog rutan **& anspråk** för användarattribut:

    a. Klicka på **ikonen Redigera** till höger om **namn identifierarens värde**.

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. Välj attributvärdet **User. mail** för raden i listan **källattribut** .

    c. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Clear Review** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-clear-review-single-sign-on"></a>Konfigurera enkel inloggning för Clear Review

1. För att konfigurera enkel inloggning på sidan **Clear Review** öppnar du **Clear Review**-portalen med administratörsautentiseringsuppgifter.

2. Välj **Administratör** i det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning – knappen Spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. I avsnittet **integrering** längst ned på sidan klickar du på knappen **ändra** till höger om **Inställningar för enkel inloggning**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Utför följande steg på sidan **Inställningar för enkel inloggning**

    ![Konfigurera enkel inloggning – knappen Spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    b. I textrutan **SAML-slutpunkt** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.  

    c. I textrutan **SLO Endpoint** (SLO-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.  

    d. Öppna det nedladdade certifikatet i anteckningar och klistra in innehållet i textrutan **X.509-certifikat**.   

    e. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Clear Review.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Clear Review**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Clear Review**.

    ![Länken Clear Review i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-clear-review-test-user"></a>Skapa Clear Review-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Clear Review. Kontakta [Clear Review-supportteamet](https://clearreview.com/contact/) för att lägga till användare i Clear Review-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Clear Review-panelen i åtkomstpanelen så bör du automatiskt loggas in på Clear Review som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

