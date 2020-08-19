---
title: 'Självstudie: Azure Active Directory integration med SilkRoad Life Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad Life Suite.
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
ms.openlocfilehash: 49dd333454f0eb92f5fb0dddc40390ec1baa91c5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525602"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudie: Azure Active Directory integrering med SilkRoad Life Suite

I den här självstudien får du lära dig att integrera SilkRoad Life Suite med Azure Active Directory (Azure AD).
Genom att integrera SilkRoad Life Suite med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till SilkRoad Life Suite.
* Du kan göra det möjligt för användarna att logga in automatiskt till SilkRoad Life Suite (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SilkRoad Life Suite behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SilkRoad Life Suite-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SilkRoad Life Suite stöder **SP** -INITIERAd SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Lägga till SilkRoad Life Suite från galleriet

Om du vill konfigurera SilkRoad Life Suite till Azure AD måste du lägga till SilkRoad Life Suite från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SilkRoad Life Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SilkRoad Life Suite**, väljer **SilkRoad Life Suite** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SilkRoad Life-Svit i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SilkRoad Life Suite baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i SilkRoad Life Suite.

Om du vill konfigurera och testa enkel inloggning med SilkRoad Life Suite i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SilkRoad Life Suite enkel inloggning](#configure-silkroad-life-suite-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SilkRoad Life Suite test User](#create-silkroad-life-suite-test-user)** – om du vill ha en motsvarighet till Britta Simon i SilkRoad Life Suite som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med SilkRoad Life Suite:

1. I [Azure Portal](https://portal.azure.com/)på sidan **SilkRoad Life Suite** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    > [!NOTE]
    > Du får en förklarad **fil för tjänst leverantören** som beskrivs senare i den här självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

    d. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. I avsnittet **grundläggande SAML-konfiguration** , om du inte har en **metadata-fil för tjänst leverantören**, utför följande steg:

    ![Information om SilkRoad Life Suite-domän och URL: er för enkel inloggning](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. I rutan **Identifierare** skriver du en URL med följande mönster:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [SilkRoad Life Suite-klientens support team](https://www.silkroad.com/locations/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera SilkRoad Life Suite** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurera SilkRoad Life Suite enkel inloggning

1. Logga in på din SilkRoad företags webbplats som administratör.

    > [!NOTE]
    > För att få åtkomst till SilkRoad Life Suite Authentication Application för att konfigurera Federation med Microsoft Azure AD, kontakta SilkRoad-supporten eller din SilkRoad Services-representant.

1. Gå till **tjänst leverantör**och klicka sedan på **Federations information**.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klicka på **Ladda ned federationsmetadata**och spara sedan metadatafilen på datorn. Använd hämtade federationsmetadata som en fil för en **tjänst leverantörs-metadata** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. I ditt **Silkroad** -program klickar du på **källor för autentisering**.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klicka på **Lägg till källa för autentisering**.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. I avsnittet **Lägg till autentiserings källa** utför du följande steg:

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Under **Alternativ 2 – metadatafil**klickar du på **Bläddra** för att ladda upp den hämtade Metadata-filen från Azure Portal.
  
    b. Klicka på **skapa identitets leverantör med fildata**.

1. I avsnittet **autentiseringsnivåer** klickar du på **Redigera**.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Utför följande steg i dialog rutan **Redigera Authentication source** :

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Välj **Ja**som **aktive rad**.

    b. I text rutan **EntityId** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    c. I text rutan **Beskrivning av IDP** anger du en beskrivning av konfigurationen (till exempel: *Azure AD SSO*).

    d. I text rutan **metadatafil** laddar du upp den **metadatafil** som du laddade ned från Azure Portal.
  
    e. I text rutan **namn på IDP** anger du ett namn som är särskilt för din konfiguration (till exempel: *Azure SP*).
  
    f. I text rutan för **utloggnings tjänstens URL** klistrar du in värdet för den **utloggnings-URL** som du kopierade från Azure Portal.

    ex. I text rutan **inloggnings tjänstens URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    h. Klicka på **Spara**.

1. Inaktivera alla andra källor för autentisering.

    ![Enkel inloggning för Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SilkRoad Life Suite.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SilkRoad Life Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SilkRoad Life Suite**.

    ![SilkRoad Life Suite-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silkroad-life-suite-test-user"></a>Skapa SilkRoad Life Suite test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i SilkRoad Life Suite. Arbeta med [support teamet för SilkRoad Life Suite](https://www.silkroad.com/locations/) för att lägga till användarna i SilkRoad Life Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SilkRoad Life Suite på åtkomst panelen, bör du loggas in automatiskt till SilkRoad-tidssviten som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
