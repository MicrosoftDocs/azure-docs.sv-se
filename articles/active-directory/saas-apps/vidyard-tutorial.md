---
title: 'Självstudie: Azure Active Directory integrering med Vidyard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Vidyard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 2c6b88e9c59fa7195e77275ad193975924df137f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532202"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Självstudie: Azure Active Directory integrering med Vidyard

I den här självstudien får du lära dig hur du integrerar Vidyard med Azure Active Directory (Azure AD).
Genom att integrera Vidyard med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Vidyard.
* Du kan göra det möjligt för användarna att logga in automatiskt till Vidyard (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Vidyard behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Vidyard-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Vidyard stöder **SP** -och **IDP** -initierad SSO

* Vidyard stöder **just-in-Time** User-etablering

## <a name="adding-vidyard-from-the-gallery"></a>Lägga till Vidyard från galleriet

Om du vill konfigurera integreringen av Vidyard i Azure AD måste du lägga till Vidyard från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Vidyard från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Vidyard**, väljer **Vidyard** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Vidyard i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Vidyard baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Vidyard upprättas.

Om du vill konfigurera och testa enkel inloggning med Vidyard i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Vidyard-enkel inloggning](#configure-vidyard-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Vidyard test User](#create-vidyard-test-user)** – om du vill ha en motsvarighet till Britta Simon i Vidyard som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Vidyard i Azure AD:

1. Välj **enkel inloggning**på sidan **Vidyard** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Vidyard-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Vidyard-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du kommer att uppdatera dessa värden med den faktiska identifieraren, svars-URL: en och inloggnings-URL, som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Vidyard** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-vidyard-single-sign-on"></a>Konfigurera Vidyard enkel inloggning

1. Logga in på din Vidyard-program företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **grupp**  >  **säkerhet** från Vidyard-instrumentpanelen

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure1.png)

3. Klicka på fliken **ny profil** .

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure2.png)

4. I avsnittet **SAML-konfiguration** utför du följande steg:

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure3.png)

    a. Ange namn på allmän profil i text rutan **profil namn** .

    b. Kopiera **sid** värde för inloggning på SSO-sidan och klistra in det i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    c. Kopiera **ACS URL** -värde och klistra in det i text rutan för **svars-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    d. Kopiera **utfärdare/metadata URL-** värde och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    e. Öppna den hämtade certifikat filen från Azure Portal i anteckningar och klistra sedan in den i rutan **X. 509-certifikat** .

    f. I text rutan **URL för SAML-slutpunkt** klistrar du in värdet för **inloggnings-URL: en** som kopierats från Azure Portal

    ex. Klicka på **Bekräfta**.

5. På fliken enkel inloggning väljer du **tilldela** bredvid en befintlig profil

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > När du har skapat en SSO-profil tilldelar du den till alla grupper som användare behöver åtkomst via Azure. Om användaren inte finns i den grupp som de har tilldelats, skapar Vidyard automatiskt ett användar konto och tilldelar deras roll i real tid.

6. Välj din organisations grupp, som är synlig i de **grupper som är tillgängliga för tilldelning**.

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure5.png)

7. Du kan se de tilldelade grupperna under de **grupper som för närvarande är tilldelade**. Välj en roll för gruppen per organisation och klicka på **Bekräfta**.

    ![Vidyard-konfiguration](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Mer information finns i [det här dokumentet](https://knowledge.vidyard.com/hc/articles/360009990033-SAML-based-Single-Sign-On-SSO-in-Vidyard).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Vidyard.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Vidyard**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Vidyard**.

    ![Vidyard-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-vidyard-test-user"></a>Skapa Vidyard test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Vidyard. Vidyard stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Vidyard skapas en ny efter autentiseringen.

>[!Note]
>Kontakta [Vidyard support team](mailto:support@vidyard.com)om du behöver skapa en användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Vidyard på åtkomst panelen, bör du loggas in automatiskt på den Vidyard som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

