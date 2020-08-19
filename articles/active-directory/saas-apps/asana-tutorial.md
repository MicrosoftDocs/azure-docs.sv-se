---
title: 'Självstudie: Azure Active Directory integrering med asana | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Asana.
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
ms.openlocfilehash: dd2c02f25584fd32acd32277a1dd2300c6a98e73
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549576"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Självstudie: Azure Active Directory integrering med asana

I den här självstudien får du lära dig hur du integrerar Asana med Azure Active Directory (Azure AD).
När du integrerar Asana med Azure AD så får du följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till Asana.
* Du kan låta dina användare loggas in automatiskt på Asana (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Asana så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Asana-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Asana stöder **SP**-initierad enkel inloggning

* Asana stöder [**automatisk** användaretablering](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Lägga till Asana från galleriet

Om du vill konfigurera integrering av Asana i Azure AD så behöver du lägga till Asana från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Asana från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du in **Asana**, väljer **Asana** från resultatpanelen och klickar därefter på **Lägg till** för att lägga till programmet.

     ![Asana i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Asana baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Asana upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Asana så måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Asana](#configure-asana-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Asana-testanvändare](#create-asana-test-user)** – för att få en motpart för Britta Simon i Asana som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Asana genom att utföra följande steg:

1. I [Azure-portalen](https://portal.azure.com/) på programintegreringssidan för **Asana** så väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning på Asana-domäner och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du URL:en: `https://app.asana.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://app.asana.com/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Asana** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-asana-single-sign-on"></a>Konfigurera enkel inloggning i Asana

1. Logga in i ditt Asana-program i en annan webbläsare. Om du vill konfigurera enkel inloggning i Asana så öppnar du arbetsyteinställningarna genom att klicka på arbetsytans namn i det övre högra hörnet på skärmen. Klicka sedan på ** \<your workspace name\> Inställningar**.

    ![Inställningar för enkel inloggning i Asana](./media/asana-tutorial/tutorial_asana_09.png)

2. I fönstret **Organisationsinställningar** så klickar du på **Administration**. Klicka därefter på **Medlemmar måste logga in via SAML** för att aktivera konfigurationen för enkel inloggning. Utför följande steg:

    ![Konfigurera inställningar för enkel inloggningsorganisation](./media/asana-tutorial/tutorial_asana_10.png)  

    a. I textrutan **URL för inloggningssida** så klistrar du in **inloggnings-URL**.

    b. Högerklicka på det certifikat som hämtats från Azure-portalen och öppna sedan certifikatfilen med Anteckningar eller din önskade textredigerare. Kopiera innehållet mellan start- och slutcertifikatets titel och klistra in det i textrutan **X.509-certifikat**.

3. Klicka på **Spara**. Gå till [Asana-guide för att konfigurera enkel inloggning](https://asana.com/guide/help/premium/authentication#gl-saml) om du behöver ytterligare hjälp.

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

I det här avsnittet så låter du Britta Simon använda Azure enkel inloggning genom att ge åtkomst till Asana.

1. I Azure-portalen så väljer du **Företagsprogram**, **Alla program** och därefter **Asana**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Asana**.

    ![Asana-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-asana-test-user"></a>Skapa en Asana-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Asana. Asana stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](asana-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

I det här avsnittet skapar du en användare som heter Britta Simon i Asana.

1. I **Asana** så går du till **Team**-avsnittet i den vänstra panelen. Klicka på plustecken-knappen.

    ![Skapa en testanvändare för Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Skriv e-postadressen till användaren som **Britta. simon \@ contoso.com** i text rutan och välj sedan **Bjud in**.

3. Klicka på **Skicka inbjudan**. Den nya användaren får ett e-postmeddelande på sitt e-postkonto. användaren måste skapa och validera kontot.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Asana-panelen i åtkomstpanelen så bör du automatiskt loggas in på den Asana som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användar etablering](asana-provisioning-tutorial.md)
