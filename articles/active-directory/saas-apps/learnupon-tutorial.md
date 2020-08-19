---
title: 'Självstudie: Azure Active Directory integrering med LearnUpon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LearnUpon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9541f2c5f8ee1aac45b6f659547a3ee34c093181
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535177"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Självstudie: Azure Active Directory integrering med LearnUpon

I den här självstudien får du lära dig hur du integrerar LearnUpon med Azure Active Directory (Azure AD).
Genom att integrera LearnUpon med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LearnUpon.
* Du kan göra det möjligt för användarna att logga in automatiskt till LearnUpon (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LearnUpon behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* LearnUpon-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.


* LearnUpon stöder **IDP** INITIERAd SSO

* LearnUpon stöder **just-in-Time** User-etablering


## <a name="adding-learnupon-from-the-gallery"></a>Lägga till LearnUpon från galleriet

Om du vill konfigurera integreringen av LearnUpon i Azure AD måste du lägga till LearnUpon från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till LearnUpon från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **LearnUpon**, väljer **LearnUpon** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![LearnUpon i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med LearnUpon baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i LearnUpon upprättas.

Om du vill konfigurera och testa enkel inloggning med LearnUpon i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LearnUpon-enkel inloggning](#configure-learnupon-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LearnUpon test User](#create-learnupon-test-user)** – om du vill ha en motsvarighet till Britta Simon i LearnUpon som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med LearnUpon i Azure AD:

1. Välj **enkel inloggning**på sidan **LearnUpon** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för LearnUpon-domän och URL: er](common/idp-reply.png)

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [LearnUpon client support team](https://www.learnupon.com/features/support/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** letar du upp **tumavtrycket** – detta läggs till i dina LearnUpon SAML-inställningar.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera LearnUpon** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-learnupon-single-sign-on"></a>Konfigurera LearnUpon enkel inloggning

1. Öppna en annan webb läsar instans och logga in i LearnUpon med ett administratörs konto.

1. Klicka på fliken **Inställningar** .

    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klicka på **enkel inloggning på SAML**och klicka sedan på **allmänna inställningar** för att konfigurera SAML-inställningar.
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. I avsnittet **allmänna inställningar** utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Välj **Aktiverad**.

    b. Välj **version** som **2,0**.

    c. Välj **hoppa över villkor** som **Nej**.

    d. I text rutan för **SAML-token post** anger du namnet på begär ande post-parametern till SAML-konsument-URL: en som anges ovan och som innehåller SAML-kontrollen som ska verifieras och autentiseras, till exempel **SAMLResponse**.

    e. I text rutan **namn på ID-format** anger du det värde som anger var i SAML-försäkran användarens identifierare (e-postadress) finns, till exempel `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .
  
    f. I text rutan **identifiera providerns plats** anger du det värde som anger var användarna skickas till om de klickar på den uppladdade ikonen från din Azure Portal inloggnings skärm.
  
    ex. I text rutan **Logga ut URL** klistrar du in URL-värdet för **utloggning** , som du har kopierat från Azure Portal.

    h. Klicka på **Hantera finger avtryck**och överför sedan finger utskriften av ditt hämtade certifikat.

1. Klicka på **användar inställningar**och utför sedan följande steg:

     ![Konfigurera enkel inloggning](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. I text rutan för den **första namn identifieraren** anger du det värde som talar om för oss var i SAML-kontrollen att användarnas FirstName finns, till exempel: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .
  
    b. I text rutan för den **sista namn identifieraren** anger du det värde som talar om för oss var i SAML-försäkran som användare efter namn finns, till exempel: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LearnUpon.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **LearnUpon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LearnUpon**.

    ![LearnUpon-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-learnupon-test-user"></a>Skapa LearnUpon test användare

I det här avsnittet skapas en användare som kallas Britta Simon i LearnUpon. LearnUpon stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i LearnUpon skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt måste du kontakta [LearnUpon support team](https://www.learnupon.com/features/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LearnUpon på åtkomst panelen, bör du loggas in automatiskt på den LearnUpon som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)