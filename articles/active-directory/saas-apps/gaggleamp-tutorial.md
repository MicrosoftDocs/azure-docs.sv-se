---
title: 'Självstudie: Azure Active Directory integrering med GaggleAMP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GaggleAMP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 7293430968d82fbf9a5efd370f3e0a3ca85842a2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550936"
---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Självstudie: Azure Active Directory integrering med GaggleAMP

I den här självstudien lär du dig att integrera GaggleAMP med Azure Active Directory (AD Azure).
Integreringen av GaggleAMP med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till GaggleAMP.
* Du kan göra så att dina användare automatiskt loggas in på GaggleAMP (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med GaggleAMP behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* GaggleAMP-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GaggleAMP har stöd för **SP**- och **IDP**-initierad enkel inloggning

* GaggleAMP har stöd för **just-in-time**-användaretablering

## <a name="adding-gaggleamp-from-the-gallery"></a>Lägga till GaggleAMP från galleriet

För att konfigurera integreringen av GaggleAMP i Azure AD behöver du lägga till GaggleAMP från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till GaggleAMP från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **GaggleAMP**, väljer **GaggleAMP** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![GaggleAMP i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med GaggleAMP baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i GaggleAMP upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med GaggleAMP slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för GaggleAMP](#configure-gaggleamp-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa GaggleAMP-testanvändare](#create-gaggleamp-test-user)** – för att ha en motsvarighet för Britta Simon i GaggleAMP som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med GaggleAMP:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **GaggleAMP** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med GaggleAMP-domän och URL:er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://accounts.gaggleamp.com/auth/saml/callback`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://gaggleamp.com/i/<customerid>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för GaggleAMP](mailto:sales@gaggleamp.com) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera GaggleAMP** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-gaggleamp-single-sign-on"></a>Konfigurera enkel inloggning för GaggleAMP

1. I en annan webb läsar instans navigerar du till sidan med SAML SSO som skapats åt dig av Gaggle support team (till exempel: *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit* ).

2. På sidan **SAML SSO** (Enkel inloggning med SAML) utför du följande steg:  
   
    ![Enkel inloggning för GaggleAMP](./media/gaggleamp-tutorial/tutorial_gaggleamp_06.png)

    a. Välj **Annan** i listrutan **Identitetsprovider**.
    
    b. I text rutan för **identitets leverantörs utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.
    
    c. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.
    
    d. Öppna den nedladdade filen med **certifikat (Base64)** i Anteckningar, kopiera dess innehåll till Urklipp och klistra in det i textrutan **X.509 Certificate** (X.509-certifikat).
    
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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till GaggleAMP.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **GaggleAMP**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **GaggleAMP**.

    ![Länken för GaggleAMP i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-gaggleamp-test-user"></a>Skapa GaggleAMP-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i GaggleAMP. GaggleAMP har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i GaggleAMP skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på GaggleAMP-panelen i åtkomstpanelen bör du automatiskt loggas in på GaggleAMP som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

