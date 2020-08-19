---
title: 'Självstudie: Azure Active Directory integrering med Marketo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: c1b8874d8813d6200c915778841c26e77b02e434
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554933"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Självstudie: Azure Active Directory integrering med Marketo

I den här självstudien får du lära dig hur du integrerar Marketo med Azure Active Directory (Azure AD).
Att integrera Marketo med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har till gång till Marketo.
* Du kan göra det möjligt för användarna att logga in automatiskt till Marketo (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Marketo behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Aktive rad prenumeration för enkel inloggning med Marketo

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Marketo stöder **IDP** INITIERAd SSO

## <a name="adding-marketo-from-the-gallery"></a>Lägga till Marketo från galleriet

Om du vill konfigurera integrering av Marketo i Azure AD måste du lägga till Marketo från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Marketo från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Marketo**, väljer **Marketo** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Marketo i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Marketo baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Marketo upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Marketo måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Marketo](#configure-marketo-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ett Marketo-test](#create-marketo-test-user)** för att få en motsvarighet till Britta Simon i Marketo som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med Marketo:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Marketo** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för Marketo-domäner och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://saml.marketo.com/sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Marketo-klientens support team](https://investors.marketo.com/contactus.cfm) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Marketo** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-marketo-single-sign-on"></a>Konfigurera enkel inloggning för Marketo

1. Om du vill hämta Munchkin-ID för ditt program loggar du in på Marketo med administratörs behörighet och utför följande åtgärder:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till integrations menyn och klicka på **länken Munchkin**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopiera Munchkin-ID: t som visas på skärmen och slutför svars-URL: en i konfigurations guiden för Azure AD.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Följ stegen nedan om du vill konfigurera SSO i programmet:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till integrations menyn och klicka på **enkel inloggning**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Om du vill aktivera SAML-inställningarna klickar du på knappen **Redigera** .
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktive rad** Inställningar för enkel inloggning.
   
    f. Klistra in **Azure AD-identifieraren**i text rutan **utfärdar-ID** .
   
    ex. Ange URL: en som i text rutan **entitets-ID** `http://saml.marketo.com/sp` .
   
    h. Välj elementet användar-ID plats som **namn identifierare**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Om användar identifieraren inte är UPN-värde ändrar du värdet i fliken attribut.
   
    i. Ladda upp certifikatet, som du har laddat ned från konfigurations guiden för Azure AD. **Spara** inställningarna.
   
    j. Redigera inställningarna för omdirigera sidor.
   
    k. Klistra in **inloggnings webb adressen** i text rutan för **INLOGGNINGs-URL** : en.
   
    l. Klistra in **URL:** en för utloggning i text rutan för **utloggnings-URL** .
   
    m. I **fel-URL**: en kopierar du din webbplats för **Marketo-instansen** och klickar på knappen **Spara** för att spara inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Om du vill aktivera SSO för användare utför du följande åtgärder:
   
    a. Logga in på Marketo-appen med administratörs behörighet.
   
    b. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Gå till menyn **säkerhet** och klicka på **inloggnings inställningar**.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Markera alternativet **KRÄV SSO** och **Spara** inställningarna.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_14.png)

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Marketo.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Marketo**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Marketo**.

    ![Marketo-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-marketo-test-user"></a>Skapa användare av Marketo-test

I det här avsnittet skapar du en användare som heter Britta Simon i Marketo. Följ dessa steg om du vill skapa en användare i Marketo-plattform.

1. Logga in på Marketo-appen med administratörs behörighet.

2. Klicka på knappen **administratör** i det övre navigerings fönstret.
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Gå till menyn **säkerhet** och klicka på **användare & roller**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klicka på länken **Bjud in ny användare** på fliken användare
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Fyll i följande information i guiden Bjud in ny användare
   
    a. Ange användarens **e-** postadress i text rutan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Ange **förnamnet** i text rutan
   
    c. Ange **efter namnet**  i text rutan
   
    d. Klicka på **Nästa**

6. På fliken **behörigheter** väljer du **userRoles** och klickar på **Nästa**
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klicka på knappen **Skicka** för att skicka användar inbjudan
   
    ![Konfigurera enkel inloggning](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Användaren får e-postaviseringen och måste klicka på länken och ändra lösen ordet för att aktivera kontot. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Marketo på åtkomst panelen, bör du loggas in automatiskt på den Marketo som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

