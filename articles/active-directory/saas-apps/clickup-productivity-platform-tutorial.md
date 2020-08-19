---
title: 'Självstudie: Azure Active Directory integration med ClickUp Productivity Platform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ClickUp Productivity Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 794a952ba719faa16e3584afaf8f2f292deb6c03
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551828"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Självstudie: Azure Active Directory integration med ClickUp Productivity Platform

I den här självstudien lär du dig att integrera ClickUp Productivity Platform med Azure Active Directory (AD Azure).
Integreringen av ClickUp Productivity Platform med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till ClickUp Productivity Platform.
* Du kan göra så att dina användare automatiskt loggas in på ClickUp Productivity Platform (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ClickUp Productivity Platform behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ClickUp Productivity Platform-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ClickUp Productivity Platform har stöd för **SP**-initierad enkel inloggning

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Lägga till ClickUp Productivity Platform från galleriet

För att konfigurera integreringen av ClickUp Productivity Platform i Azure AD behöver du lägga till ClickUp Productivity Platform från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ClickUp Productivity Platform från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ClickUp Productivity Platform**, väljer **ClickUp Productivity Platform** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ClickUp Productivity Platform i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med ClickUp Productivity Platform baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ClickUp Productivity Platform upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med ClickUp Productivity Platform slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ClickUp Productivity Platform-testanvändare](#create-clickup-productivity-platform-test-user)** – för att ha en motsvarighet för Britta Simon i ClickUp Productivity Platform som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med ClickUp Productivity Platform:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **ClickUp Productivity Platform** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med ClickUp Productivity Platform-domän och URL:er](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du en URL: `https://app.clickup.com/login/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren enligt beskrivningen senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurera enkel inloggning för ClickUp Productivity Platform

1. I ett annat webbläsarfönster loggar du in på din ClickUp Productivity Platform-klientorganisation som administratör.

2. Klicka på **användar profilen**och välj sedan **Inställningar**.

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Välj **Microsoft** under Single Sign-On (SSO) Provider (Provider för enkel inloggning).

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure2.png)

4. På sidan **Configure Microsoft Single Sign On** (Konfigurera enkel inloggning med Microsoft) utför du följande steg:

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Klicka på **Kopiera** för att kopiera värdet för entitets-ID och klistra in det i textrutan **Identifierare (entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    
    b. I textrutan **Azure Federation Metadata URL** (URL för Azure-federationsmetadata) klistrar du in det värde för URL för appfederationsmetadata som du har kopierat från Azure-portalen och klickar på **Spara**.

5. Slutför installationen genom att klicka på **Authenticate With Microsoft to complete setup** (Autentisera med Microsoft för att slutföra installationen) och autentisera med Microsoft-kontot.

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure4.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till ClickUp Productivity Platform.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **ClickUp Productivity Platform**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **ClickUp Productivity Platform**.

    ![Länken för ClickUp Productivity Platform i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-clickup-productivity-platform-test-user"></a>Skapa testanvändare för ClickUp Productivity Platform

1. I ett annat webbläsarfönster loggar du in på din ClickUp Productivity Platform-klientorganisation som administratör.

2. Klicka på **användar profilen**och välj sedan **personer**.
   
    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/configure0.png)

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/user1.png)

3. Ange användarens e-postadress i textrutan och klicka på **Bjud in**.

    ![Konfiguration av ClickUp Productivity Platform](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Användaren får meddelandet och måste godkänna inbjudan att aktivera kontot.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ClickUp Productivity Platform-panelen i åtkomstpanelen bör du automatiskt loggas in på ClickUp Productivity Platform som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

