---
title: 'Självstudie: Azure Active Directory integrering med Ziflow | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ziflow.
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
ms.openlocfilehash: 9a9e2298b6707304df96a2e954015459534abfa9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546091"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Självstudie: Azure Active Directory integrering med Ziflow

I den här självstudien får du lära dig hur du integrerar Ziflow med Azure Active Directory (Azure AD).
Genom att integrera Ziflow med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Ziflow.
* Du kan göra det möjligt för användarna att logga in automatiskt till Ziflow (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Ziflow behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Ziflow-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Ziflow stöder **SP** -INITIERAd SSO

## <a name="adding-ziflow-from-the-gallery"></a>Lägga till Ziflow från galleriet

Om du vill konfigurera integreringen av Ziflow i Azure AD måste du lägga till Ziflow från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Ziflow från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Ziflow**, väljer **Ziflow** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Ziflow i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Ziflow baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Ziflow upprättas.

Om du vill konfigurera och testa enkel inloggning med Ziflow i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Ziflow-enkel inloggning](#configure-ziflow-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Ziflow test User](#create-ziflow-test-user)** – om du vill ha en motsvarighet till Britta Simon i Ziflow som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Ziflow i Azure AD:

1. Välj **enkel inloggning**på sidan **Ziflow** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Ziflow-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Föregående värden är inte verkliga. Du kommer att uppdatera det unika ID-värdet i identifieraren och logga in URL med faktiskt värde, vilket beskrivs senare i självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Ziflow** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ziflow-single-sign-on"></a>Konfigurera Ziflow enkel inloggning

1. Logga in på Ziflow som säkerhets administratör i ett annat webbläsarfönster.

2. Klicka på avatar i det övre högra hörnet och klicka sedan på **Hantera konto**.

    ![Hantera Ziflow-konfiguration](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Klicka på **enkel inloggning**längst upp till vänster.

    ![Ziflow konfigurations tecken](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. På sidan **Enkel inloggning** utför du följande steg:

    ![Ziflow konfiguration, enkel](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Välj **typ** som **SAML 2.0**.

    b. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. Ladda upp det bas-64-kodade certifikatet som du har laddat ned från Azure Portal till **X509-signeringscertifikat**.

    d. I text rutan **Logga ut URL** klistrar du in värdet för **URL för utloggning**, som du har kopierat från Azure Portal.

    e. I avsnittet **konfigurations inställningar för din Identifier-Provider** kopierar du det markerade unika ID-värdet och lägger till det med identifieraren och inloggnings-URL: en i den **grundläggande SAML-konfigurationen** på Azure Portal.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Ziflow.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Ziflow**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Ziflow**.

    ![Ziflow-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ziflow-test-user"></a>Skapa Ziflow test användare

Om du vill att Azure AD-användare ska kunna logga in på Ziflow måste de tillhandahållas i Ziflow. I Ziflow är etableringen en manuell uppgift.

Gör följande för att etablera ett användarkonto:

1. Logga in på Ziflow som säkerhets administratör.

2. Navigera till **personer** överst.

    ![Ziflow konfigurations personer](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klicka på **Lägg till** och sedan på **Lägg till användare**.

    ![Ziflow konfiguration lägga till användare](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Utför följande steg på popup-menyn **Lägg till en användare** :

    ![Ziflow konfiguration lägga till användare](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. I textrutan **E-post** anger du användarens e-postadress som brittasimon@contoso.com.

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. Britta.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som Simon.

    d. Välj din Ziflow-roll.

    e. Klicka på **Lägg till 1 användare**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Ziflow på åtkomst panelen, bör du loggas in automatiskt på den Ziflow som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

