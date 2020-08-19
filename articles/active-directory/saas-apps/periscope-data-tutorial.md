---
title: 'Självstudie: Azure Active Directory integrering med Periscope-data | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Periscope Data.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: ca0bfcb0c90142924285215c427a7071297be730
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553928"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Självstudie: Azure Active Directory integrering med Periscope-data

I den här självstudien lär du dig att integrera Periscope Data med Azure Active Directory (AD Azure).
Integreringen av Periscope Data med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Periscope Data.
* Du kan göra så att dina användare automatiskt loggas in på Periscope Data (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Periscope Data behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Periscope Data-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Periscope Data har stöd för **SP**-initierad enkel inloggning

## <a name="adding-periscope-data-from-the-gallery"></a>Lägga till Periscope Data från galleriet

För att konfigurera integreringen av Periscope Data i Azure AD behöver du lägga till Periscope Data från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Periscope Data från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Periscope Data**, väljer **Periscope Data** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Periscope Data i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Periscope Data baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Periscope Data upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Periscope Data slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Periscope Data](#configure-periscope-data-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Periscope Data-testanvändare](#create-periscope-data-test-user)** – för att ha en motsvarighet för Britta Simon i Periscope Data som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Periscope Data:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Periscope Data** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Periscope Data-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du någon av följande URL:er:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdena med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Periscope Data](mailto:support@periscopedata.com) för att hämta det här värdet och det identifierarvärde som du får från avsnittet **Konfigurera enkel inloggning för Periscope Data**, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurera enkel inloggning för Periscope Data

1. I ett annat webbläsarfönster loggar du in på Periscope Data som administratör.

2. Öppna kugg hjuls menyn längst ned till vänster och öppna menyn **fakturerings**  >  **säkerhet** och utför följande steg. Endast administratörer har åtkomst till de här inställningarna.

    ![Konfigurera information för Periscope Data](./media/periscope-data-tutorial/configure01.png)

    a. Kopiera **URL:en för appfederationsmetadata** från steg 5, **SAML-signeringscertifikat**, och öppna den i en webbläsare. Då öppnas ett XML-dokument.

    b. I textrutan **Enkel inloggning** väljer du **Azure Active Directory**.

    c. Leta upp taggen **SingleSignOnService** och klistra in värdet för **Plats** i textrutan **SSO URL** (URL för enkel inloggning).

    d. Leta upp taggen **SingleLogoutService** och klistra in värdet för **Plats** i textrutan **SLO URL** (URL för enkel utloggning).

    e. Kopiera **ID** -värdet för din instans och klistra in det i en **identifierare (entitets-ID)** i text rutan för det **grundläggande SAML-konfigurations** avsnittet på Azure Portal.

    f. Hitta den första taggen i XML-filen, kopiera värdet för **entityId** och klistra in det i text rutan **utfärdare** .

    ex. Leta upp taggen **IDPSSODescriptor** med SAML-protokoll. I det avsnittet letar du upp taggen **KeyDescriptor** med **use=signing**. Kopiera värdet för **X509Certificate** och klistra in det i textrutan **Certifikat**.

    h. Platser med flera utrymmen kan välja standardutrymmet från listrutan **Default Space** (Standardutrymme). Det här blir det utrymme som nya användare läggs till i när de loggar in på Periscope Data för första gången och etableras via enkel inloggning med Active Directory.

    i. Klicka slutligen på **Spara** och **bekräfta** ändringen av inställningarna för enkel inloggning genom att skriva **Logout** (Logga ut).

    ![Konfigurera information för Periscope Data](./media/periscope-data-tutorial/configure02.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Periscope Data.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Periscope Data**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Periscope Data**.

    ![Länken för Periscope Data i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-periscope-data-test-user"></a>Skapa Periscope Data-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Periscope Data måste de etableras till Periscope Data. I Periscope Data är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Periscope Data som administratör.

2. Klicka på ikonen **Inställningar** nere till vänster på menyn och gå till **Behörigheter**.

    ![Konfigurera information för Periscope Data](./media/periscope-data-tutorial/configure03.png)

3. Klicka på **ADD USER** (Lägg till användare) och utför följande steg:

      ![Konfigurera information för Periscope Data](./media/periscope-data-tutorial/configure04.png)

    a. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    b. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    c. I text rutan **e-postadress** anger du e-postmeddelandet som **brittasimon \@ contoso.com**.

    d. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Periscope Data-panelen i åtkomstpanelen bör du automatiskt loggas in på Periscope Data som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

