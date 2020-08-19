---
title: 'Självstudie: Azure Active Directory integrering med InstaVR Viewer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och InstaVR Viewer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: ba3c504819eb30da8d21aa47aafc313d16eec51d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550390"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Självstudie: Azure Active Directory integrering med InstaVR Viewer

I den här självstudien får du lära dig hur du integrerar InstaVR Viewer med Azure Active Directory (AD Azure).
Genom att integrera InstaVR Viewer med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vilka som har åtkomst till InstaVR Viewer.
* Du kan göra så att dina användare automatiskt loggas in på InstaVR Viewer (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med InstaVR Viewer behöver du följande saker:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* InstaVR Viewer-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* InstaVR Viewer har stöd för **SP**-initierad enkel inloggning
* InstaVR Viewer stöder **just-in-time**-användaretablering

## <a name="adding-instavr-viewer-from-the-gallery"></a>Lägga till InstaVR Viewer från galleriet

För att konfigurera integreringen av InstaVR Viewer till Azure AD behöver du lägga till InstaVR Viewer från galleriet till listan över hanterade SaaS-appar.

**Lägg till InstaVR Viewer från galleriet med hjälp av följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **InstaVR Viewer**, väljer **InstaVR Viewer** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![InstaVR Viewer i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med InstaVR Viewer baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i InstaVR Viewer upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med InstaVR Viewer behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för InstaVR Viewer](#configure-instavr-viewer-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för InstaVR Viewer ](#create-instavr-viewer-test-user)** – för att ha en motsvarighet för Britta Simon i InstaVR Viewer som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning för Azure AD med InstaVR Viewer med hjälp av följande steg:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **InstaVR Viewer** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med domän och URL:er för InstaVR Viewer](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Det finns inget fast mönster för inloggnings-URL. Den skapas när InstaVR Viewer-kunden utför webbpaketering. Den är unik för varje kund och paket. För att få exakt inloggnings-URL behöver du logga in på InstaVR Viewer-instansen och utföra webbpaketering.

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med det faktiska identifierarvärde enligt beskrivningen senare i den här självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** och **metadatafilen för federation** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadata-certificatebase64.png)

6. I avsnittet **Konfigurera InstaVR Viewer** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-instavr-viewer-single-sign-on"></a>Konfigurera enkel inloggning för InstaVR Viewer

1. Öppna ett nytt webbläsarfönster och logga in på InstaVR Viewer-företagssidan som administratör.

2. Klicka på **användarikonen** och välj **Konto**.

    ![InstaVR Viewer-konfiguration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Rulla ned till **SAML Auth** och utför följande steg:

    ![InstaVR Viewer-konfiguration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. I textrutan **SSO URL** klistrar du värdet för **Inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I textrutan **Utloggnings-URL** klistrar du värdet för **Utloggnings-URL**, som du har kopierat från Azure-portalen.

    c. I textrutan **Entitets-ID** klistrar du värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.

    d. För att ladda upp den nedladdade certifikatfilen klickar du på **Uppdatera**.

    e. För att ladda upp den nedladdade metadatafilen för federation klickar du på **Uppdatera**.

    f. Kopiera värdet för **Entitets-ID** och klistra in det i textrutan **Identifierare (entitets-ID)** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

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

I det här avsnittet ger du Britta Simon möjligheten att använda enkel inloggning för Azure genom att bevilja åtkomst till InstaVR Viewer.

1. På Azure-portalen väljer du **Företagsprogram** följt av **Alla program** och därefter **InstaVR Viewer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **InstaVR Viewer**.

    ![InstaVR Viewer-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-instavr-viewer-test-user"></a>Skapa InstaVR Viewer-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i InstaVR Viewer. InstaVR Viewer stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i InstaVR Viewer skapas en ny efter autentisering. Om du stöter på problem kontaktar du [supportteamet för InstaVR Viewer](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga in på InstaVR Viewer-företagssidan som administratör.

2. Välj **Paket** i den vänstra navigeringspanelen och välj **Make package for Web** (Skapa paket för webben).

    ![InstaVR Viewer-konfiguration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Välj **Hämta**.

    ![InstaVR Viewer-konfiguration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Välj **Open Hosted Page** (Öppna hanterad sida). Efter det sker omdirigering till Azure AD för inloggning.

    ![InstaVR Viewer-konfiguration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Ange dina autentiseringsuppgifter för Azure AD för att logga in med Azure AD via enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
