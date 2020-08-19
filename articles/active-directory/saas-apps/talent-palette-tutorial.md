---
title: 'Självstudie: Azure Active Directory-integrering med Talent Palette | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Talent Palette.
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
ms.openlocfilehash: 1b7cfa03aeb705ea7e7caf3254bc39d9fff722ce
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546542"
---
# <a name="tutorial-azure-active-directory-integration-with-talent-palette"></a>Självstudie: Azure Active Directory-integrering med Talent Palette

I den här självstudien lär du dig hur du integrerar Talent Palette med Azure Active Directory (Azure AD).
När du integrerar Talent Palette med Azure AD får du följande fördelar:

* Du kan styra i Azure AD vilka som har åtkomst till Talent Palette.
* Du kan göra så att dina användare automatiskt loggas in på Talent Palette (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Talent Palette behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration aktiverad för enkel inloggning med Talent Palette

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Folloze stöder **IDP**-initierad enkel inloggning
* Folloze stöder **just-in-time**-användaretablering

## <a name="adding-talent-palette-from-the-gallery"></a>Lägga till Talent Palette från galleriet

För att kunna konfigurera integreringen av Talent Palette i Azure AD måste du lägga till Talent Palette från galleriet i listan över hanterade SaaS-appar.

**Gör följande för att lägga till Talent Palette från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select_azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add_new_app.png)

4. Skriv **Talent Palette** i sökrutan, välj **Talent Palette** i resultatpanelen och klick sedan på knappen **Lägg till** för att lägga till programmet.

     ![Talent Palette i resultatlistan](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Talent Palette utifrån en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Talent Palette upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Talent Palette måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Talent Palette ](#configure-talent-palette-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en Talent Palette-testanvändare](#create-talent-palette-test-user)** – för att ha en motsvarighet till Britta Simon i Talent Palette som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Talent Palette:

1. I [Azure-portalen](https://portal.azure.com/), på **Talent Palette**-sidan för appintegrering väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select_sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select_saml_option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit_urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om domän och URL:er för enkel inloggning med Talent Palette](common/both_replyurl.png)

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://talent-p.net/saml/acs/<tenantID>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om domän och URL:er för enkel inloggning med Talent Palette](common/both_signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://talent-p.net/saml/sso/<tenantID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [kundsupporten för Talent Palette](mailto:talent-support@pa-consul.co.jp) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

7. I avsnittet för att **konfigurera Talent Palette** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy_configuration_urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-talent-palette-single-sign-on"></a>Konfigurera enkel inloggning för Talent Palette

För att konfigurera enkel inloggning på **Talent Palette**-sidan behöver du skicka det nedladdade **certifikatet (RAW)** och lämpliga kopierade URL:er från Azure-portalen till [Talent Palette-supporten](mailto:talent-support@pa-consul.co.jp). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new_user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user_properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du så att Britta Simon kan använda enkel inloggning med Azure genom att tilldela åtkomst till Talent Palette.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Talent Palette**.

    ![Bladet Företagsprogram](common/enterprise_applications.png)

2. I listan över program skriver och väljer du **Talent Palette**.

    ![Talent Palette-länken i programlistan](common/all_applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users_groups_blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add_assign_user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-talent-palette-test-user"></a>Skapa Talent Palette-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Talent Palette. Samarbeta med  [Talent Palette-supporten](mailto:talent-support@pa-consul.co.jp) med att lägga till användare på Talent Palette-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Talent Palette-panelen i åtkomstpanelen bör du automatiskt loggas in på Talent Palette som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
