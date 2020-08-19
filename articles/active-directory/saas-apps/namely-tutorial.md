---
title: 'Självstudie: Azure Active Directory integration med nämligen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dvs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 00ddd61209af6e0d16b7f7579a6e8729cde0cdf0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552483"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Självstudie: Azure Active Directory integrering med, nämligen

I den här självstudien får du lära dig att integrera, nämligen med Azure Active Directory (Azure AD).
Integrering med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till, nämligen.
* Du kan göra det möjligt för användarna att logga in automatiskt till, nämligen (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med, måste du ha följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel prenumeration som är aktive rad för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Nämligen stöder **SP** -INITIERAd SSO

## <a name="adding-namely-from-the-gallery"></a>Lägga till i, nämligen från galleriet

Om du vill konfigurera integrationen av, i Azure AD, måste du lägga till nämligen från galleriet till listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **nämligen** **, dvs. Välj från resultat** panelen och klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![Nämligen i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med, baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i.

Om du vill konfigurera och testa enkel inloggning med Azure AD med nämligen, måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning](#configure-namely-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa som-test användare](#create-namely-test-user)** – för att få en motsvarighet till Britta Simon i, d.v.s. som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **för program integrering** .

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Nämligen information om enkel inloggning för domäner och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.namely.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta: [klient support teamet](https://www.namely.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera** a, kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-namely-single-sign-on"></a>Konfigurera enkel inloggning

1. Logga in på företagets plats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på **företag**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_06.png) 

3. Klicka på fliken **Settings** (Inställningar).
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_07.png) 

4. Klicka på **SAML**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_08.png) 

5. Utför följande steg på sidan **SAML-inställningar** :
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Klicka på **Aktivera SAML**. 

    b. I text rutan **URL för identitetsprovider** , klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.
    
    c. Öppna det hämtade certifikatet i anteckningar, kopiera innehållet och klistra in det i text rutan för **identitets leverantörs certifikat** .
     
    d. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till alltså.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan, **nämligen**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **nämligen**.

    ![Nämligen-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-namely-test-user"></a>Skapa, nämligen test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i nämligen.

**Gör så här för att skapa en användare som heter Britta Simon i:**

1. Logga in på din företags plats som administratör.

2. I verktygsfältet högst upp klickar du på **personer**.
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_10.png) 

3. Klicka på fliken **katalog** .
   
    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_11.png) 

4. Klicka på **Lägg till ny person**.

    ![Konfigurera enkel inloggning](./media/namely-tutorial/tutorial_namely_12.png)

5. I dialog rutan **Lägg till ny person** utför du följande steg:

    a. I text rutan för det **första namnet** skriver du **Britta**.

    b. Skriv **Simon**i text rutan **efter namn** .

    c. Skriv **e-postadressen** för BrittaSimon i text rutan för **e-post** .

    d. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ensamt i åtkomst panelen, bör du loggas in automatiskt på den så att du kan konfigurera SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

