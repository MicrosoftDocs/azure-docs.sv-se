---
title: 'Självstudie: Azure Active Directory integration med identifiera | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och känner igen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 86c2c7383f3850b0c51831f94e673c3941894a08
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548906"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Självstudie: Azure Active Directory integration med identifiera

I den här självstudien får du lära dig att integrera igenkänning med Azure Active Directory (Azure AD).
Att integrera igenkänning med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst att känna igen.
* Du kan göra det möjligt för användarna att logga in automatiskt för att identifiera (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med identifiera behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Identifiera enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Identifiera stöd för **SP** -INITIERAd SSO

## <a name="adding-recognize-from-the-gallery"></a>Lägga till igenkänning från galleriet

Om du vill konfigurera integreringen av identifiera i Azure AD måste du lägga till identifiera från galleriet i listan över hanterade SaaS-appar.

**Gör så här om du vill lägga till igenkänning från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du in **identifiera**, väljer **identifiera** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Identifiera i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med hjälp av en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i identifiera.

Om du vill konfigurera och testa enkel inloggning med Azure AD med identifiera måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera identifiera enkel inloggning](#configure-recognize-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa identifiera test användare](#create-recognize-test-user)** – för att få en motsvarighet till Britta Simon i att identifiera att är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med identifiera:

1. I [Azure Portal](https://portal.azure.com/)på sidan **identifiera** program integrering väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du hämtar metadata- **filen för tjänst leverantören** från avsnittet **Konfigurera identifiera enkel inloggning** i självstudien.

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** -värdet i automatiskt i avsnittet grundläggande SAML-konfiguration.

    ![Identifiera information om enkel inloggning för domän och URL: er](common/sp-identifier.png)

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Om **Identifier** -värdet inte är automatiskt ifyllt får du identifieraren genom att öppna URL: en för providerns metadata från avsnittet SSO-inställningar som beskrivs senare i avsnittet **Konfigurera identifiera enkel inloggning** i självstudien. Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kund support teamet](mailto:support@recognizeapp.com) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera igenkänning** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurera identifiera enkel inloggning

1. I ett annat webbläsarfönster loggar du in på din identifierande klient organisation som administratör.

2. Klicka på **meny**i det övre högra hörnet. Gå till **företags administratör**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_000.png)

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Utför följande steg i avsnittet **SSO-inställningar** .
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Som **Aktivera SSO**väljer du **på**.

    b. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.
    
    c. I text rutan för **SSO-mål-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.
    
    d. I text rutan **mål webb adress för service nivå mål** klistrar du in värdet för den **utloggnings-URL** som du kopierade från Azure Portal. 
    
    e. Öppna din hämtade **certifikat fil (base64)** i anteckningar, kopiera innehållet i den till Urklipp och klistra sedan in den i text rutan **certifikat** .
    
    f. Klicka på knappen **Spara inställningar** . 

5. Under avsnittet **SSO-inställningar** kopierar du webb adressen under **providerns metadata-URL**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Öppna **URL-länken för metadata** under en tom webbläsare för att ladda ned Metadatadokumentet. Kopiera sedan EntityDescriptor-värdet (entityID) från filen och klistra in den i text rutan **identifierare** i den **grundläggande SAML-konfigurationen** på Azure Portal.
    
    ![Konfigurera enkel inloggning på appsidan](./media/recognize-tutorial/tutorial_recognize_004.png)

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till att identifiera.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **identifiera**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **identifiera**.

    ![Länken identifiera i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-recognize-test-user"></a>Skapa identifiera test användare

För att Azure AD-användare ska kunna logga in på identifiera måste de tillhandahållas i igenkännings syfte. När det gäller igenkänning är etableringen en manuell uppgift.

Den här appen har inte stöd för SCIM-etablering men har en alternativ användar synkronisering som etablerar användare. 

**Gör följande för att etablera ett användarkonto:**

1. Logga in på företags webbplatsen för igenkänning som administratör.

2. Klicka på **meny**i det övre högra hörnet. Gå till **företags administratör**.

3. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

4. Utför följande steg i avsnittet om **synkronisering av användare** .
   
    ![Ny användare](./media/recognize-tutorial/tutorial_recognize_005.png "Ny användare")
   
    a. När **synkronisering är aktiverat**väljer du **på**.
   
    b. Som **Välj Sync-Provider**väljer du **Microsoft/Office 365**.
   
    c. Klicka på **Kör användar synkronisering**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen identifiera i åtkomst panelen, bör du loggas in automatiskt på det identifierat som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

