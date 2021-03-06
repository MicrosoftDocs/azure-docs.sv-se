---
title: 'Självstudie: Azure Active Directory integrering med LCVista | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LCVista.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: c32e0da05b3d076cbb18c2f4d475f45bfb0ecd15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458769"
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Självstudie: Azure Active Directory integrering med LCVista

I den här självstudien får du lära dig hur du integrerar LCVista med Azure Active Directory (Azure AD).
Genom att integrera LCVista med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LCVista.
* Du kan göra det möjligt för användarna att logga in automatiskt till LCVista (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LCVista behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* LCVista-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LCVista stöder **SP** -INITIERAd SSO

## <a name="adding-lcvista-from-the-gallery"></a>Lägga till LCVista från galleriet

Om du vill konfigurera integreringen av LCVista i Azure AD måste du lägga till LCVista från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till LCVista från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **LCVista**, väljer **LCVista** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![LCVista i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med LCVista baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i LCVista upprättas.

Om du vill konfigurera och testa enkel inloggning med LCVista i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LCVista enkel inloggning](#configure-lcvista-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LCVista test User](#create-lcvista-test-user)** – om du vill ha en motsvarighet till Britta Simon i LCVista som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med LCVista i Azure AD:

1. Välj **enkel inloggning** på sidan **LCVista** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för LCVista-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.lcvista.com/rainier/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.lcvista.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [LCVista client support team](https://lcvista.com/contact) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera LCVista** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lcvista-single-sign-on"></a>Konfigurera LCVista Single Sign-On

1. Logga in på ditt LCVista-program som administratör.

2. I avsnittet **SAML-konfiguration** markerar du **Aktivera SAML-inloggning** och anger den information som anges i bilden nedan. 

    ![Konfigurera enkel inloggning](./media/lcvista-tutorial/tutorial_lcvista_config.png)

    a. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    b. I text rutan **URL** klistrar du in **inloggnings-URL** -värde, som du har kopierat från Azure Portal.

    c. Öppna XML-filen med metadata som du har laddat ned från Azure Portal till anteckningar, kopiera värdet **X509Certificate** och klistra in det i avsnittet **x509-certifikat** .

    d. Klistra in värdet i text rutan för det **första namnattributet** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. Klistra in värdet i text rutan för det **sista namnattributet** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Klistra in värdet i text rutan **email-attribut** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    ex. I text rutan **attribut för användar namn** klistrar du in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    e. Spara inställningarna genom att klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LCVista.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **LCVista**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LCVista**.

    ![LCVista-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lcvista-test-user"></a>Skapa LCVista test användare

I det här avsnittet skapar du en användare som heter Britta Simon i LCVista. Arbeta med [LCVista-klientens support team](https://lcvista.com/contact) för att lägga till användare i LCVista-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LCVista på åtkomst panelen, bör du loggas in automatiskt på den LCVista som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)