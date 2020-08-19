---
title: 'Självstudie: Azure Active Directory integrering med Egnyte | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 512b96b6b785c0694bb41fab657f0a45c4321f10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544340"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Egnyte

I den här självstudien får du lära dig hur du integrerar Egnyte med Azure Active Directory (Azure AD). När du integrerar Egnyte med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Egnyte.
* Gör det möjligt för användarna att logga in automatiskt till Egnyte med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Egnyte för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Egnyte stöder **SP**-initierad enkel inloggning
* När du har konfigurerat Egnyte kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Lägga till Egnyte från galleriet

För att konfigurera integrering av Egnyte i Azure AD behöver du lägga till Egnyte från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Egnyte** i sökrutan.
1. Välj **Egnyte** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Egnyte baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Egnyte upprättas.

För att konfigurera och testa enkel inloggning med Azure AD för Egnyte, måste du utföra följande:

Om du vill konfigurera och testa Azure AD SSO med Egnyte, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EGNYTE SSO](#configure-egnyte-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Egnyte test User](#create-egnyte-test-user)** -om du vill ha en motsvarighet till B. Simon i Egnyte som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Egnyte:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Egnyte** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Egnyte-domäner och -URL:er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för Egnyte-klienten](https://www.egnyte.com/corp/contact_egnyte.html) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

5. I avsnittet **Konfigurera Egnyte** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Egnyte.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Egnyte**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Egnyte** i programlistan.

    ![Egnyte-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-egnyte-sso"></a>Konfigurera Egnyte SSO

1. Logga in på din Egnyte-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Inställningar**.
   
    ![Inställningar](./media/egnyte-tutorial/ic787819.png "Inställningar")

3. Klicka på **Inställningar** på menyn.

    ![Inställningar](./media/egnyte-tutorial/ic787820.png "Inställningar")

4. Klicka på fliken **Konfiguration** och sedan på **Säkerhet**.

    ![Säkerhet](./media/egnyte-tutorial/ic787821.png "Säkerhet")

5. Gör följande i avsnittet **Enkel inloggningsautentisering**:

    ![Autentisering med enkel inloggning](./media/egnyte-tutorial/ic787822.png "Autentisering med enkel inloggning")   
    
    a. För **Enkel inloggningsautentisering** väljer du **SAML 2.0**.
   
    b. Som **identitetsprovider** väljer du **AzureAD**.
   
    c. Klistra in **inloggnings-URL:en** som du har kopierat från Azure-portalen till textrutan **Inloggnings-URL för identitetsprovider**.
   
    d. Klistra in **Azure AD-identifieraren** som du har kopierat från Azure-portalen till textrutan för **entitets-ID för identitetsprovider**.
      
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **Identity Provider Certificate ** (Certifikat för identitetsprovider).
   
    f. Välj **e-postadress** som **standardanvändarmappning**.
   
    ex. Välj **inaktiverad** för **Use domain-specific issuer value** (Använd domänspecifikt utfärdarvärde).
   
    h. Klicka på **Spara**.

### <a name="create-egnyte-test-user"></a>Skapa Egnyte-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Egnyte måste de tillhandahållas i Egnyte. Etablering utförs manuellt med Egnyte.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på din **Egnyte** företags webbplats som administratör.

2. Gå till **Settings (Inställningar) \> Users & Groups (Användare och grupper)**.

3. Klicka på **Add New User** (Lägg till ny användare) och välj sedan vilken typ av användare som du vill lägga till.
   
    ![Användare](./media/egnyte-tutorial/ic787824.png "Användare")

4. I avsnittet **New Power User** (Ny privilegierad användare) utför du följande steg:
    
    ![Ny standard användare](./media/egnyte-tutorial/ic787825.png "Ny standard användare")   

    a. I text rutan **e-postadress** anger du e-postmeddelandet som **Brittasimon \@ contoso.com**.

    b. I textrutan för **användarnamn** anger du användarnamnet för användaren, till exempel **Brittasimon**.

    c. Välj **Single Sign-On** (Enkel inloggning) som **Authentication Type** (Autentiseringstyp).
   
    d. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får en e-postavisering.
    >

>[!NOTE]
>Du kan använda andra verktyg för Egnyte av användar konton eller API: er som tillhandahålls av Egnyte för att etablera Azure AD-användarkonton.
>

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Egnyte-panelen i åtkomstpanelen bör du automatiskt loggas in på Egnyte som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
