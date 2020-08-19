---
title: 'Självstudie: Azure Active Directory integration med optimalt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och optimalt.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 4564019f540d0dd08c8e0b2827b4a6b7df9c2cf0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544000"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Självstudie: Azure Active Directory integrering med optimalt

I den här självstudien får du lära dig att integrera optimalt med Azure Active Directory (Azure AD).
Att integrera i optimering med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till optimalt.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade för optimering (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna konfigurera Azure AD-integrering med optimal:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Optimerad enkel inloggnings prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har optimerat stöd för **SP** -INITIERAd SSO

## <a name="adding-optimizely-from-the-gallery"></a>Lägga till optimering från galleriet

Om du vill konfigurera integreringen av optimalt i Azure AD måste du lägga till optimering från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till optimering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **optimerat**, väljer **optimera** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Optimera i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med optimering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i optimalt.

Om du vill konfigurera och testa enkel inloggning med Azure AD med optimerat måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera](#configure-optimizely-single-sign-on)** enkel inloggning med enkel inloggning för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ett optimalt test av användare](#create-optimizely-test-user)** – för att få en motsvarighet till Britta Simon i optimalt som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med optimering:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **optimera** program integrering.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Optimera domän-och URL-information för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://app.optimizely.net/<instance name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > De här värdena är inte verkliga. Du uppdaterar värdet med den faktiska inloggnings-URL: en och identifieraren, som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ditt optimala program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

6. Utöver ovan förväntar sig programmet optimerat fler attribut för att skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut |
    | ---------------| --------------- |
    | e-post | user.mail |
    
    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera optimering** , kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-optimizely-single-sign-on"></a>Konfigurera enkel inloggning med enkel inloggning

1. Om du vill konfigurera enkel inloggning på **optimerings** sidan, kontakta din optimala konto hanterare och ange de hämtade **certifikaten (base64)** och lämpliga kopierade URL: er.

2. Som svar på din e-post ger optimerar du inloggnings-URL: en (SP-initierad SSO) och ID: t för service providerns entitets-ID.

    a. Kopiera den **SP-initierade SSO-URL: en** som tillhandahålls av optimera och klistra in i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    b. Kopiera **entitets-ID: t för service providern** som tillhandahålls av optimerat och klistra in i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

3. Logga in på ditt optimala program i ett annat webbläsarfönster.

4. Klicka på konto namnet i det övre högra hörnet och sedan på **konto inställningar**.

    ![Enkel inloggning för Azure AD](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. På fliken konto markerar du kryss rutan **Aktivera SSO** under enkel inloggning i **översikts** avsnittet.
  
    ![Enkel inloggning för Azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klicka på **Spara**

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att ge åtkomst till optimalt.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **optimera**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **optimera**.

    ![Länken optimera i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-optimizely-test-user"></a>Skapa optimalt test användare

I det här avsnittet skapar du en användare som kallas Britta Simon i ett optimalt område.

1. På sidan start väljer du fliken **medarbetare** .

2. Om du vill lägga till en ny medarbetare till projektet klickar du på **Ny medarbetare**.
   
    ![Skapa en testanvändare för Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Fyll i e-postadressen och tilldela dem en roll. Klicka på **Bjud in**.

    ![Skapa en testanvändare för Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. De får en e-postinbjudan. Med hjälp av e-postadressen måste de logga in för att optimera.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Optimera i åtkomst panelen bör du loggas in automatiskt på det optimerade alternativet för att konfigurera SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

