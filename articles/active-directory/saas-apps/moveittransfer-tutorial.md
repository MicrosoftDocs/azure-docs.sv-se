---
title: 'Självstudie: Azure Active Directory integrering med MOVEit-överföring – Azure AD-integrering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit-överföring – Azure AD-integrering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4ec0a3082b5978e0c540a2e0a70843081201a29b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544171"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudie: Azure Active Directory integrering med MOVEit transfer – Azure AD-integrering

I den här självstudien får du lära dig att integrera MOVEit-överföring – Azure AD-integrering med Azure Active Directory (Azure AD).
Att integrera MOVEit-överföring – Azure AD-integrering med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till MOVEit-överföring – Azure AD-integrering.
* Du kan göra det möjligt för användarna att logga in automatiskt till MOVEit-överföring – Azure AD-integrering (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MOVEit-överföring – Azure AD-integrering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit-överföring – Azure AD-integrering med enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MOVEit-överföring – Azure AD-integrering stöder **SP** -INITIERAd SSO

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Lägga till MOVEit-överföring – Azure AD-integrering från galleriet

Om du vill konfigurera integrationen av MOVEit transfer-Azure AD-integrering i Azure AD måste du lägga till MOVEit transfer-Azure AD integration från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till MOVEit transfer – Azure AD-integrering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **MOVEit transfer-Azure AD integration**, väljer **MOVEit transfer – Azure AD integration** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![MOVEit-överföring – Azure AD-integrering i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med MOVEit transfer – Azure AD-integration baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och en relaterad användare i MOVEit transfer – Azure AD-integrering etableras.

Om du vill konfigurera och testa enkel inloggning med Azure AD med MOVEit transfer – Azure AD-integrering måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MOVEit-överföring – enkel inloggning i Azure AD-integrering](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** – så här konfigurerar du inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MOVEit transfer – Azure AD integration test User](#create-moveit-transfer---azure-ad-integration-test-user)** – om du vill ha en motsvarighet till Britta Simon i MOVEit transfer – Azure AD-integrering som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med MOVEit transfer – Azure AD-integrering:

1. I [Azure Portal](https://portal.azure.com/)på sidan **MOVEit transfer – Azure AD integration** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värde automatiskt i avsnittet **grundläggande SAML-konfiguration** :

    ![MOVEit-överföring – Azure AD-integrerings domän och enkel inloggnings information för URL: er](common/sp-identifier-reply.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://contoso.com`

    > [!NOTE]
    > **Inloggnings-URL** -värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [MOVEit transfer – support teamet för Azure AD integration client](https://community.ipswitch.com/s/support) för att hämta värdet. Du kan ladda ned **metadata filen för tjänst leverantören** från **providerns metadata-URL** , som beskrivs senare i avsnittet **Konfigurera Moveit-överföring – Azure AD-integrering** i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera MOVEit-överföring – Azure AD-integrering** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurera MOVEit-överföring – enkel inloggning med Azure AD-integrering

1. Logga in på MOVEit transfer-klienten som administratör.

2. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

    ![Avsnittet Inställningar på App-sidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klicka på **single inloggning** Link, som finns under **säkerhets principer – > användarautentisering**.

    ![Säkerhets principer på App-sida](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Hämta Metadatadokumentet genom att klicka på länken för metadata-URL.

    ![Metadata-URL för tjänste leverantör](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Verifiera **entityId** matchar **identifieraren** i avsnittet **grundläggande SAML-konfiguration** .
   * Verifiera **AssertionConsumerService** -platsens URL matchar **svars-URL**  i avsnittet **grundläggande SAML-konfiguration** .
    
     ![Konfigurera enkel inloggning på appsidan](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klicka på knappen **Lägg till identitetsprovider** för att lägga till en ny federerad identitets leverantör.

    ![Lägg till identitetsprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klicka på **Bläddra...** om du vill välja den metadatafil som du laddade ned från Azure Portal, klickar du på **Lägg till identitets leverantör** för att ladda upp den nedladdade filen.

    ![SAML Identity Provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Välj "**Ja**" som **aktive rad** på sidan **Redigera inställningar för federerad identitetsprovider...** och klicka på **Spara**.

    ![Inställningar för federerade identitets leverantörer](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. På sidan **Redigera användar inställningar för federerad identitetsprovider** utför du följande åtgärder:
    
    ![Redigera inställningar för federerade identitets leverantörer](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Välj **SAML-NameID** som **inloggnings namn**.
    
    b. Välj **annat** som **fullständigt namn** och ange värdet i text rutan **attributnamn** : `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Välj **annan** som **e-post** och i text rutan **attributnamn** sätter du in värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Välj **Ja** som **Skapa automatiskt konto på inloggning**.
    
    e. Klicka på knappen **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MOVEit-överföring – Azure AD-integrering.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **MOVEit transfer – Azure AD-integrering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **MOVEit transfer – Azure AD-integrering**.

    ![Länken MOVEit transfer – Azure AD integration i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Skapa MOVEit-överföring – test användare för Azure AD-integration

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i MOVEit transfer – Azure AD-integrering. MOVEit-överföring – Azure AD-integration stöder just-in-Time-etablering, som du har aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt MOVEit-överföring – Azure AD-integration om den inte finns ännu.

>[!NOTE]
>Om du behöver skapa en användare manuellt måste du kontakta [support teamet MOVEit transfer-Azure AD integration-klient](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen MOVEit transfer-Azure AD-integration på åtkomst panelen, bör du loggas in automatiskt på den MOVEit-överföring – Azure AD-integration som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

