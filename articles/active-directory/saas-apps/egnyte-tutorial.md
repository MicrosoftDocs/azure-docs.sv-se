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
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519845"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Egnyte

I den här självstudien lär du dig att integrera Egnyte med Azure Active Directory (Azure AD). När du integrerar Egnyte med Azure AD kan du:

* Kontrollera vem som har åtkomst till Egnyte från Azure AD.
* Gör så att dina användare automatiskt loggas in på Egnyte med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Egnyte-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Egnyte har stöd för **SP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-egnyte-from-the-gallery"></a>Lägga till Egnyte från galleriet

För att konfigurera integrering av Egnyte i Azure AD behöver du lägga till Egnyte från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Egnyte** i sökrutan.
1. Välj **Egnyte** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Konfigurera och testa Azure AD SSO för Egnyte

Konfigurera och testa enkel inloggning för Azure AD Form.com med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Form.com.

Utför följande steg för att konfigurera Form.com testa enkel inloggning med Azure AD Form.com:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Enkel inloggning för Egnyte](#configure-egnyte-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Egnyte-testanvändare](#create-egnyte-test-user)** – för att ha en motsvarighet för B.Simon i Egnyte som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för Egnyte** hittar du **avsnittet Hantera** och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.egnyte.com`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdet med den faktiska Sign-On-URL:en och svars-URL:en. Kontakta [supportteamet för Egnyte-klienten](https://www.egnyte.com/corp/contact_egnyte.html) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

5. I avsnittet **Konfigurera Egnyte** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Egnyte.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. Välj **Egnyte** i programlistan.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen &quot;Standardåtkomst&quot;.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name=&quot;configure-egnyte-sso&quot;></a>Konfigurera enkel inloggning för Egnyte

1. I ett annat webbläsarfönster loggar du in på din Egnyte-företagswebbplats som administratör.

2. Klicka på **Inställningar**.
   
    ![Inställningar 1](./media/egnyte-tutorial/settings-tab.png &quot;Inställningar")

3. Klicka på **Inställningar** på menyn.

    ![Meny 1](./media/egnyte-tutorial/menu-tab.png "Meny")

4. Klicka på fliken **Konfiguration** och sedan på **Säkerhet**.

    ![Säkerhet](./media/egnyte-tutorial/configuration.png "Säkerhet")

5. Gör följande i avsnittet **Enkel inloggningsautentisering**:

    ![Autentisering med enkel inloggning](./media/egnyte-tutorial/authentication.png "Autentisering med enkel inloggning")   
    
    a. För **Enkel inloggningsautentisering** väljer du **SAML 2.0**.
   
    b. Som **identitetsprovider** väljer du **AzureAD**.
   
    c. Klistra in **inloggnings-URL:en** som du har kopierat från Azure-portalen till textrutan **Inloggnings-URL för identitetsprovider**.
   
    d. Klistra in **Azure AD-identifieraren** som du har kopierat från Azure-portalen till textrutan för **entitets-ID för identitetsprovider**.
      
    e. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **Identity Provider Certificate** (Certifikat för identitetsprovider).
   
    f. Välj **e-postadress** som **standardanvändarmappning**.
   
    ex. Välj **inaktiverad** för **Use domain-specific issuer value** (Använd domänspecifikt utfärdarvärde).
   
    h. Klicka på **Spara**.

### <a name="create-egnyte-test-user"></a>Skapa Egnyte-testanvändare

För att Azure AD-användare ska kunna logga in på Egnyte måste de etableras till Egnyte. Etablering utförs manuellt med Egnyte.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på **din Egnyte-företagswebbplats** som administratör.

2. Gå till **Settings (Inställningar) \> Users & Groups (Användare och grupper)**.

3. Klicka på **Add New User** (Lägg till ny användare) och välj sedan vilken typ av användare som du vill lägga till.
   
    ![Användare](./media/egnyte-tutorial/add-user.png "Användare")

4. I avsnittet **New Power User** (Ny privilegierad användare) utför du följande steg:
    
    ![Ny standardanvändare](./media/egnyte-tutorial/new-user.png "Ny standardanvändare")   

    a. I **textrutan** E-post anger du e-postadressen för **användaren: Brittasimon \@ contoso.com**.

    b. I textrutan för **användarnamn** anger du användarnamnet för användaren, till exempel **Brittasimon**.

    c. Välj **Single Sign-On** (Enkel inloggning) som **Authentication Type** (Autentiseringstyp).
   
    d. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får en e-postavisering.
    >

>[!NOTE]
>Du kan använda andra verktyg eller API:er för Att skapa Egnyte-användarkonton som tillhandahålls av Egnyte för att etablera Azure AD-användarkonton.
>

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Egnyte inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Egnyte inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Egnyte-panelen i Mina appar omdirigeras detta till Egnyte-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Egnyte kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
