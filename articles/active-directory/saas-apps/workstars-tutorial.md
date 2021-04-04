---
title: 'Självstudie: Azure Active Directory integrering med Workstars | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workstars.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 9f53072b106bedb8e49ba7f3728f39137f848a58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895026"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Självstudie: Azure Active Directory integrering med Workstars

I den här självstudien får du lära dig hur du integrerar Workstars med Azure Active Directory (Azure AD).
Genom att integrera Workstars med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Workstars.
* Du kan göra det möjligt för användarna att logga in automatiskt till Workstars (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Workstars behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Workstars-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Workstars stöder **IDP** INITIERAd SSO

## <a name="adding-workstars-from-the-gallery"></a>Lägga till Workstars från galleriet

Om du vill konfigurera integreringen av Workstars i Azure AD måste du lägga till Workstars från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workstars från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Workstars**, väljer **Workstars** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Workstars i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Workstars baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Workstars upprättas.

Om du vill konfigurera och testa enkel inloggning med Workstars i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Workstars enkel inloggning](#configure-workstars-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Workstars test User](#create-workstars-test-user)** – om du vill ha en motsvarighet till Britta Simon i Workstars som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Workstars i Azure AD:

1. Välj **enkel inloggning** på sidan **Workstars** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för Workstars-domän och URL: er](common/idp-intiated.png)

    a. Skriv en URL i text rutan **identifierare** : `https://workstars.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Workstars client support team](http://support.workstars.com/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Workstars** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workstars-single-sign-on"></a>Konfigurera Workstars Single Sign-On

1. Logga in på din Workstars företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar** i huvud verktygsfältet.

    ![Skärm bild som visar knappen Inställningar.](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Gå till **inloggnings**  >  **Inställningar**.

    ![Workstars inloggning](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Skärm bild som visar avsnittet enkel inloggning där du kan välja inställningar.](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. Utför följande steg på sidan **enkel inloggning (SAML) – inställningar** :
    
    ![Workstars SAML](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Skriv **Office 365** i text rutan **namn på identitetsprovider** .

    b. I text rutan för **entitets-ID för identitetsprovider** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. Kopiera innehållet i den hämtade certifikat filen i anteckningar och klistra sedan in den i text rutan **x509-certifikat** . 

    d. I textrutan **SAML SSO URL** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.
    
    e. I text rutan **utloggnings-URL** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal. 

    f. Välj **namn-ID** som **e-post (standard)**.

    ex. Klicka på **Bekräfta**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Workstars.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Workstars**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Workstars**.

    ![Workstars-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workstars-test-user"></a>Skapa Workstars test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Workstars. Arbeta med [Workstars support team](http://support.workstars.com) för att lägga till användare i Workstars-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Workstars på åtkomst panelen, bör du loggas in automatiskt på den Workstars som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)