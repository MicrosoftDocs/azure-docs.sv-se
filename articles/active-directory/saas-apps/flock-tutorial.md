---
title: 'Självstudie: Azure Active Directory integrering med flock | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Flock.
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
ms.openlocfilehash: c14d70f7489115b980479e7225cc3b47f80b3f2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554977"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Självstudie: Azure Active Directory integrering med flock

I den här självstudien lär du dig att integrera Flock med Azure Active Directory (Azure AD).
När du integrerar Flock med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Flock i Azure AD.
* Du kan låta dina användare loggas in automatiskt på Flock (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Flock behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad Flock-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Flock har stöd för **SP**-initierad enkel inloggning

## <a name="adding-flock-from-the-gallery"></a>Lägga till Flock från galleriet

När du konfigurerar integreringen av Flock till Azure AD, måste du lägga till Flock från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till Flock från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Flock**, väljer **Flock** i resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Flock i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Flock baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Flock upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Flock, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Flock](#configure-flock-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Flock-testanvändare](#create-flock-test-user)** – för att ha en motsvarighet till Britta Simon i Flock som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Flock:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Flock** i [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Flock-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.flock.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Flock-klientens supportteam](mailto:support@flock.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Flock** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-flock-single-sign-on"></a>Konfigurera enkel inloggning i Flock

1. Logga in i ett annat webbläsarfönster på Flock-företagswebbplatsen som administratör.

2. Välj fliken **Autentisering** i den vänstra navigeringspanelen och välj sedan **SAML-autentisering**.

    ![Flock-konfiguration](./media/flock-tutorial/configure1.png)

3. Gör följande i avsnittet **SAML-autentisering**:

    ![Flock-konfiguration](./media/flock-tutorial/configure2.png)

    a. I textrutan **SAML 2.0-slutpunkt (HTTP)** klistrar du in värdet för den **inloggnings-URL** som du kopierade från Azure-portalen.

    b. I textrutan **Identifiera providerutgivare** klistrar du in värdet för den **Azure AD-identifierare** som du kopierade från Azure-portalen.

    c. Öppna det nedladdade **certifikatet (Base64)** från Azure-portalen i Anteckningar och klistra in innehållet i textrutan **Offentligt certifikat**.

    d. Klicka på **Spara**.

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

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till Flock.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Flock**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Flock**.

    ![Flock-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-flock-test-user"></a>Skapa Flock-testanvändare

Azure AD-användarna måste vara etablerade i Flock för att kunna logga in. När det gäller Flock är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Flock-företagswebbplatsen som administratör.

2. Klicka på **Hantera team** i det vänstra navigeringsfönstret.

    ![Lägga till medarbetare](./media/flock-tutorial/user1.png)

3. Klicka på fliken **Lägg till medlem** och välj sedan **Teammedlemmar**.

    ![Lägga till medarbetare](./media/flock-tutorial/user2.png)

4. Ange e-postadressen till användaren som **Brittasimon \@ contoso.com** och välj sedan **Lägg till användare**.

    ![Lägga till medarbetare](./media/flock-tutorial/user3.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Flock-ikonen i åtkomstpanelen bör du automatiskt loggas in på den Flock som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

