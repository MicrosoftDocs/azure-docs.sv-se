---
title: 'Självstudie: Azure Active Directory integrering med Palo-nätverk – bländare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo-nätverk – bländare.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: ebea5797c378197862628b46ed17ea077e3e5fa8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554064"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Självstudie: Azure Active Directory integrering med Palo-nätverk – bländare

I den här självstudien får du lära dig att integrera Palo-nätverk-bländare med Azure Active Directory (Azure AD).
Integrering av Palo-nätverk – bländare med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Palo-nätverk – bländare.
* Du kan göra det möjligt för användarna att logga in automatiskt till Palo-bländare (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Palo-nätverk-bländare behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Palo-nätverk – bländare med enkel inloggning aktive rad öppnings prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo-nätverk – bländare stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Lägga till Palo-nätverk – bländare från galleriet

Om du vill konfigurera integrationen av Palo-nätverk – bländare i Azure AD måste du lägga till Palo-nätverk – bländare från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Palo-nätverk – bländare från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Palo-nätverk-bländare**, väljer **Palo-nätverk-bländare** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Palo-nätverk – bländare i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Palo-nätverk – bländare som baseras på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Palo-nätverk-bländare upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Palo-nätverk-bländare måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Palo-nätverk – enkel inloggning med bländare](#configure-palo-alto-networks---aperture-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Palo-nätverk-bländare-test användare](#create-palo-alto-networks---aperture-test-user)** – om du vill ha en motsvarighet till Britta Simon i Paload nätverk-bländare som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Palo-nätverk – bländare:

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Palo-nätverk-bländare-** program integration och väljer **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Palo-nätverk-bländare-domän och URL enkel inloggning information](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Palo-nätverk-bländare-domän och URL enkel inloggning information](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Palo-nätverk – bländare-klientens support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Palo-nätverk – bländare** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurera Palo-nätverk – enkel inloggning på bländare

1. Logga in på Palo-nätverk-bländare som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar**på den översta meny raden.

    ![Fliken Inställningar](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigera till **program** avsnittet Klicka på **autentisering** formulär menyns vänstra sida.

    ![Fliken auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Utför följande steg på sidan **autentisering** :
    
    ![Fliken autentisering](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Markera kryss rutan **aktivera enkel inloggning (SSP-providers som stöds är okta, en inloggning)** från fältet **för enkel inloggning** .

    b. I text rutan **ID för identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    c. Klicka på **Välj fil** för att ladda upp det hämtade certifikatet från Azure AD i certifikat fältet för **identitets leverantören** .

    d. I text rutan **URL för identitetsprovider** , klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    e. Läs informationen om IdP från avsnittet om **bländare** och hämta certifikatet från **nyckel fältet bländare** .

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Palo-nätverk – bländare.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Palo-nätverk-bländare**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Palo-nätverk-bländare**.

    ![Länken Palo-nätverk – bländare i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Skapa Palo-nätverk – bländare-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Palo-nätverk-bländare. Arbeta med [Palo-nätverk – bländare-klientens support team](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) för att lägga till användarna i Palo-nätverkets bländare-plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Palo-nätverk – bländare i åtkomst panelen, bör du loggas in automatiskt till Palo-nätverkets bländare som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

