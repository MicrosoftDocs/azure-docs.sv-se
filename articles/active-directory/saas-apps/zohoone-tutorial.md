---
title: 'Självstudie: Azure Active Directory integration med Zoho One | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: baa33e79b3bba8eccb2d0fc03e110e1a3c4defde
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546014"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Självstudie: Azure Active Directory integrering med Zoho One

I den här självstudien får du lära dig att integrera Zoho en med Azure Active Directory (Azure AD).
Genom att integrera Zoho en med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Zoho.
* Du kan göra det möjligt för användarna att logga in automatiskt för att Zoho en (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zoho ett behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Zoho en aktive rad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zoho One stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Lägga till Zoho från galleriet

Om du vill konfigurera integreringen av Zoho i Azure AD måste du lägga till Zoho från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zoho från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zoho ett**, väljer **Zoho ett** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Zoho en i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Zoho, baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zoho.

Om du vill konfigurera och testa enkel inloggning med Zoho i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Zoho en enkel inloggning](#configure-zoho-one-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zoho en test användare](#create-zoho-one-test-user)** – om du vill ha en motsvarighet till Britta Simon i Zoho som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Zoho One:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Zoho One** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Zoho en enkel inloggnings information för en domän och URL](common/idp-relay.png)

    a. Skriv en URL i text rutan **identifierare** : `one.zoho.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Föregående **svars-URL** -värde är inte verkligt. Du får `<saml-identifier>` värdet från #step4 av **Konfigurera Zoho ett enda inloggnings** avsnitt, som beskrivs senare i självstudien.

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://one.zoho.com`

5. Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:


    ![Zoho en enkel inloggnings information för en domän och URL](common/both-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Föregående **URL-** värde för inloggning är inte verkligt. Du uppdaterar värdet med den faktiska inloggnings-URL: en från avsnittet **Konfigurera Zoho ett enkel inloggning** , som beskrivs senare i självstudien. 

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. På sidan **Konfigurera Zoho** , kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Konfigurera Zoho en enkel inloggning

1. Logga in på din Zoho-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Konfigurera** under **SAML-autentisering**på fliken **organisation** .

    ![Zoho en organisation](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. På popup-sidan utför du följande steg:

    ![Zoho en sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. I text rutan för utloggnings **-URL** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    c. Klicka på **Bläddra** för att ladda upp det **certifikat (base64)** som du har laddat ned från Azure Portal.

    d. Klicka på **Spara**.

4. När du har sparat inställningen för SAML-autentisering kopierar du värdet för **SAML-identifieraren** och lägger till det med **svars-URL:** en i stället för `<saml-identifier>` `https://accounts.zoho.com/samlresponse/one.zoho.com` och klistrar in det genererade värdet i text rutan **SVARs-URL** under avsnittet **grundläggande SAML-konfiguration** .

    ![Zoho ett SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Gå till fliken **domäner** och klicka sedan på **Lägg till domän**.

    ![Zoho en domän](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. På sidan **Lägg till domän** utför du följande steg:

    ![Zoho en Lägg till domän](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. I text rutan **domän namn** skriver du domän som contoso.com.

    b. Klicka på **Lägg till**.

    >[!Note]
    >När du har lagt till domänen följer du [de här](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stegen för att verifiera din domän. När domänen har verifierats kan du använda ditt domän namn i **inloggnings-URL: en** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zoho One.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Zoho ett**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zoho ett**.

    ![Zoho en länk i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zoho-one-test-user"></a>Skapa Zoho en test användare

Om du vill att Azure AD-användare ska kunna logga in på Zoho ett måste de vara etablerade i Zoho One. I Zoho One är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in för att Zoho en som säkerhets administratör.

2. På fliken **användare** klickar du på **användar logo typ**.

    ![Zoho en användare](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Utför följande steg på sidan **Lägg till användare** :

    ![Zoho en Lägg till användare](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. I text rutan **namn** anger du namnet på den användare som **Britta Simon**.
    
    b. I textrutan **E-postadress** anger du användarens e-postadress, som brittasimon@contoso.com.

    >[!Note]
    >Välj din verifierade domän i listan domän.

    c. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zoho en panel på åtkomst panelen, bör du loggas in automatiskt på den Zoho som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

