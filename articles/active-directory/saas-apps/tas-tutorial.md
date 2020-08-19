---
title: 'Självstudie: Azure Active Directory integration med aktivitet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och aktivitet.
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
ms.openlocfilehash: 4aaf4f059976635f32c1a4e6b7130f8835598f98
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552143"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>Självstudie: Azure Active Directory integration med aktivitet

I den här självstudien får du lära dig att integrera aktivitet med Azure Active Directory (Azure AD).
Att integrera aktivitet med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har till gång till uppgifter.
* Du kan göra det möjligt för användarna att vara inloggade automatiskt för att bli inloggad (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integration med aktivitet behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Aktive rad prenumeration för enkel inloggning med aktivitet

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AKTIVITET stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-tas-from-the-gallery"></a>Lägga till en aktivitet från galleriet

Om du vill konfigurera en integrering av en aktivitet i Azure AD måste du lägga till en uppgift från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till uppgifter från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du uppgift, väljer uppgift **från resultat** **panelen och klickar**sedan på **Lägg till** för att lägga till programmet.

     ![AKTIVITET i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med aktivitet baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren.

Om du vill konfigurera och testa enkel inloggning med Azure AD med aktivitet måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för aktivitet](#configure-tas-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa uppgift-testa användare](#create-tas-test-user)** – för att få en motsvarighet till Britta Simon i uppgift som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med aktivitet:

1. På **sidan för program integration** i [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för domän och URL-adresser](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://taseu.combtas.com/<DOMAIN>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://taseu.combtas.com/<ENVIRONMENTNAME>/AssertionService.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för domän och URL-adresser](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://taseu.combtas.com/<DOMAIN>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du kommer att uppdatera dessa med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera aktivitet** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-tas-single-sign-on"></a>Konfigurera enkel inloggning för aktivitet

1. Logga in som administratör i ett annat webbläsarfönster.

2. På vänster sida av menyn klickar du på **Inställningar** och navigerar till **administratör** och klickar sedan på **hantera enkel inloggning**.

    ![Konfiguration av aktivitet](./media/tas-tutorial/configure01.png)

3. Utför följande steg på sidan **hantera enkel inloggning** :

    ![Konfiguration av aktivitet](./media/tas-tutorial/configure02.png)

    a. Skriv ditt miljö namn i text rutan **namn** .
    
    b. Välj **SAML2** som **Autentiseringstyp**.

    c. I text rutan **Ange URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    d. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i rutan **Ange certifiering** .

    e. I text rutan **Ange ny IP** -adress skriver du in IP-adressen.

    >[!NOTE]
    > Kontakta [support teamet](mailto:support@combtas.com) för att hämta IP-adressen.

    f. Kopiera URL: en för **enkel inloggning** och klistra in den i ID-rutan **(entitets-ID)** och **Logga in URL** för **grundläggande SAML-konfiguration** i Azure Portal. Observera att URL: en är Skift läges känslig och måste sluta med ett snedstreck (/).

    ex. Kopiera **bekräftelse tjänstens** URL på installations sidan och klistra in den i text rutan **SVARs-URL** i den  **grundläggande SAML-konfigurationen** i Azure Portal.

    h. Klicka på **Infoga SSO-rad**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till uppgifter.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **aktivitet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **aktivitet**.

    ![Länken aktivitet i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tas-test-user"></a>Skapa uppgift test användare

I det här avsnittet skapar du en användare som kallas Britta Simon i aktivitet. Samar beta med [support teamet](mailto:support@combtas.com) för att lägga till användarna på den andra plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen uppgifter i åtkomst panelen, bör du loggas in automatiskt till den aktivitet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

