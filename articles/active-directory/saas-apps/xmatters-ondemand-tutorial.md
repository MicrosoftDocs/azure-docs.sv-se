---
title: 'Självstudie: Azure Active Directory-integrering med xMatters OnDemand | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och xMatters OnDemand.
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
ms.openlocfilehash: 9e84be9cbc6aa50f4beb3ed333b47459b15fbcc1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546295"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Självstudie: Azure Active Directory integrering med xMatters OnDemand

I den här självstudien får du lära dig att integrera xMatters OnDemand med Azure Active Directory (Azure AD).
Genom att integrera xMatters OnDemand med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till xMatters OnDemand.
* Du kan göra det möjligt för användarna att logga in automatiskt till xMatters OnDemand (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med xMatters OnDemand behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* xMatters OnDemand enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* xMatters OnDemand stöder **IDP** INITIERAd SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Lägger till xMatters OnDemand från galleriet

Om du vill konfigurera integreringen av xMatters OnDemand i Azure AD måste du lägga till xMatters OnDemand från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till xMatters OnDemand från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **XMatters OnDemand**, väljer **xMatters OnDemand** från resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

     ![xMatters OnDemand i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med xMatters OnDemand baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och en relaterad användare i xMatters OnDemand upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med xMatters OnDemand måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera XMatters OnDemand Single Sign-on](#configure-xmatters-ondemand-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa XMatters OnDemand test User](#create-xmatters-ondemand-test-user)** – om du vill ha en motsvarighet till Britta Simon i xMatters OnDemand som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med xMatters OnDemand:

1. I [Azure Portal](https://portal.azure.com/)på sidan **xMatters OnDemand** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![information om enkel inloggning för xMatters OnDemand-domän och URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    - `https://<companyname>.au1.xmatters.com.au/`
    - `https://<companyname>.cs1.xmatters.com/`
    - `https://<companyname>.xmatters.com/`
    - `https://www.xmatters.com`
    - `https://<companyname>.xmatters.com.au/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://<companyname>.au1.xmatters.com.au`
    - `https://<companyname>.xmatters.com/sp/<instancename>`
    - `https://<companyname>.cs1.xmatters.com/sp/<instancename>`
    - `https://<companyname>.au1.xmatters.com.au/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [XMatters OnDemand client support team](https://www.xmatters.com/company/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

    > [!IMPORTANT]
    > Du måste vidarebefordra certifikatet till [support teamet XMatters OnDemand](https://www.xmatters.com/company/contact-us/). Certifikatet måste laddas upp av xMatters support-teamet innan du kan slutföra konfigurationen för enkel inloggning.

6. I avsnittet **Konfigurera XMatters OnDemand** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurera enkel inloggning för xMatters OnDemand

1. Logga in på din XMatters OnDemand-företags webbplats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på **admin**och sedan på **företags information** i navigerings fältet på vänster sida.

    ![Administratör](./media/xmatters-ondemand-tutorial/IC776795.png "Administratör")

3. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![SAML-konfiguration](./media/xmatters-ondemand-tutorial/IC776796.png "SAML-konfiguration")

    a. Välj **Aktivera SAML**.

    b. I text rutan **ID för identitets leverantör** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    c. I text rutan **enkel inloggnings-URL** klistrar du in **inloggnings-URL** -värdet som du har kopierat från Azure Portal.

    d. I text rutan **URL för enkel utloggning** , klistra in **URL**för att klistra in, som du har kopierat från Azure Portal.

    e. Klicka på **Spara ändringar**längst upp på sidan företags information.

    ![Företagsinformation](./media/xmatters-ondemand-tutorial/IC776797.png "Företagsinformation")

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till xMatters OnDemand.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **xMatters OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **XMatters OnDemand**.

    ![XMatters OnDemand-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-xmatters-ondemand-test-user"></a>Skapa xMatters OnDemand test User

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i xMatters OnDemand.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **XMatters OnDemand** -klient.

2. Klicka på fliken **användare** . och klicka sedan på **Lägg till användare**.

    ![Användare](./media/xmatters-ondemand-tutorial/IC781048.png "Användare")

3. I avsnittet **Lägg till en användare** utför du följande steg:

    ![Lägg till en användare](./media/xmatters-ondemand-tutorial/IC781049.png "Lägg till en användare")

    a. Välj **Active** (Aktiv).

    b. I text rutan **användar-ID** anger du användar-ID: t för användaren Brittasimon@contoso.com .

    c. I textrutan för **förnamn** skriver du förnamnet på användaren, till exempel Britta.

    d. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    e. I text rutan **plats** anger du en giltig webbplats för ett giltigt Azure AD-konto som du vill etablera.

    f. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen xMatters OnDemand på åtkomst panelen, bör du loggas in automatiskt på xMatters OnDemand som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

