---
title: 'Självstudie: Azure Active Directory integration med en genom koppling | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UNIFI.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 27e346dd97824918d859ea6819e3c2b43f4c02aa
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551956"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>Självstudie: Azure Active Directory integrering med en enhetlig

I den här självstudien får du lära dig hur du integrerar UNIFI med Azure Active Directory (AD Azure).
Integreringen av UNIFI med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till UNIFI från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i UNIFI (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med UNIFI behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En UNIFI-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UNIFI stöder **SP- och IDP**-initierad enkel inloggning
* UNIFI stöder **automatisk användaretablering**

## <a name="adding-unifi-from-the-gallery"></a>Lägga till UNIFI från galleriet

För att konfigurera integreringen av UNIFI med Azure AD måste du lägga till UNIFI från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till UNIFI från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **UNIFI**, väljer **UNIFI** från resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![UNIFI i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med UNIFI baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i UNIFI upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med UNIFI måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för UNIFI](#configure-unifi-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa UNIFI-testanvändare](#create-unifi-test-user)** – för att skapa en motsvarighet till Britta Simon i UNIFI som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med UNIFI:

1. Välj **Enkel inloggning** på sidan för programintegrering av **UNIFI** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med UNIFI-domäner och -URL:er](common/idp-identifier.png)

    Skriv webb adressen i text rutan **identifierare** : `INVIEWlabs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.discoverunifi.com/login`

    ![image](common/both-preintegrated-signon.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera UNIFI** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-unifi-single-sign-on"></a>Konfigurera enkel inloggning för UNIFI

1. Öppna ett nytt webbläsarfönster och logga in på din **UNIFI**-företagswebbplats som administratör.

2. Klicka på **Användare**.

    ![Konfigurera enkel inloggning](./media/unifi-tutorial/app1.png)

3. Klicka på **Lägg till ny identitetsprovider**.

    ![Konfigurera enkel inloggning](./media/unifi-tutorial/app2.png)

4. Utför följande steg i avsnittet **Lägg till identitetsprovider**:

    ![Konfigurera enkel inloggning](./media/unifi-tutorial/app3.png) 

    a. Skriv namnet på identitetsprovidern i textrutan **Providernamn**.

    b. I textrutan för **provider-URL** klistrar du in värdet för **Inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. Öppna certifikatet som du laddade ned från Azure-portalen i Anteckningar. Ta bort taggen **---BEGIN CERTIFICATE---** och **---END CERTIFICATE---** och klistra sedan in resten av innehållet i textrutan **Certifikat**.

    d. Markera kryssrutan **Är standardprovider**.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till UNIFI.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **UNIFI**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **UNIFI** i programlistan.

    ![UNIFI-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-unifi-test-user"></a>Skapa UNIFI-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon. **UNIFI** har stöd för automatisk användaretablering, vilket betyder att inga manuella steg krävs. Användare skapas automatiskt efter en lyckad autentisering från Azure AD.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på UNIFI-panelen på åtkomstpanelen bör du automatiskt loggas in i UNIFI-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

