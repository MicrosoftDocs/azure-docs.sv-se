---
title: 'Självstudie: Azure Active Directory integrering med Citrix ShareFile | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix ShareFile.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 0535363165ccda83759a21bff1773280e60fe73c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542606"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Självstudie: Azure Active Directory integrering med Citrix ShareFile

I den här självstudien lär du dig hur du integrerar Citrix ShareFile med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Citrix ShareFile med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Citrix ShareFile.
* Du kan göra så att dina användare automatiskt loggas in på Citrix ShareFile (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna konfigurera Azure AD-integrering med Citrix ShareFile behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Citrix ShareFile-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Citrix ShareFile har stöd för **SP**-initierad enkel inloggning

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Lägga till Citrix ShareFile från galleriet

För att kunna konfigurera integreringen av Citrix ShareFile i Azure AD måste du lägga till Citrix ShareFile från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Citrix ShareFile från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Citrix ShareFile** i sökrutan, välj **Citrix ShareFile** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

     ![Citrix ShareFile i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Citrix ShareFile baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Citrix ShareFile upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Citrix ShareFile måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Citrix ShareFile](#configure-citrix-sharefile-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Citrix ShareFile-testanvändare](#create-citrix-sharefile-test-user)** – så att det finns en motsvarighet till Britta Simon i Citrix ShareFile som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Citrix ShareFile:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **Citrix ShareFile**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Citrix ShareFile – enkel inloggning-information för domän och -URL:er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<tenant-name>.sharefile.com/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster:

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Citrix ShareFile-kundsupporten](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **om att konfigurera Citrix ShareFile** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>Konfigurera enkel inloggning med Citrix ShareFile

1. Öppna ett annat webbläsarfönster och logga in på din **Citrix ShareFile**-företagswebbplats som administratör.

2. Klicka på **Admin** (Administratör) i verktygsfältet högst upp.

3. Välj **Konfigurera enkel inloggning** i det vänstra navigeringsfönstret.
   
    ![Konto administration](./media/sharefile-tutorial/ic773627.png "Konto administration")

4. Gör följande i dialogrutan **Single Sign-On/SAML 2.0 Configuration** (Konfiguration av enkel inloggning/SAML 2.0) under **Basic Settings** (Grundinställningar):
   
    ![Enkel inloggning](./media/sharefile-tutorial/ic773628.png "Enkel inloggning")
   
    a. Klicka på **Aktivera SAML**.
    
    b. I textrutan **Your IDP Issuer/ Entity ID** (Utfärdare av identitetsprovider/entitets-ID) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen.

    c. Klicka på **Ändra** bredvid fältet **X.509-certifikat** och ladda sedan upp certifikatet du har laddat ned från Azure-portalen.
    
    d. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.
    
    e. I text rutan **Logga ut URL** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.

5. Klicka på **Spara** på Citrix ShareFile-hanteringsportalen.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Citrix ShareFile.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Citrix ShareFile**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Citrix ShareFile** i programlistan.

    ![Citrix ShareFile-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-citrix-sharefile-test-user"></a>Skapa Citrix ShareFile-testanvändare

För att kunna göra det möjligt för Azure AD-användare att logga in på Citrix ShareFile måste de etableras i Citrix ShareFile. När det gäller Citrix ShareFile är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Citrix ShareFile**-klient.

2. Klicka på **Hantera användare \> Manage Users Home \> + Create Employee** (Hantera användare > Hantera användares startsida > + Skapa medarbetare).
   
    ![Skapa anställd](./media/sharefile-tutorial/IC781050.png "Skapa anställd")

3. Gör följande i avsnittet **Grundläggande information**:
   
    ![Grundläggande information](./media/sharefile-tutorial/IC799951.png "Grundläggande information")
   
    a. I text rutan **e-postadress** skriver du e-postadressen för Britta Simon som **brittasimon \@ contoso.com**.
   
    b. I textrutan **Förnamn** anger du **förnamnet** på användaren: **Britta**.
   
    c. I textrutan **Efternamn** anger du **efternamnet** på användaren: **Simon**.

4. Klicka på **Lägg till användare**.
  
    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sitt konto innan det blir aktivt. Du kan använda alla anda verktyg eller API:er för att skapa Citrix ShareFile-användarkonton som tillhandahålls av Citrix ShareFile för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Citrix ShareFile-panelen i åtkomstpanelen bör du automatiskt loggas in på Citrix ShareFile som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

