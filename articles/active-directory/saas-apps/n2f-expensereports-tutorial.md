---
title: 'Självstudie: Azure Active Directory integrering med N2F-utgifts rapporter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och N2F-utgifts rapporter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 6036ff86c430422556ed4f7e1bc80fe122ed0a30
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552576"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Självstudie: Azure Active Directory integrering med N2F-utgifts rapporter

I den här självstudien får du lära dig att integrera N2F-utgifts rapporter med Azure Active Directory (Azure AD).
Genom att integrera N2F med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till N2F-utgifts rapporter.
* Du kan göra det möjligt för användarna att logga in automatiskt till N2F (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med N2F utgifts rapporter behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* N2F – utgifts rapporter för enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* N2F-utgifts rapporter stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Lägga till N2F-utgifts rapporter från galleriet

Om du vill konfigurera integreringen av N2F-utgifts rapporter i Azure AD måste du lägga till N2F-utgifts rapporter från galleriet i listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till N2F i galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **N2F-utgifts rapporter**, väljer **N2F-utgifts rapporter** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![N2F – utgifts rapporter i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med N2F-utgifts rapporter som baseras på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i N2F-utgifts rapporter etableras.

Om du vill konfigurera och testa enkel inloggning med N2F-utgifts rapporter i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera N2F – utgifts rapporter enkel inloggning](#configure-n2f---expense-reports-single-sign-on)** – konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa N2F-utgifts rapporter testa användare](#create-n2f---expense-reports-test-user)** – för att få en motsvarighet till Britta Simon i N2F utgifts rapporter som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med N2F-utgifts rapporter:

1. På sidan [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **N2F-utgifts rapporter** för program integrering.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, behöver användaren inte utföra några steg eftersom appen redan är Förintegrerad med Azure.

    ![N2F-utgifts rapporter domän och URL-information för enkel inloggning](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![N2F-utgifts rapporter domän och URL-information för enkel inloggning](common/metadata-upload-additional-signon.png)

    Skriv en URL i text rutan **inloggnings-URL** :  `https://www.n2f.com/app/`

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

7. I avsnittet **Konfigurera principer för att skapa principer** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurera N2F – utgifts rapporter enkel inloggning

1. I ett annat webbläsarfönster loggar du in på företags platsen för N2F-utgifter som administratör.

2. Klicka på **Inställningar** och välj sedan **Avancerade inställningar** i list rutan.

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj fliken **konto inställningar** .

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/configure2.png)

4. Välj **autentisering** och välj sedan **+ Lägg till en autentiseringsmetod** -flik.

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/configure3.png)

5. Välj **SAML Microsoft Office 365** som autentiseringsmetod.

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/configure4.png)

6. Utför följande steg i avsnittet **autentiseringsmetod** :

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/configure5.png)

    a. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    b. I text rutan **metadata-URL** klistrar du in URL-värdet för **app Federation-Metadata** , som du har kopierat från Azure Portal.

    c. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till N2F-utgifts rapporter.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **N2F-utgifts rapporter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **N2F-utgifts rapporter**.

    ![Länken N2F-utgifts rapporter i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-n2f---expense-reports-test-user"></a>Skapa N2F-utgifts rapporter test användare

Om du vill att Azure AD-användare ska kunna logga in på N2F-utgifts rapporter måste de tillhandahållas i N2F-utgifts rapporter. När det gäller N2F-utgifts rapporter är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din företags webbplats för N2F-utgifter som administratör.

2. Klicka på **Inställningar** och välj sedan **Avancerade inställningar** i list rutan.

    ![N2F – utgifter Lägg till användare](./media/n2f-expensereports-tutorial/configure1.png)

3. Välj fliken **användare** från den vänstra navigerings panelen.

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/user1.png)

4. Välj fliken **ny användare** .

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/user2.png)

5. Utför följande steg i avsnittet **användare** :

    ![N2F – konfiguration av utgifts rapporter](./media/n2f-expensereports-tutorial/user3.png)

    a. I text rutan **e-postadress** anger du e-postadressen till användaren, t. ex. **brittasimon \@ contoso.com**.

    b. I text rutan för det **första namnet** anger du det första namnet på användaren som **Britta**.

    c. I textrutan **Namn** anger du namnet på användaren: **BrittaSimon**.

    d. Välj **roll, direkt chef (N + 1)** och **Division** enligt organisationens krav.

    e. Klicka på **validera och skicka inbjudan**.

    > [!NOTE]
    > Om du har problem med att lägga till användaren kan du kontakta [support teamet för N2F-utgifts rapporter](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen N2F-utgifts rapporter i åtkomst panelen, bör du loggas in automatiskt på de N2F-utgifts rapporter som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

