---
title: 'Självstudie: Azure Active Directory integrering med Adobe Sign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Sign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: b7d1ea6c1ee21970a598855471284130160e1d65
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537965"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Självstudie: Azure Active Directory integrering med Adobe Sign

I den här självstudien lär du dig hur du integrerar Adobe Sign med Azure Active Directory (Azure AD).
Integreringen av Adobe Sign med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Adobe Sign.
* Du kan göra så att dina användare automatiskt loggas in på Adobe Sign (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Adobe Sign behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Sign-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Adobe Sign stöder **SP**-initierad enkel inloggning

## <a name="adding-adobe-sign-from-the-gallery"></a>Lägga till Adobe Sign från galleriet

För att konfigurera integreringen av Adobe Sign med Azure AD måste du lägga till SAML SSO for Adobe Sign från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Adobe Sign från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Adobe Sign**, väljer **Adobe Sign** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Adobe Sign i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Adobe Sign baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Adobe Sign upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Adobe Sign behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Adobe Sign](#configure-adobe-sign-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Adobe Sign-testanvändare](#create-adobe-sign-test-user)** – för att ha en motsvarighet för Britta Simon i Adobe Sign som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Adobe Sign:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Adobe Sign**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Adobe Sign-domäner och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.echosign.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Adobe Sign** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-adobe-sign-single-sign-on"></a>Konfigurera enkel inloggning för Adobe Sign

1. Innan du konfigurerar konfigurationen kan du kontakta [support teamet för Adobe Sign client](https://helpx.adobe.com/in/contact/support.html) för att lägga till din domän i listan med Adobe-signerade tillåtna. Så här lägger du till domänen:

    a. [Supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) skickar dig en slumpmässigt genererad token. För din domän kommer token se ut ungefär på följande sätt: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publicera verifieringstoken i en DNS-textpost och meddela [supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Det kan ta några dagar eller längre. Observera att DNS-spridningsfördröjningar innebär att ett värde som publiceras i DNS kanske inte visas förrän efter en timme eller mer. Din IT-administratör bör känna till hur denna token publiceras i en DNS-textpost.
    
    c. När du meddelar [supportteamet för Adobe Sign](https://helpx.adobe.com/in/contact/support.html) via supportbegäran validerar de domänen och lägger till den i ditt konto efter att token har publicerats.
    
    d. Så här publicerar du generellt token i en DNS-post:

    * Logga in på ditt domänkonto
    * Leta reda på sidan för att uppdatera DNS-posten. Den här sidan heter kanske DNS Management (DNS-hantering), Name Server Management (Namnserverhantering) eller Advanced Settings (Avancerade inställningar).
    * Hitta TXT-posterna för din domän.
    * Lägg till en TXT-post med det fullständiga tokenvärde som tillhandahålls av Adobe.
    * Spara ändringarna.

1. Öppna ett nytt webbläsarfönster och logga in på din Adobe Sign-företagswebbplats som administratör.

1. I SAML-menyn väljer du **konto inställningar**  >  **SAML-inställningar**.
   
    ![Skärm bild av sidan Adobe signera SAML-inställningar](./media/adobe-echosign-tutorial/ic789520.png "Konto")

1. I avsnittet **SAML-inställningar** utför du följande steg:
  
   ![Skärm bild av SAML-inställningar](./media/adobe-echosign-tutorial/ic789521.png "SAML-inställningar")
   
   ![Skärm bild av SAML-inställningar](./media/adobe-echosign-tutorial/ic789522.png "SAML-inställningar")

   a. Under **SAML Mode** (SAML-läge) väljer du **SAML Mandatory** (SAML är obligatoriskt).
   
   b. Välj **Allow Echosign Account Administrators to log in using their Echosign Credentials** (Tillåt Echosign-kontoadministratörer att logga in med sina Echosign-autentiseringsuppgifter).
   
   c. Under **User Creation** (Skapande av användare) väljer du **Automatically add users authenticated through SAML** (Lägg automatiskt till användare som autentiseras via SAML).

   d. Klistra in den **Azure AD-identifierare** som du har kopierat från Azure-portalen i textrutan **IdP-entitets-ID**.
    
   e. Klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IdP-inloggnings-URL**.
   
   f. Klistra in den **utloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IdP-utloggnings-URL**.

   ex. Öppna den nedladdade **Certificate(Base64)**-filen i Anteckningar. Kopiera innehållet i den till Urklipp och klistra in det till textrutan **IdP-certifikat**.

   h. Välj **Spara ändringar**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Adobe Sign.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Adobe Sign**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Adobe Sign**.

    ![Adobe Sign-länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adobe-sign-test-user"></a>Skapa Adobe Sign-testanvändare

För att Azure AD-användare ska kunna logga in på Adobe Sign måste de etableras till Adobe Sign. Det här är en manuell uppgift.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för skapande av Adobe Sign-konton som tillhandahålls av Adobe Sign för att etablera Azure AD-användarkonton. 

1. Logga in på din **Adobe Sign**-företagsplats som administratör.

2. I menyn längst upp väljer du **Konto**. I den vänstra rutan väljer **du sedan användare & grupper**  >  **skapa en ny användare**.
   
    ![Skärm bild av Adobe Sign Company-webbplatsen med konto, användare &grupper och skapa en ny användare markerad](./media/adobe-echosign-tutorial/ic789524.png "Konto")
   
3. I avsnittet **Skapa ny användare** utför du följande steg:
   
    ![Skärm bild av avsnittet Skapa ny användare](./media/adobe-echosign-tutorial/ic789525.png "Skapa användare")
   
    a. Skriv **e-postadress**, **förnamn** och **efternamn** för ett giltigt Azure AD-konto som du vill etablera i respektive textrutor.
   
    b. Välj **Skapa användare**.

>[!NOTE]
>Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Adobe Sign-panelen i åtkomstpanelen bör du automatiskt loggas in på Adobe Sign som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

