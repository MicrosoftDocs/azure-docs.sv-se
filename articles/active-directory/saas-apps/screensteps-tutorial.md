---
title: 'Självstudie: Azure Active Directory integrering med ScreenSteps | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScreenSteps.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 8f23f9763d25d5edfebd0e4cff7f3b7772ef1554
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543373"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Självstudie: Azure Active Directory integrering med ScreenSteps

I den här självstudien får du lära dig hur du integrerar ScreenSteps med Azure Active Directory (Azure AD).
Genom att integrera ScreenSteps med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ScreenSteps.
* Du kan göra det möjligt för användarna att logga in automatiskt till ScreenSteps (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ScreenSteps behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ScreenSteps stöder **SP** -INITIERAd SSO

## <a name="adding-screensteps-from-the-gallery"></a>Lägga till ScreenSteps från galleriet

Om du vill konfigurera integreringen av ScreenSteps i Azure AD måste du lägga till ScreenSteps från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till ScreenSteps från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **ScreenSteps**, väljer **ScreenSteps** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![ScreenSteps i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med ScreenSteps baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i ScreenSteps upprättas.

Om du vill konfigurera och testa enkel inloggning med ScreenSteps i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ScreenSteps-enkel inloggning](#configure-screensteps-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ScreenSteps test User](#create-screensteps-test-user)** – om du vill ha en motsvarighet till Britta Simon i ScreenSteps som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med ScreenSteps i Azure AD:

1. Välj **enkel inloggning**på sidan **ScreenSteps** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för ScreenSteps-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska inloggnings-URL: en som beskrivs senare i den här självstudien.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera ScreenSteps** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-screensteps-single-sign-on"></a>Konfigurera ScreenSteps enkel inloggning

1. Logga in på din ScreenSteps-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **konto inställningar**.

    ![Konto hantering](./media/screensteps-tutorial/ic778523.png "Kontohantering")

1. Klicka på **enkel inloggning**.

    ![Fjärrautentisering](./media/screensteps-tutorial/ic778524.png "Fjärrautentisering")

1. Klicka på **skapa slut punkt för enkel inloggning**.

    ![Fjärrautentisering](./media/screensteps-tutorial/ic778525.png "Fjärrautentisering")

1. I avsnittet **skapa enkel inloggnings slut punkt** utför du följande steg:

    ![Skapa en slut punkt för autentisering](./media/screensteps-tutorial/ic778526.png "Skapa en slut punkt för autentisering")

    a. Skriv en rubrik i text rutan **rubrik** .

    b. Välj **SAML**i listan **läge** .

    c. Klicka på **Skapa**.

1. **Redigera** den nya slut punkten.

    ![Redigera slut punkt](./media/screensteps-tutorial/ic778528.png "Redigera slut punkt")

1. I avsnittet **Redigera slut punkt för enkel inloggning** utför du följande steg:

    ![Fjärrautentiseringens slut punkt](./media/screensteps-tutorial/ic778527.png "Fjärrautentiseringens slut punkt")

    a. Klicka på **överför ny SAML-certifikatfil**och ladda sedan upp certifikatet, som du har laddat ned från Azure Portal.

    b. Klistra in **inloggnings-URL** -värde, som du har kopierat från Azure Portal till text rutan för **fjärrinloggnings-URL** .

    c. Klistra in URL-värde för **utloggning** , som du har kopierat från Azure Portal till text rutan för **utloggnings-URL** .

    d. Välj en **grupp** för att tilldela användare när de är etablerade.

    e. Klicka på **Uppdatera**.

    f. Kopiera **URL: en för SAML-konsumenten** till Urklipp och klistra in i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ex. Gå tillbaka till **slut punkten redigera enkel inloggning**.

    h. Klicka på knappen **Använd som standard för konto** om du vill använda den här slut punkten för alla användare som loggar in på ScreenSteps. Alternativt kan du klicka på knappen **Lägg till i webbplats** om du vill använda den här slut punkten för vissa platser i **ScreenSteps**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ScreenSteps.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **ScreenSteps**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ScreenSteps**.

    ![ScreenSteps-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-screensteps-test-user"></a>Skapa ScreenSteps test användare

I det här avsnittet skapar du en användare som heter Britta Simon i ScreenSteps. Arbeta med [ScreenSteps-klientens support team](https://www.screensteps.com/contact)   för att lägga till användare i ScreenSteps-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ScreenSteps på åtkomst panelen, bör du loggas in automatiskt på den ScreenSteps som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)