---
title: 'Självstudie: Azure Active Directory integrering med M-Files | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 77c83e82577cb1d6d07c90a0260a5fa006fd8625
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554853"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Självstudie: Azure Active Directory integrering med M-Files

I den här självstudien lär du dig att integrera M-Files med Azure Active Directory (AD Azure).
Integreringen av M-Files med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till M-Files.
* Du kan göra så att dina användare loggas in automatiskt på M-Files (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med M-Files behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* M-Files-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* M-Files har stöd för **SP**-initierad enkel inloggning

## <a name="adding-m-files-from-the-gallery"></a>Lägga till M-Files från galleriet

För att konfigurera integreringen av M-Files i Azure AD måste du lägga till M-Files från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till M-Files från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **M-Files**, väljer **M-Files** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![M-Files i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med M-Files baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i M-Files upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med M-Files behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för M-Files](#configure-m-files-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa M-Files-testanvändare](#create-m-files-test-user)** – för att ha en motsvarighet för Britta Simon i M-Files som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för M-Files:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **M-Files** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![M-Files-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för M-Files](mailto:support@m-files.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera M-Files** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-m-files-single-sign-on"></a>Konfigurera enkel inloggning för M-Files

1. För att få enkel inloggning konfigurerat för ditt program kontaktar du [supportteamet för M-Files](mailto:support@m-files.com) och ge dem nedladdade metadata.
   
    >[!NOTE]
    >Följ stegen nedan om du vill konfigurera enkel inloggning för M-Files-skrivbordsprogrammet. Det krävs inga fler steg om du bara vill konfigurera enkel inloggning för webbversionen av M-Files.  

1. Följ stegen nedan om du vill konfigurera M-Files-skrivbordsprogrammet för att aktivera enkel inloggning med Azure AD. För att ladda ned M-Files går du till sidan för [nedladdning av M-Files](https://www.m-files.com/en/download-latest-version).

1. Öppna fönstret **M-Files Desktop Settings** (M-Files-skrivbordsinställningar). Klicka sedan på **Lägg till**.
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_10.png)

1. I fönstret **Document Vault Connection Properties** (Anslutningsegenskaper för Document Vault) utför du följande steg:
   
    ![Konfigurera enkel inloggning](./media/m-files-tutorial/tutorial_m_files_11.png)  

    Under avsnittet Server skriver du värdena på följande sätt:  

    a. För **Användarnamn** skriver `<tenant-name>.cloudvault.m-files.com`. 
 
    b. För **Portnummer** skriver du **4466**. 

    c. För **Protokoll** väljer du **HTTPS**. 

    d. I fältet **Autentisering** väljer du **Specific Windows user** (Specifik Windows-användare). Då visas en inloggningssida. Infoga dina autentiseringsuppgifter för Azure AD. 

    e. För **valvet på servern** väljer du motsvarande valv på servern.
 
    f. Klicka på **OK**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till M-Files.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **M-Files**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **M-Files**.

    ![Länken för M-Files i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-m-files-test-user"></a>Skapa testanvändare för M-Files

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i M-Files. Ta hjälp av [supportteamet för M-Files](mailto:support@m-files.com) för att lägga till användarna i M-Files.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på M-Files-panelen i åtkomstpanelen bör du automatiskt loggas in på M-Files som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

