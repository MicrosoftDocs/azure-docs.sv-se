---
title: 'Självstudie: Azure Active Directory integrering med Heroku | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Heroku.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 11cdffd7002be8c55f648af41fccf7fff4ff001a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550724"
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Självstudie: Azure Active Directory integrering med Heroku

I den här självstudien lär du dig att integrera Heroku med Azure Active Directory (Azure AD).
När du integrerar Heroku med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Heroku i Azure AD.
* Du kan låta dina användare loggas in automatiskt på Heroku (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Heroku behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad Heroku-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Heroku stöder **IDP**-initierad enkel inloggning
* Heroku har stöd för etablering av användare enligt principen **just-in-time**

## <a name="adding-heroku-from-the-gallery"></a>Lägga till Heroku från galleriet

När du konfigurerar integreringen av Heroku i Azure AD, måste du lägga till Heroku från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till Heroku från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Heroku**, väljer **Heroku** i resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Heroku i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Heroku baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Heroku upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Heroku, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Heroku](#configure-heroku-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Heroku-testanvändare](#create-heroku-test-user)** – för att ha en motsvarighet till Britta Simon i Heroku som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Heroku:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Heroku** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Heroku-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://sso.heroku.com/saml/<company-name>/init`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Du kan hämta dessa värden från Heroku-teamet, som beskrivs i senare avsnitt av den här artikeln.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Heroku** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-heroku-single-sign-on"></a>Konfigurera enkel inloggning i Heroku

1. Logga in på din Heroku-klient som administratör i ett annat webbläsarfönster.

2. Klicka på fliken **Settings** (Inställningar).

3. På **sidan Enkel inloggning** klickar du på **Ladda upp metadata**.

4. Ladda upp metadatafilen som du laddade ned från Azure-portalen.

5. När installationen är klar visas en bekräftelsedialogruta för administratörer och URL:en för enkel inloggning visas för slutanvändarna.

6. Kopiera **Herokus inloggnings-URL** och **Herokus entitets-ID** och gå tillbaka till **Grundläggande SAML-konfiguration** i Azure-portalen. Klistra in dessa värden i textrutorna **Inloggnings-URL** respektive **Identifierare (Entitets-ID)**.

    ![Konfigurera enkel inloggning](./media/heroku-tutorial/tutorial_heroku_52.png)

7. Klicka på **Nästa**.

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

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till Heroku.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Heroku**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Heroku**.

    ![Heroku-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-heroku-test-user"></a>Skapa Heroku-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Heroku. Heroku stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid åtkomst till Heroku om användaren inte finns ännu. När kontot har etablerats får användaren ett bekräftelsemeddelande och måste då klicka på bekräftelselänken.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Heroku-klienten](https://www.heroku.com/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Heroku-ikonen i åtkomstpanelen bör du automatiskt loggas in på den Heroku som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

