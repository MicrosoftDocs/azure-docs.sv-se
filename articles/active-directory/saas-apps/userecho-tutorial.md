---
title: 'Självstudie: Azure Active Directory integrering med UserEcho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserEcho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 6708fcbe6f756a92984ee8aa48c181e1103f2046
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532899"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Självstudie: Azure Active Directory integrering med UserEcho

I den här självstudien får du lära dig hur du integrerar UserEcho med Azure Active Directory (Azure AD).
Genom att integrera UserEcho med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till UserEcho.
* Du kan göra det möjligt för användarna att logga in automatiskt till UserEcho (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med UserEcho behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* UserEcho-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UserEcho stöder **SP** -INITIERAd SSO

## <a name="adding-userecho-from-the-gallery"></a>Lägga till UserEcho från galleriet

Om du vill konfigurera integreringen av UserEcho i Azure AD måste du lägga till UserEcho från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till UserEcho från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **UserEcho**, väljer **UserEcho** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![UserEcho i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med UserEcho baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i UserEcho upprättas.

Om du vill konfigurera och testa enkel inloggning med UserEcho i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera UserEcho-enkel inloggning](#configure-userecho-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa UserEcho test User](#create-userecho-test-user)** – om du vill ha en motsvarighet till Britta Simon i UserEcho som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med UserEcho i Azure AD:

1. Välj **enkel inloggning**på sidan **UserEcho** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för UserEcho-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.userecho.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [UserEcho client support team](https://feedback.userecho.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera UserEcho** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-userecho-single-sign-on"></a>Konfigurera UserEcho enkel inloggning

1. Logga in på din UserEcho företags webbplats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på ditt användar namn för att expandera menyn och klicka sedan på **Konfigurera**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klicka på **integrationer**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klicka på **webbplats**och sedan på **enkel inloggning (SAML2)**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Utför följande steg på sidan **enkel inloggning (SAML)** :
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Som **SAML-aktiverad**väljer du **Ja**.
    
    b. Klistra in **inloggnings-URL**, som du har kopierat från Azure Portal till URL-text rutan för **SAML SSO** .
    
    c. Klistra in inloggnings- **URL**, som du har kopierat från Azure Portal till text rutan **utloggnings-URL** .
    
    d. Öppna det hämtade certifikatet i anteckningar, kopiera innehållet och klistra in det i rutan **X. 509-certifikat** .
    
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
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till UserEcho.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **UserEcho**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **UserEcho**.

    ![UserEcho-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-userecho-test-user"></a>Skapa UserEcho test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i UserEcho.

**Utför följande steg för att skapa en användare med namnet Britta Simon i UserEcho:**

1. Logga in på din UserEcho företags webbplats som administratör.

2. I verktygsfältet högst upp klickar du på ditt användar namn för att expandera menyn och klicka sedan på **Konfigurera**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klicka på **användare**, för att expandera avsnittet **användare** .
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klicka på **Användare**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klicka på **Bjud in en ny användare**.
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_12.png)

6. I dialog rutan **Bjud in en ny användare** utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Skriv namnet på användaren som Britta Simon i text rutan **namn** .
    
    b.  I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.
    
    c. Klicka på **Bjud in**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen UserEcho på åtkomst panelen, bör du loggas in automatiskt på den UserEcho som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

