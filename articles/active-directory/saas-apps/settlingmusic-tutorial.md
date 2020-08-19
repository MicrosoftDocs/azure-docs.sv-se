---
title: 'Självstudie: Azure Active Directory integrering med att reglera musik | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och att reglera musik.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: d8c1f8c9d3ecdd7cda33e358c2b0de99123be5ae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539971"
---
# <a name="tutorial-azure-active-directory-integration-with-settling-music"></a>Självstudie: Azure Active Directory integrering med att reglera musik

I den här självstudien får du lära dig hur du integrerar inlöser musik med Azure Active Directory (Azure AD).
Integrering av att lösa in musik med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till att reglera musik.
* Du kan låta dina användare bli inloggade automatiskt för att reglera musik (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med att reglera musik behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Löser den aktiverade prenumerationen för enkel inloggning med musik

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Att lösa musik stöder **SP** -INITIERAd SSO

## <a name="adding-settling-music-from-the-gallery"></a>Lägga till att reglera musik från galleriet

Om du vill konfigurera integrering av att lösa in musik i Azure AD måste du lägga till att reglera musik från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till att reglera musik från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Lös musik**, väljer att **reglera musik** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Lösa musik i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med att reglera musik baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i att lösa musik upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med att lösa musik måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning](#configure-settling-music-single-sign-on)** för att lösa in musik på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ett konto för att lösa in musik test](#create-settling-music-test-user)** för att få en motsvarighet till Britta Simon i att reglera musik som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med att lösa musik:

1. På sidan för att lösa in **musik** program på [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Att lösa information om enkel inloggning för musik domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.rakurakuseisan.jp/<USERACCOUNT>/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [support teamet för musik](https://rakurakuseisan.jp/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera att reglera musik** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-settling-music-single-sign-on"></a>Konfigurera enkel inloggning för att lösa in musik

1. Logga in på att lösa musik som en säkerhets administratör i ett annat webbläsarfönster.

1. Klicka på fliken **hantering** överst på sidan.

    ![Steg 1 för att lösa musik](./media/settlingmusic-tutorial/tutorial_settlingmusic_step1.png)

1. Klicka på fliken  **system inställning** .

    ![Step2 för att lösa musik](./media/settlingmusic-tutorial/tutorial_settlingmusic_step2.png)

1. Växla till fliken **säkerhet** .

    ![Steg 3 för att lösa musik](./media/settlingmusic-tutorial/tutorial_settlingmusic_step3.png)

1. I avsnittet **enkel inloggnings inställning** utför du följande steg:

    ![Step5 för att lösa musik](./media/settlingmusic-tutorial/tutorial_settlingmusic_step4.png)

    a. Klicka **för att aktivera**.

    b. I **inloggnings-URL: en för text rutan ID-provider** klistrar du in värdet för **inloggnings-URL** som du har kopierat från Azure Portal.

    c. I text rutan **ID för utloggnings-URL** klistrar du in värdet för den **utloggnings-URL** som du kopierade från Azure Portal.

    d. Klicka på **Välj fil** för att ladda upp **certifikatet (base64)** som du har laddat ned formuläret Azure Portal.

    e. Klicka på knappen **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till att reglera musik.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan att **betala musik**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **likvidera musik**.

    ![Länken för att lösa musik i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-settling-music-test-user"></a>Skapa användare för att lösa in musik test

I det här avsnittet skapar du en användare som kallas Britta Simon i att reglera musiken. Arbeta med att [reglera support teamet för musik klienten](https://rakurakuseisan.jp/) för att lägga till användarna på den lösa musik plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för att reglera musik på åtkomst panelen, bör du loggas in automatiskt på den avskrifts musik som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)