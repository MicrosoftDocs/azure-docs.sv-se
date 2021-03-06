---
title: 'Självstudie: Azure Active Directory integrering med Agiloft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Agiloft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: cd926875eda5676da6c154a57b0ac18ca12d9b79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97671662"
---
# <a name="tutorial-azure-active-directory-integration-with-agiloft"></a>Självstudie: Azure Active Directory integrering med Agiloft

I den här självstudien lär du dig att integrera Agiloft med Azure Active Directory (AD Azure).
När du integrerar Agiloft med Azure AD får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till Agiloft.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Agiloft (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Agiloft behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Agiloft-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Agiloft stöder **SP- och IDP**-initierad enkel inloggning
* Agiloft stöder **just-in-time**-användaretablering

## <a name="adding-agiloft-from-the-gallery"></a>Lägga till Agiloft från galleriet

För att konfigurera integrering av Agiloft i Azure AD måste du lägga till Agiloft från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Agiloft från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Agiloft**, väljer **Agiloft** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Agiloft i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Agiloft baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Agiloft upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Agiloft måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Agiloft](#configure-agiloft-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Agiloft-testanvändare](#create-agiloft-test-user)** – för att ha en motsvarighet för Britta Simon i Agiloft som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Agiloft:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Agiloft-portalen** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Skärm bild som markerar text rutorna identifierare och svars-URL.](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    ```http
    https://<subdomain>.agiloft.com/<KB_NAME>
    ```

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ```http
    https://<subdomain>.agiloft.com:443/gui2/spsamlsso?project=<KB_NAME>
    ```

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Agiloft-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    ```http
    https://<subdomain>.agiloft.com/gui2/samlssologin.jsp?project=<KB_NAME>
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för Agiloft-klienten](https://www.agiloft.com/support-login.htm) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Agiloft** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-agiloft-single-sign-on"></a>Konfigurera enkel inloggning för Agiloft

1. Logga in på din Agiloft-företagswebbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Konfigurera** (i det vänstra fönstret) och välj sedan **Åtkomst**.

    ![Skärm bild som visar avsnittet åtkomst.](./media/agiloft-tutorial/setup1.png)

3. Klicka på knappen **Configure SAML 2.0 Single Sign-On** (Konfigurera enkel inloggning för SAML 2.0).

    ![Skärm bild som visar knappen Konfigurera SAML 2,0 Single Sign-On.](./media/agiloft-tutorial/setup2.png)

4. En guidedialogruta visas. I dialogrutan klickar du på **Identity Provider Details** (Information om identitetsprovider) och fyller i följande fält:  

    ![Agiloft-konfiguration](./media/agiloft-tutorial/setup4.png)

    a. I **IDP entitets-ID/Issuer-** text, klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    b. I text rutan för **inloggnings-URL för IDP** klistrar du in värdet för **inloggnings-URL**, som du har kopierat från Azure Portal.

    c. I text rutan för **IDP-utloggnings webb adress** klistrar du in värdet för **URL för utloggning** som du har kopierat från Azure Portal.

    d. Öppna ditt **bas-64-kodade certifikat** i anteckningar som hämtats från Azure Portal, kopiera innehållet i det till Urklipp och klistra in det i text rutan **IDP X. 509-certifikat** .

    e. Klicka på **Finish**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Agiloft.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Agiloft**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Agiloft**.

    ![Länken Agiloft i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-agiloft-test-user"></a>Skapa Agiloft-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Agiloft. Agiloft stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Agiloft skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Agiloft-panelen i åtkomstpanelen bör du automatiskt loggas in på Agiloft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
