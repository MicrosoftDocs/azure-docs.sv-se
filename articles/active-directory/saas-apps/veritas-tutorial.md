---
title: 'Självstudie: Azure Active Directory integrering med Veritas Enterprise Vault. Cloud SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veritas Enterprise Vault. Cloud SSO.
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
ms.openlocfilehash: cdb3f30813d2650737cfa43507cef2b1d456573d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532548"
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Självstudie: Azure Active Directory integrering med Veritas Enterprise Vault. enkel inloggning i molnet

I den här självstudien får du lära dig att integrera Veritas Enterprise-valvet. Cloud SSO med Azure Active Directory (Azure AD).
Integrering av Veritas Enterprise Vault. med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till Veritas Enterprise Vault. i molnet SSO.
* Du kan göra det möjligt för användarna att logga in automatiskt till Veritas Enterprise-valvet. enkel inloggning med Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Veritas Enterprise Vault. Cloud SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Veritas Enterprise-valvet. Cloud SSO-aktiverad prenumeration med enkel inloggning har Aktiver ATS

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Veritas Enterprise-valvet. Cloud SSO stöder **SP** -INITIERAd SSO

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Lägger till Veritas Enterprise Vault. moln-SSO från galleriet

Du måste lägga till Veritas Enterprise Vault för att konfigurera integreringen av Veritas Enterprise Vault. via en enkel inloggning i Azure AD.

**Gör så här för att lägga till Veritas Enterprise Vault. Cloud SSO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Veritas Enterprise Vault. Cloud SSO**, Välj **Veritas Enterprise Vault. Cloud SSO** från resultat panelen och klicka på **Lägg till** för att lägga till programmet.

     ![Veritas Enterprise-valvet. moln-SSO i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Veritas Enterprise Vault. enkel inloggning i molnet som baseras på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera, en länk relation mellan en Azure AD-användare och en relaterad användare i Veritas Enterprise-valvet måste du upprätta en anslutning till molnet.

Så här konfigurerar och testar du enkel inloggning med Veritas Enterprise Vault i Azure AD. i molnet SSO måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Veritas Enterprise-valvet. med enkel inloggning i Cloud SSO](#configure-veritas-enterprise-vaultcloud-sso-single-sign-on)** kan du konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Veritas Enterprise-valvet. Cloud SSO-testanvändare](#create-veritas-enterprise-vaultcloud-sso-test-user)** – om du vill ha en motsvarighet till Britta Simon i Veritas Enterprise Vault. Cloud SSO som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du enkel inloggning i Azure AD med Veritas Enterprise Vault. Cloud SSO, utför följande steg:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Veritas Enterprise-valvet. Cloud SSO** -programintegration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Veritas Enterprise-valv. information om enkel inloggning för Cloud SSO-domäner och URL: er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. I rutan **identifierare** använder du URL: en med hjälp av data centret:

    | Datacenter| URL |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|

    c. I text rutan **svars-URL** använder du URL: en med följande data Center:

    | Datacenter| URL |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta Veritas Enterprise-valvet för att få det här värdet genom att kontakta [support teamet för Cloud SSO.](https://www.veritas.com/support/.html) Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Veritas Enterprise Vault. Cloud SSO** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-veritas-enterprise-vaultcloud-sso-single-sign-on"></a>Konfigurera Veritas Enterprise Vault. enkel inloggning för enkel inloggning i molnet

Om du vill konfigurera enkel inloggning på **Veritas Enterprise-valvet.** på sidan för enkel inloggning i molnet måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Veritas Enterprise Vault. support team för support för Cloud SSO](https://www.veritas.com/support/.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Veritas Enterprise Vault. Cloud SSO.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Veritas Enterprise Vault. Cloud SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Veritas Enterprise Vault. Cloud SSO**.

    ![Veritas Enterprise Vault-länken för enkel inloggning i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-veritas-enterprise-vaultcloud-sso-test-user"></a>Skapa Veritas Enterprise-valvet. Cloud SSO-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Veritas Enterprise Vault. Cloud SSO. Arbeta med [Veritas Enterprise Vault. Cloud SSO support team](https://www.veritas.com/support/.html) för att lägga till användare i Veritas Enterprise-valvet. plattform för moln-SSO. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den Veritas Enterprise-valvet. på panelen för inloggning i molnet i åtkomst panelen, bör du loggas in automatiskt på Veritas Enterprise-valvet. i det här fallet ska du konfigurera SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

