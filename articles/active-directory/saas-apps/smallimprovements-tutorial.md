---
title: 'Självstudie: Azure Active Directory-integrering med små förbättringar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och små förbättringar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: c659af83034a2406449e586723341e1ae535934f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525432"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Självstudie: Azure Active Directory integrering med små förbättringar

I den här självstudien får du lära dig hur du integrerar små förbättringar med Azure Active Directory (Azure AD).
Att integrera små förbättringar med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har till gång till små förbättringar.
* Du kan göra det möjligt för användarna att logga in automatiskt till små förbättringar (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med små förbättringar behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Den aktiverade prenumerationen för enkel inloggning med små förbättringar

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Små förbättringar stöder **SP** -INITIERAd SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Lägga till små förbättringar från galleriet

Om du vill konfigurera en integrering av små förbättringar i Azure AD måste du lägga till små förbättringar från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till små förbättringar från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **små förbättringar**i sökrutan, Välj **små förbättringar** från resultat panelen och klicka sedan på **Lägg till** för att lägga till programmet.

     ![Små förbättringar i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med små förbättringar baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i små förbättringar upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med små förbättringar måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med små förbättringar](#configure-small-improvements-single-sign-on)** – konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa små förbättringar testa användaren](#create-small-improvements-test-user)** – för att få en motsvarighet till Britta Simon med små förbättringar som är kopplade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med små förbättringar i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering på **små förbättringar** och väljer **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Små förbättringar domän-och URL-information för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [support teamet för små förbättringar](mailto:support@small-improvements.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera små förbättringar** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurera enkel inloggning med små förbättringar

1. Logga in på företags webbplatsen för små förbättringar som administratör i ett annat webbläsarfönster.

1. På huvud instrument panelens sida klickar du på knappen **Administration** till vänster.

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klicka på knappen **SAML SSO** i avsnittet **integrations** .

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Utför följande steg på sidan SSO-konfiguration:

    ![Konfigurera enkel inloggning](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. I text rutan **http-slutpunkt** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. Öppna det hämtade certifikatet i anteckningar, kopiera innehållet och klistra in det i text rutan **x509-certifikat** . 

    c. Om du vill ha ett SSO-och inloggnings formulär som är tillgängligt för användare, kontrollerar du att alternativet **Aktivera åtkomst via inloggning/lösen ord** är tillgängligt.  

    d. Ange lämpligt värde för att namnge SSO-inloggnings knappen i text rutan för **SAML-prompten** .  

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
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till små förbättringar.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **små förbättringar**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **små förbättringar**.

    ![Länken små förbättringar i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-small-improvements-test-user"></a>Skapa små förbättringar test användare

Om du vill att Azure AD-användare ska kunna logga in på små förbättringar måste de tillhandahållas i små förbättringar. När det gäller små förbättringar är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på företags webbplatsen för små förbättringar som administratör.

1. Från start sidan går du till menyn till vänster och klickar på **Administration**.

1. Klicka på knappen **användar katalog** från avsnittet användar hantering.

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klicka på **Lägg till användare**.

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. I dialog rutan **Lägg till användare** utför du följande steg: 

    ![Skapa en testanvändare för Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Ange det **första namnet** på användaren, t. ex. **Britta**.

    b. Ange det **senaste namnet** på användaren som **Simon**.

    c. Ange **e-postmeddelandet** som användare **brittasimon@contoso.com** .

    d. Du kan också välja att ange det personliga meddelandet i e-postmeddelandet **Skicka meddelande** . Avmarkera den här kryss rutan om du inte vill skicka meddelandet.

    e. Klicka på **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen små förbättringar på åtkomst panelen, bör du loggas in automatiskt på de små förbättringarna för vilka du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)