---
title: 'Självstudie: Azure Active Directory integrering med ArcGIS Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.openlocfilehash: a544a98b3a9d5c2d30f95240325e91018d2f40c9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549631"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Självstudie: Azure Active Directory integrering med ArcGIS Enterprise

I den här självstudien lär du dig att integrera ArcGIS Enterprise med Azure Active Directory (AD Azure).
Genom att integrera ArcGIS Enterprise med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till ArcGIS Enterprise.
* Du kan göra så att dina användare automatiskt loggas in på ArcGIS Enterprise (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med ArcGIS Enterprise behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ArcGIS Enterprise-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ArcGIS Enterprise stöder **IDP**-initierad enkel inloggning
* ArcGIS Enterprise stöder **just-in-time**-användaretablering


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Lägga till ArcGIS Enterprise från galleriet

För att konfigurera integreringen av ArcGIS Enterprise till Azure AD behöver du lägga till ArcGIS Enterprise från galleriet till listan över hanterade SaaS-appar.

**Lägg till ArcGIS Enterprise från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ArcGIS Enterprise**, väljer **ArcGIS Enterprise** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ArcGIS Enterprise i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för ArcGIS Enterprise](#configure-arcgis-enterprise-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ArcGIS Enterprise-testanvändare](#create-arcgis-enterprise-test-user)** – för att ha en motsvarighet för Britta Simon i ArcGIS Enterprise som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **ArcGIS Enterprise**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg, om du vill konfigurera programmet i **IDP** initierat läge:

    ![ArcGIS Enterprise-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<EXTERNAL_DNS_NAME>.portal`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![ArcGIS Enterprise-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Hämta dessa värden genom att kontakta [supportteamet för ArcGIS Enterprise-klienten](mailto:support@esri.com). Du får identifierarvärdet från avsnittet **Ange identitetsprovider**, som beskrivs senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Konfigurera enkel inloggning för ArcGIS Enterprise

1. Om du vill automatisera konfigurationen inom ArcGIS Enterprise måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera ArcGIS Enterprise** för att dirigera dig till ArcGIS Enterprise-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på ArcGIS Enterprise. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera ArcGIS Enterprise manuellt loggar du in på företags platsen för ArcGIS Enterprise som administratör.


1. Välj **Organization >EDIT SETTINGS** (Organisation > Redigera inställningar).

    ![Konfiguration av ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

1. Välj fliken **Säkerhet**.

    ![Konfiguration av ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

1. Rulla ned till avsnittet **Enterprise Logins via SAML** (Enterprise-inloggningar via SAML) och välj **SET ENTERPRISE LOGIN** (Ange Enterprise-inloggning).

    ![Konfiguration av ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

1. I avsnittet **Set Identity Provider** (Ange identitetsprovider) utför du följande steg:

    ![Konfiguration av ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. Ange ett namn såsom **Azure Active Directory-test** i textrutan **Namn**.

    b. I textrutan **URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    c. Klicka på **Visa avancerade inställningar** och kopiera värdet **Entitets-ID** och klistra in det i textrutan **Identifierare** i avsnittet **ArcGIS Enterprise Domain and URLs** (ArcGIS Enterprise-domän och URL:er) i Azure-portalen.
    
    ![Konfiguration av ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Klicka på **UPDATE IDENTITY PROVIDER** (Uppdatera identitetsprovider).

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

I det här avsnittet ser du till att Britta Simon ska kunna använda enkel inloggning i Azure genom att bevilja åtkomst till ArcGIS Enterprise.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **ArcGIS Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **ArcGIS Enterprise**.

    ![ArcGIS Enterprise-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-arcgis-enterprise-test-user"></a>Skapa ArcGIS Enterprise-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i ArcGIS Enterprise. ArcGIS Enterprise stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i ArcGIS Enterprise skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du  [supportteamet för ArcGIS Enterprise](mailto:support@esri.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på ArcGIS Enterprise-panelen i åtkomstpanelen bör du automatiskt loggas in på ArcGIS Enterprise som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

