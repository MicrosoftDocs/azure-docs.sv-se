---
title: 'Självstudie: Azure Active Directory integrering med arbets plats för AutoTask | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Autotask Workplace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2019
ms.author: jeedes
ms.openlocfilehash: 8c7e5699930b633d900b2b31b0fe29fbd8505944
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550205"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>Självstudie: Azure Active Directory integrering med arbets plats för AutoTask

I den här självstudien lär du dig att integrera Autotask Workplace med Azure Active Directory (Azure AD).
Integreringen av Autotask Workplace med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Autotask Workplace.
* Du kan göra så att dina användare automatiskt loggas in på Autotask Workplace (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med Autotask Workplace:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Autotask Workplace-prenumeration med enkel inloggning aktiverat
* En Autotask Workplace-prenumeration med enkel inloggning aktiverat
* Du måste vara administratör eller superadministratör i Workplace.
* Du måste ha ett administratörskonto i Azure AD.
* De användare som ska använda den här funktionen måste ha konton i Workplace och Azure AD, och deras e-postadresser måste matcha för båda.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Autotask Workplace har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-autotask-workplace-from-the-gallery"></a>Lägga till Autotask Workplace från galleriet

För att konfigurera integrering av Autotask Workplace med Azure AD behöver du lägga till Autotask Workplace från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Autotask Workplace från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Autotask Workplace**, väljer **Autotask Workplace** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Autotask Workplace i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Autotask Workplace baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Autotask Workplace upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Autotask Workplace:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Autotask Workplace](#configure-autotask-workplace-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Autotask Workplace-testanvändare](#create-autotask-workplace-test-user)** – för att ha en motsvarighet till Britta Simon i Autotask Workplace som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Autotask Workplace:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Autotask Workplace** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Autotask Workplace-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Autotask Workplace-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.awp.autotask.net/loginsso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Autotask Workplace** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-autotask-workplace-single-sign-on"></a>Konfigurera enkel inloggning för Autotask Workplace

1. I ett annat webbläsarfönster loggar du in på Workplace Online med autentiseringsuppgifter för administratör.

    > [!Note]
    > När du konfigurerar IdP måste en underdomän anges. För att fastställa rätt underdomän loggar du in på Workplace Online. När du loggat in bör du anteckna underdomänen i URL:en. Underdomänen är delen mellan ”https://” och ”.awp.autotask.net/” och ska vara us, eu, ca eller au.

2. Gå till **konfiguration**  >  **enkel inloggning** och utför följande steg:

    ![Konfiguration av enkel inloggning för Autotask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)

    a. Välj alternativet **XML-metadatfil** och ladda sedan upp den nedladdade **XML för federationsmetadata** från Azure-portalen.

    b. Klicka på **AKTIVERA ENKEL INLOGGNING**.

    ![Konfiguration för godkännande av enkel inloggning för Autotask](./media/autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. Markera kryssrutan **I confirm this information is correct and I trust this IdP** (Jag bekräftar att denna information är korrekt och jag litar på denna IdP).

    d. Klicka på **GODKÄNN**.

> [!Note]
> Om du behöver hjälp med att konfigurera Autotask Workplace läser du [den här sidan](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) för att få hjälp med ditt Workplace-konto.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Autotask Workplace.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Autotask Workplace**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Autotask Workplace**.

    ![Länken för Autotask Workplace i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-autotask-workplace-test-user"></a>Skapa Autotask Workplace-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Autotask Workplace. Ta hjälp från [supportteamet för Autotask Workplace](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm) och lägg till användarna på Autotask Workplace-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Autotask Workplace-panelen i åtkomstpanelen bör du automatiskt loggas in på Autotask Workplace som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
