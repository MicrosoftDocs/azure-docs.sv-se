---
title: 'Självstudie: Azure Active Directory integrering med SAML 1,1 token Enabled LOB-app | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML 1.1-tokenaktiverad LOB App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 51c794d3e121b06f7eafc7f59f1fb8912e9d1f08
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543439"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Självstudie: Azure Active Directory integrering med SAML 1,1 token Enabled LOB-app

I den här självstudien lär du dig att integrera en SAML 1.1-token-aktiverad LOB App med Azure Active Directory (AD Azure).
Genom att integrera en SAML 1.1-token-aktiverad LOB App med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SAML 1.1-tokenaktiverad LOB App.
* Du kan göra så att dina användare automatiskt loggas in på en SAML 1.1-tokenaktiverad LOB App (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med en SAML 1.1-token aktiverad LOB App behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration på SAML 1.1-tokenaktiverad LOB App med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML 1.1-tokenaktiverad LOB App stöder **SP**-initierad SSO

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Lägga till SAML 1.1-tokenaktiverad LOB App från galleriet

Om du vill konfigurera integreringen av SAML 1.1-tokenaktiverad LOB App till Azure AD måste du lägga till SAML 1.1-tokenaktiverad LOB App från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAML 1.1-tokenaktiverad LOB App från galleriet utför du följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du ** SAML 1.1-tokenaktiverad LOB App**, väljer ** SAML 1.1-tokenaktiverad LOB App** från resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![SAML 1.1-tokenaktiverad LOB App i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med SAML 1.1-tokenaktiverad LOB App baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML 1.1-tokenaktiverad LOB App upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med en SAML 1.1-token-aktiverad LOB App behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för en SAML 1.1-token-aktiverad LOB App](#configure-saml-11-token-enabled-lob-app-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för SAML 1.1-tokenaktiverad LOB App](#create-saml-11-token-enabled-lob-app-test-user)**   – du har en motsvarighet till Britta Simon i SAML 1.1-tokenaktiverad LOB App som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

För att konfigurera Azure AD enkel inloggning med SAML 1.1-tokenaktiverad LOB App behöver du följande objekt:

1. I [Azure-portalen](https://portal.azure.com/), på programintegreringssidan **SAML 1.1-tokenaktiverad LOB App**, markerar du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om SAML 1.1-tokenaktiverad LOB App-domän och URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://your-app-url`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://your-app-url`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta supportteamet för SAML 1.1-tokenaktiverad LOB App-klienten för hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Set up SAML 1.1 Token enabled LOB App** (Konfigurera SAML 1.1-tokenaktiverad LOB App) kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Konfigurera SAML 1.1-tokenaktiverad LOB App med enkel inloggning aktiverat

För att kunna konfigurera enkel inloggning på **SAML 1.1-tokenaktiverad LOB App**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till SAML 1.1-tokenaktiverad LOB App-supporten. De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till SAML 1.1-tokenaktiverad LOB App.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **SAML 1.1-tokenaktiverad LOB App**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver och markerar du **SAML 1.1-tokenaktiverad LOB App**.

    ![SAML 1.1-tokenaktiverad LOB App i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Skapa SAML 1.1-tokenaktiverad LOB App-aktiverad testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i en SAML 1.1-token-aktiverad LOB App. Arbeta med SAML 1.1-tokenaktiverad LOB App-supportteamet för att lägga till användare i SAML 1.1-tokenaktiverad LOB App-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SAML 1.1-tokenaktiverad LOB App i åtkomstpanelen bör du loggas in automatiskt till den SAML 1.1-tokenaktiverad LOB App som du ställer in enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

