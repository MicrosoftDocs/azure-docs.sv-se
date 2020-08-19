---
title: 'Självstudie: Azure Active Directory integrering med ENVI MMIS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Envi MMIS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: aababc6ffd84540b99ba68f6bbb449d2aa78ed4b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551752"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Självstudie: Azure Active Directory integrering med ENVI MMIS

I den här självstudien lär du dig att integrera Envi MMIS med Azure Active Directory (AD Azure).
Integreringen av Envi MMIS med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Envi MMIS.
* Du kan göra så att dina användare automatiskt loggas in på Envi MMIS (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Envi MMIS behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Envi MMIS-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Envi MMIS har stöd för **SP-** och **IDP**-initierad enkel inloggning

## <a name="adding-envi-mmis-from-the-gallery"></a>Lägga till Envi MMIS från galleriet

För att konfigurera integrering av Envi MMIS i Azure AD behöver du lägga till Envi MMIS från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Envi MMIS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Envi MMIS**, väljer **Envi MMIS** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Envi MMIS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD till Envi MMIS baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Envi MMIS upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Envi MMIS behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Envi MMIS](#configure-envi-mmis-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Envi MMIS-testanvändare](#create-envi-mmis-test-user)** – för att ha en motsvarighet för Britta Simon i Envi MMIS som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Envi MMIS:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Envi MMIS** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Envi MMIS-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Envi MMIS-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för Envi MMIS](mailto:support@ioscorp.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera Envi MMIS** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-envi-mmis-single-sign-on"></a>Konfigurera enkel inloggning för Envi MMIS

1. I ett annat webbläsarfönster loggar du in på din Envi MMIS-webbplats som administratör.

2. Klicka på fliken **Min domän**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure1.png)

3. Klicka på **Redigera**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure2.png)

4. Markera kryssrutan **Use remote authentication** (Använd fjärrautentisering) och välj sedan **HTTP Redirect** (HTTP-omdirigering) i listrutan **Authentication Type** (Autentiseringstyp).

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure3.png)

5. Välj fliken **Resurser** och klicka sedan på **Upload Metadata** (Ladda upp metadata).

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure4.png)

6. I popup-fönstret **Upload Metadata** (Ladda upp metadata) utför du följande steg:

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure5.png)

    a. Välj alternativet **Fil** i listrutan **Upload From** (Ladda upp från).

    b. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att välja **ikonen Välj fil**.

    c. Klicka på **OK**.

7. När du har laddat upp den nedladdade metadatafilen fylls fälten i automatiskt. Klicka på **Uppdatera**

    ![Konfigurera enkel inloggning – knappen Spara](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Envi MMIS.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Envi MMIS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Envi MMIS**.

    ![Länken för Envi MMIS i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-envi-mmis-test-user"></a>Skapa Envi MMIS-testanvändare

För att göra det möjligt för Azure AD-användare att logga in i Envi MMIS måste de etableras till Envi MMIS. När det gäller Envi MMIS är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Envi MMIS-företagsplats som administratör.

2. Klicka på fliken **Användarlista**.

    ![Lägga till medarbetare](./media/envimmis-tutorial/user1.png)

3. Klicka på knappen **Lägg till användare**.

    ![Lägga till medarbetare](./media/envimmis-tutorial/user2.png)

4. I avsnittet **Lägg till användare** utför du följande steg:

    ![Lägga till medarbetare](./media/envimmis-tutorial/user3.png)

    a. I text rutan **användar namn** anger du användar namnet för Britta Simon-kontot, t. ex. **brittasimon \@ contoso.com**.
    
    b. I textrutan **Förnamn** skriver du förnamnet för BrittaSimon: **Britta**.

    c. I textrutan **Efternamn** skriver du efternamnet för BrittaSimon: **Simon**.

    d. Ange rubriken för användaren i **rubriken** för textrutan.
    
    e. I text rutan **e-postadress** skriver du e-postadressen för Britta Simon-konto som **brittasimon \@ contoso.com**.

    f. Skriv användar **namnet** för Britta Simon-kontot som **brittasimon \@ contoso.com**i text rutan SSO-användarnamn.

    ex. Klicka på **Spara**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Envi MMIS-panelen i åtkomstpanelen bör du automatiskt loggas in på Envi MMIS som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

