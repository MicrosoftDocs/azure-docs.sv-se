---
title: 'Självstudie: Azure Active Directory integrering med Carlson Wagonlit Travel | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Carlson Wagonlit Travel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: fccdacce90bec75cd30e5df199310f6b5d569e66
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530617"
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Självstudie: Azure Active Directory integrering med Carlson Wagonlit Travel

I den här självstudien lär du dig att integrera Carlson Wagonlit Travel med Azure Active Directory (Azure AD).
När du integrerar Carlson Wagonlit Travel med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Carlson Wagonlit Travel i Azure AD.
* Du kan låta dina användare loggas in automatiskt på Carlson Wagonlit Travel (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Carlson Wagonlit Travel behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad Carlson Wagonlit Travel-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Carlson Wagonlit Travel har stöd för **IDP**-initierad enkel inloggning

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Lägga till Carlson Wagonlit Travel från galleriet

När du konfigurerar integreringen av Carlson Wagonlit Travel till Azure AD, måste du lägga till Carlson Wagonlit Travel från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till Carlson Wagonlit Travel från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Carlson Wagonlit Travel**. Välj **Carlson Wagonlit Travel** i resultatpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

     ![Carlson Wagonlit Travel i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Carlson Wagonlit Travel baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Carlson Wagonlit Travel upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Carlson Wagonlit Travel, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Carlson Wagonlit Travel](#configure-carlson-wagonlit-travel-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Carlson Wagonlit Travel-testanvändare](#create-carlson-wagonlit-travel-test-user)** – för att ha en motsvarighet till Britta Simon i Carlson Wagonlit Travel som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

För att konfigurera enkel inloggning i Azure AD med Carlson Wagonlit Travel behöver du utföra följande:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering av **Carlson Wagonlit Travel** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Carlson Wagonlit Travel-domän och URL:er med enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du värdet: `cwt-stage`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Carlson Wagonlit Travel** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-carlson-wagonlit-travel-single-sign-on"></a>Konfigurera enkel inloggning i Carlson Wagonlit Travel

För att konfigurera enkel inloggning på **Carlson Wagonlit Travel**-sidan behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Carlson Wagonlit Travel](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till Carlson Wagonlit Travel.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Carlson Wagonlit Travel**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Carlson Wagonlit Travel**.

    ![Carlson Wagonlit Travel-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-carlson-wagonlit-travel-test-user"></a>Skapa Carlson Wagonlit Travel-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Carlson Wagonlit Travel. Kontakta  [Carlson Wagonlit Travel-supportteamet](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html) för att lägga till användarna på Carlson Wagonlit Travel-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Carlson Wagonlit Travel-ikonen i åtkomstpanelen bör du automatiskt loggas in på den Carlson Wagonlit Travel som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
