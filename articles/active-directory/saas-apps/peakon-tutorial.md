---
title: 'Självstudie: Azure Active Directory integrering med Peakon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Peakon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: a7e385b40911e1adf4e5142de4fb627334baee4a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543760"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Självstudie: Azure Active Directory integrering med Peakon

I den här självstudien får du lära dig hur du integrerar Peakon med Azure Active Directory (Azure AD).
Genom att integrera Peakon med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Peakon.
* Du kan göra det möjligt för användarna att logga in automatiskt till Peakon (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Peakon behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Peakon-aktiverad prenumeration med enkel inloggning

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Peakon stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-peakon-from-the-gallery"></a>Lägga till Peakon från galleriet

Om du vill konfigurera integreringen av Peakon i Azure AD måste du lägga till Peakon från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Peakon från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Peakon**, väljer **Peakon** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Peakon i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Peakon baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Peakon upprättas.

Om du vill konfigurera och testa enkel inloggning med Peakon i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Peakon-enkel inloggning](#configure-peakon-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Peakon test User](#create-peakon-test-user)** – om du vill ha en motsvarighet till Britta Simon i Peakon som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Peakon i Azure AD:

1. Välj **enkel inloggning**på sidan **Peakon** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Peakon-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.peakon.com/saml/<companyid>/assert`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Peakon-domän och URL: er](common/metadata-upload-additional-signon.png)

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.peakon.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren och svars-URL: en som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

7. I avsnittet **Konfigurera Peakon** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-peakon-single-sign-on"></a>Konfigurera Peakon enkel inloggning

1. Logga in på Peakon som administratör i ett annat webbläsarfönster.

2. I meny raden på vänster sida av sidan klickar du på **konfiguration**och navigerar sedan till **integreringar**.

    ![Konfigurationen](./media/peakon-tutorial/tutorial_peakon_config.png)

3. På sidan **integrations** klickar du på **enkel inloggning**.

    ![Den enskilda](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Under avsnittet **enkel inloggning** klickar du på **Aktivera**.

    ![Aktivera](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Utför följande steg på sidan **enkel inloggning för anställda som använder SAML** :

    ![SAML-](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. I text rutan **SSO-inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. I text rutan **URL för SSO-utloggning** klistrar du in värdet för den **utloggnings-URL**som du har kopierat från Azure Portal.

    c. Klicka på **Välj fil** för att ladda upp det certifikat som du har laddat ned från Azure Portal i rutan certifikat.

    d. Klicka på **ikonen** för att kopiera text rutan **entitets-ID** och klistra in i **identifierare** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    e. Klicka på **ikonen** om du vill kopiera text rutan **svars-URL (ACS)** och klistra in i **svars-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    f. Klicka på **Spara**

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Peakon.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Peakon**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Peakon**.

    ![Peakon-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-peakon-test-user"></a>Skapa Peakon test användare

För att göra det möjligt för Azure AD-användare att logga in på Peakon måste de tillhandahållas i Peakon.  
När det gäller Peakon är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Peakon-företags webbplats som administratör.

2. I meny raden på vänster sida av sidan klickar du på **konfiguration**och navigerar sedan till **anställda**.

    ![Den anställda](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Klicka på **Lägg till anställd**på sidans övre högra sida.

      ![Lägg till medarbetare](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Utför följande steg på dialog sidan **ny anställd** :

     ![Den nya medarbetaren](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. I text rutan **namn** skriver du första namnet som **Britta** och efter namn som **Simon**.

    b. I text rutan **e-post** skriver du in e-postadressen som **Brittasimon \@ contoso.com**.

    c. Klicka på **skapa anställd**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Peakon på åtkomst panelen, bör du loggas in automatiskt på den Peakon som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

