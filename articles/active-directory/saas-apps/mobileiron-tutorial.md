---
title: 'Självstudie: Azure Active Directory integrering med MobileIron | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 45ad613fd250f905dd1cd5e14b5c8aeb42896a51
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554670"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Självstudie: Azure Active Directory integrering med MobileIron

I den här självstudien lär du dig att integrera MobileIron med Azure Active Directory (Azure AD).
Genom att integrera MobileIron med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till MobileIron från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på MobileIron (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med MobileIron behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* MobileIron-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MobileIron har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-mobileiron-from-the-gallery"></a>Lägga till MobileIron från galleriet

För att konfigurera integrering av MobileIron i Azure AD behöver du lägga till MobileIron från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till MobileIron från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **MobileIron** i sökrutan, välj **MobileIron** i resultatpanelen och klicka på knappen **Lägg till** för att lägga till programmet.

     ![MobileIron i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med MobileIron baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i MobileIron upprättas.

Om du vill kunna konfigurera och testa enkel inloggning med Azure AD med hjälp av MobileIron, behöver du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning på MobileIron](#configure-mobileiron-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MobileIron-testanvändare](#create-mobileiron-test-user)** – för att ha en motsvarighet för Britta Simon i MobileIron som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med hjälp av MobileIron:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **MobileIron**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP**-initierat läge:

    ![Information om enkel inloggning med MobileIron-domäner och URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://www.mobileiron.com/<key>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med MobileIron-domäner och URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får du värdena för nyckeln och värden från den administrativa MobileIron-portalen, som beskrivs senare i självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Konfigurera enkel inloggning med MobileIron

1. Logga in som administratör på företagswebbplatsen för MobileIron i ett annat webbläsarfönster.

2. Gå till **Administratörs**  >  **identitet** och välj **AAD** -alternativ i fältet **information om Cloud IDP-inställningar** .

    ![Konfigurera knapp för enkel administratörsinloggning](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Kopiera värdena för **nyckel** och **värd** och klistra in dem så att webbadresserna i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen blir kompletta.

    ![Konfigurera knapp för enkel administratörsinloggning](./media/mobileiron-tutorial/key.png)

4. I fältet **Exportera metadatafil från AAD och importera till MobileIron-moln** klickar du på **Välj fil** för att ladda upp hämtade metadata från Azure-portalen. Klicka på **Klar** när överföringen har slutförts.

    ![Konfigurera metadataknapp för enkel administratörsinloggning](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till MobileIron.

1. Välj **Företagsprogram** i Azure-portalen, välj **Alla program**, och välj sedan **MobileIron**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **MobileIron**.

    ![MobileIron-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mobileiron-test-user"></a>Skapa MobileIron-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på MobileIron måste de först etableras i MobileIron.  
När det gäller MobileIron är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in som administratör på företagswebbplatsen för MobileIron.

1. Gå till **Användare** och klicka på **Lägg till** > **enskild användare**.

    ![Konfigurera knapp för enkel användarinloggning](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Utför följande steg på dialogsidan **”Enkel användare”**:

    ![Konfigurera knapp för tillägg av användare för enkel inloggning](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. I text rutan e-postadress anger du e- **postadressen** till användaren brittasimon@contoso.com .

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. Britta.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som Simon.

    d. Klicka på **Klar**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på MobileIron-panelen i åtkomstpanelen så borde du automatiskt loggas in på den MobileIron som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

