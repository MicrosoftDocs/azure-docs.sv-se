---
title: 'Självstudie: Azure Active Directory integrering med värdbaserat grafsystem | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hosted Graphite.
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
ms.openlocfilehash: b7aaaa589b92814694bee2838332f6823b3351de
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521920"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Självstudie: Azure Active Directory integrering med värdbaserat grafsystem

I den här självstudien lär du dig att integrera Hosted Graphite med Azure Active Directory (Azure AD).
När du integrerar Hosted Graphite med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Hosted Graphite från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på Hosted Graphite (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Hosted Graphite behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Hosted Graphite-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Hosted Graphite har stöd för **SP- och IDP**-initierad enkel inloggning
* Hosted Graphite har stöd för **Just-in-time**-användaretablering

## <a name="adding-hosted-graphite-from-the-gallery"></a>Lägga till Hosted Graphite från galleriet

För att konfigurera integreringen av Hosted Graphite i Azure AD måste du lägga till Hosted Graphite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hosted Graphite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Hosted Graphite**, väljer **Hosted Graphite** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Hosted Graphite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Hosted Graphite baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Hosted Graphite upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Hosted Graphite måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Hosted Graphite](#configure-hosted-graphite-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Hosted Graphite-testanvändare](#create-hosted-graphite-test-user)** – för att ha en motsvarighet för Britta Simon i Hosted Graphite som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Hosted Graphite:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Hosted Graphite** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Hosted Graphite-domän och -URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Hosted Graphite-domän och -URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL. För att få de här värdena kan du gå till Åtkomst->SAML-konfiguration på programsidan eller kontakta [Hosted Graphite-supportteamet](mailto:help@hostedgraphite.com).

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Hosted Graphite** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hosted-graphite-single-sign-on"></a>Konfigurera enkel inloggning med Hosted Graphite

1. Logga in på din Hosted Graphite-klientorganisation som administratör.

2. Gå till sidan **SAML-konfiguration** i sidopanelen (**Åtkomst -> SAML-konfiguration**).

    ![Konfigurera enkel inloggning på appsidan](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Bekräfta att dessa URL:er matchar konfigurationen i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    ![Konfigurera enkel inloggning på appsidan](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. I textrutorna för **entitets- eller utfärdar-ID** och **inloggnings-URL för SSO** klistrar du in det värde för **Azure AD-identifierare** och **Inloggnings-URL** som du har kopierat från Azure-portalen.

    ![Konfigurera enkel inloggning på appsidan](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Välj **Skrivskyddad** som **Standardanvändarroll**.

    ![Konfigurera enkel inloggning på appsidan](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **X.509 Certificate** (X.509-certifikat).

    ![Konfigurera enkel inloggning på appsidan](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Klicka på knappen **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Hosted Graphite.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **Hosted Graphite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Hosted Graphite**.

    ![Hosted Graphite-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hosted-graphite-test-user"></a>Skapa Hosted Graphite-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Hosted Graphite. Hosted Graphite har stöd för just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Hosted Graphite skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta Hosted Graphite-supportteamet via <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Hosted Graphite-panelen i åtkomstpanelen bör du automatiskt loggas in på Hosted Graphite som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

