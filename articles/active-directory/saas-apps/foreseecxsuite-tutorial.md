---
title: 'Självstudie: Azure Active Directory integrering med förutse CX-paket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och förutse CX-serien.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: fb0ed4a48eaef2168791abbaf94fb8991fa14132
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551120"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Självstudie: Azure Active Directory integrering med förutse CX-paket

I den här självstudien får du lära dig att integrera förutse CX-paket med Azure Active Directory (Azure AD).
Genom att integrera förutse CX Suite med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till förutse CX Suite.
* Du kan låta dina användare vara automatiskt inloggade för att förutse CX-serien (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med en förutse CX-Svit behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Förutse prenumeration för enkel inloggning med CX Suite-paket

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Förutse CX-Svit stöder **SP** -INITIERAd SSO

* Förutse CX-Suite stöder **just-in-Time** User-etablering

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Lägga till förutse CX-svit från galleriet

Om du vill konfigurera integreringen av CX-programpaketet i Azure AD måste du lägga till en förutse CX-svit från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till förutse CX Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **förutse CX Suite**, väljer **förutse CX-Svit** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Förutse CX-Suite i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med en förutse CX-Svit baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i en förutse CX-Svit upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med en förutse CX-Svit måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. Konfigurera de enkla inloggnings inställningarna för **[CX Suite](#configure-foresee-cx-suite-single-sign-on)** på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa förutse CX Suite-testanvändare](#create-foresee-cx-suite-test-user)** – för att få en motsvarighet till Britta Simon i en förutse CX-svit som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med en förutse CX-Svit:

1. I [Azure Portal](https://portal.azure.com/)på sidan för **förutse CX Suite** -programintegration väljer du **enkel inloggning**.

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

    c. När metadatafilen har laddats upp, fylls **ID** -värdet i automatiskt i avsnittet grundläggande SAML-konfiguration.

    ![Förutse information om CX Suite-domän och URL-adresser för enkel inloggning](common/sp-identifier.png)

    a. Skriv en URL i text rutan **inloggnings-URL** : `https://cxsuite.foresee.com/`

    b. I text rutan **identifierare** skriver du en URL med följande mönster: https: \/ /www.okta.com/SAML2/Service-Provider/\<UniqueID>

    > [!Note]
    > Om **ID** -värdet inte får automatiskt polulated fyller du i värdet manuellt enligt mönstret ovan. Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [förutse CX Suite-support teamet](mailto:support@foresee.com) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera förutse CX Suite** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-foresee-cx-suite-single-sign-on"></a>Konfigurera förutsebara CX Suite-inloggning

Om du vill konfigurera enkel inloggning på en **förutse CX Suite-Svit** måste du skicka den hämtade **XML-koden för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till en [förutse CX Suite-support teamet](mailto:support@foresee.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till förutse CX-svit.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **förutse CX-Svit**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **förutse CX Suite**.

    ![Den förutse CX Suite-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-foresee-cx-suite-test-user"></a>Skapa förutse CX Suite-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i förutse CX Suite. Arbeta med [förutse CX Suite-support teamet](mailto:support@foresee.com) för att lägga till användare eller domän som måste läggas till i en lista över tillåtna användare för den förväntade CX Suite-plattformen. Om domänen läggs till av teamet, kommer användarna automatiskt att tillhandahålla den förväntade CX Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för att förutse CX Suite på åtkomst panelen, bör du loggas in automatiskt på den förutse CX-svit som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

