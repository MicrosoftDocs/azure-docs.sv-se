---
title: 'Självstudie: Azure Active Directory integrering med Signagelive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Signagelive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.openlocfilehash: 5a8fe24e5fa1935b2363ed5d2c15c72af8af11ff
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539784"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Självstudie: Azure Active Directory integrering med Signagelive

I den här självstudien lär du dig att integrera Signagelive med Azure Active Directory (AD Azure).
Genom att integrera Signagelive med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Signagelive.
* Du kan göra det möjligt för användarna att logga in automatiskt på Signagelive (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Signagelive behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderings period](https://azure.microsoft.com/pricing/free-trial/)på en månad.
* En Signagelive-aktiverad prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Signagelive stöder SP-initierad SSO.

## <a name="add-signagelive-from-the-gallery"></a>Lägg till Signagelive från galleriet

Om du vill konfigurera integreringen av Signagelive i Azure AD måste du först lägga till Signagelive från galleriet i listan över hanterade SaaS-appar.

Gör så här om du vill lägga till Signagelive från galleriet:

1. I [Azure Portal](https://portal.azure.com)i det vänstra fönstret väljer du ikonen **Azure Active Directory** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj alternativet **alla program** .

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen  **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Signagelive**i rutan Sök. 

     ![Signagelive i resultatlistan](common/search-new-app.png)

5. Välj **Signagelive** i resultat fönstret och välj sedan knappen **Lägg till** för att lägga till programmet.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med Signagelive baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i Signagelive.

Om du vill konfigurera och testa enkel inloggning med Signagelive i Azure AD måste du först slutföra följande Bygg stenar:

1. [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
2. [Konfigurera Signagelive enkel inloggning](#configure-signagelive-single-sign-on) för att konfigurera inställningarna för enkel inloggning på program sidan.
3. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
4. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
5. [Skapa en Signagelive-testanvändare](#create-a-signagelive-test-user) för att få en motsvarighet till Britta Simon i Signagelive som är länkad till Azure AD-representationen av användaren.
6. [Testa enkel inloggning](#test-single-sign-on) för att verifiera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Signagelive i Azure AD:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Signagelive**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , utför följande steg:

    ![Signagelive-domän och information om URL:er för enkel inloggning](common/sp-signonurl.png)

    I rutan **inloggnings-URL** anger du en URL som använder följande mönster:  `https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL: en. Kontakta [Signagelive-klientens support team](mailto:support@signagelive.com) för att få värdet. Du kan också se de mönster som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

5. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **certifikatet (RAW)** från de angivna alternativen enligt ditt krav. Spara den sedan på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera Signagelive** kopierar du de URL: er som du behöver.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-signagelive-single-sign-on"></a>Konfigurera Signagelive enkel inloggning

Om du vill konfigurera enkel inloggning på Signagelive-sidan skickar du det hämtade **certifikatet (RAW)** och kopierade URL: er från Azure Portal till [support teamet för Signagelive](mailto:support@signagelive.com). De säkerställer att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan **användare** utför du följande steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** anger du " brittasimon@yourcompanydomain.extension ". I det här fallet kan du till exempel ange " BrittaSimon@contoso.com ".

    c. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan lösen ord.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Signagelive.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Signagelive**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Signagelive**.

    ![Signagelive-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare** . I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon**i listan **användare** . Klicka sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-intyget väljer du i dialog rutan **Välj roll** lämplig roll för användaren i listan. Klicka sedan på knappen **Välj** längst ned på skärmen.

7. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

### <a name="create-a-signagelive-test-user"></a>Skapa en Signagelive-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Signagelive. Arbeta med [Signagelive support team](mailto:support@signagelive.com) för att lägga till användare i Signagelive-plattformen. Du måste skapa och aktivera användare innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen för Mina appar.

När du väljer panelen **Signagelive** på portalen för Mina appar, bör du loggas in automatiskt. Mer information om portalen för Mina appar finns i [Vad är portalen för Mina appar?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

