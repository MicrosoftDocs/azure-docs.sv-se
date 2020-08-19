---
title: 'Självstudie: Azure Active Directory integrering med BitaBIZ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BitaBIZ.
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
ms.openlocfilehash: 397197c2ab3ba4f135912eab800f1abd7ab73a0f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531143"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Självstudie: Azure Active Directory integrering med BitaBIZ

I den här självstudien lär du dig att integrera BitaBIZ med Azure Active Directory (AD Azure).
När du integrerar BitaBIZ med Azure AD innebär det följande fördelar:

* Du kan styra i vem som har åtkomst till BitaBIZ från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i BitaBIZ (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med BitaBIZ behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* BitaBIZ-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* BitaBIZ stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-bitabiz-from-the-gallery"></a>Lägga till BitaBIZ från galleriet

För att konfigurera integrering av BitaBIZ i Azure AD måste du lägga till BitaBIZ från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BitaBIZ från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **BitaBIZ**, väljer **BitaBIZ** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![BitaBIZ i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med BitaBIZ baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i BitaBIZ upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med BitaBIZ måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för BitaBIZ](#configure-bitabiz-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa BitaBIZ-testanvändare](#create-bitabiz-test-user)** – för att ha en motsvarighet för Britta Simon i BitaBIZ som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med BitaBIZ:

1. På [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **BitaBIZ** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om enkel inloggning med BitaBIZ-domän och URL:er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL med följande mönster:  `https://www.bitabiz.com/<instanceId>`

    > [!NOTE]
    > Värdet i URL:en ovan är bara exempel. Uppdatera värdet med den faktiska identifieraren. Detta förklaras senare i självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://www.bitabiz.com/dashboard`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera BitaBIZ** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bitabiz-single-sign-on"></a>Konfigurera enkel inloggning för BitaBIZ

1. Logga in till din BitaBIZ-klient som administratör i ett annat webbläsarfönster.

2. Klicka på **ADMINISTRATÖRSINSTALLATION**.

    ![BitaBIZ-konfiguration](./media/bitabiz-tutorial/settings1.png)

3. Klicka på **Microsoft integrations** (Microsoft-integrationer) under avsnittet **Lägg till värde**.

    ![BitaBIZ-konfiguration](./media/bitabiz-tutorial/settings2.png)

4. Rulla ned till avsnittet **Microsoft Azure AD (Enable single sign on)** (Microsoft Azure AD (aktivera enkel inloggning)) och utför följande steg:

    ![BitaBIZ-konfiguration](./media/bitabiz-tutorial/settings3.png)

    a. Kopiera värdet från textrutan **Entity ID (”Identifier” in Azure AD)** (Enhets-ID (”Identifierare” i Azure AD)) och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. 

    b. I textrutan **Azure AD Single Sign-On Service URL** (tjänst-URL för Azure AD enkel inloggning) klistrar du in **Inloggnings-URL** som du kopierade från Azure-portalen.

    c. I textrutan **Azure AD SAML Entity ID** (Entitets-ID för Azure AD SAML) klistrar du in värdet för **Azure Ad-identifieraren**, som du har kopierat från Azure-portalen.

    d. Öppna den nedladdade filen **Certificate(Base64)** i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **Azure AD Signing Certificate (Base64 encoded)** (Azure AD-signeringscertifikat (Base64-kodat)).

    e. Lägg till företagets e-postdomännamn, det vill säga mycompany.com, i textrutan **Domännamn** för att tilldela användare i företaget den här e-postdomänen (EJ OBLIGATORISKT).

    f. Markera **SSO-aktiverat** för BitaBIZ-kontot.

    ex. Klicka på **Spara Azure AD-konfiguration** för att spara och aktivera SSO-konfigurationen.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till BitaBIZ.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **BitaBIZ**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **BitaBIZ**.

    ![Länken BitaBIZ i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bitabiz-test-user"></a>Skapa BitaBIZ-testanvändare

För att Azure AD-användare ska kunna logga in i BitaBIZ måste de vara etablerade i BitaBIZ.  
I BitaBIZ är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på BitaBIZ-företagswebbplatsen som administratör.

2. Klicka på **ADMINISTRATÖRSINSTALLATION**.

    ![BitaBIZ – Lägg till användare](./media/bitabiz-tutorial/settings1.png)

3. Klicka på **Lägg till användare** under avsnittet **Organisation**.

    ![BitaBIZ – Lägg till användare](./media/bitabiz-tutorial/user1.png)

4. Klicka på **Add new employee** (Lägg till ny medarbetare).

    ![BitaBIZ – Lägg till användare](./media/bitabiz-tutorial/user2.png)

5. Utför följande steg i dialogrutan **Add new employee** (Lägg till ny medarbetare):

    ![BitaBIZ – Lägg till användare](./media/bitabiz-tutorial/user3.png)

    a. Skriv det första namnet på användaren som Britta i text rutan för **förnamn** .

    b. I text rutan **efter namn** skriver du det senaste namnet på användaren som Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. Välj ett datum i **Date of employment** (Anställningsdatum).

    e. Det finns andra icke-obligatoriska användarattribut som kan ställas in för användaren. Läs [dokumentationen för medarbetarkonfiguration](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) för mer information.

    f. Click **Save employee** (Spara medarbetare).

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på BitaBIZ-panelen i åtkomstpanelen bör du automatiskt loggas in på BitaBIZ som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
