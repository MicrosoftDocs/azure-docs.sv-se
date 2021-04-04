---
title: 'Självstudie: Azure Active Directory integrering med SpringCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SpringCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: jeedes
ms.openlocfilehash: 9cfc48e3fdb96ba5b63b28288a801095f7b36f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589953"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Självstudie: Azure Active Directory integrering med SpringCM

I den här självstudien får du lära dig hur du integrerar SpringCM med Azure Active Directory (Azure AD).
Genom att integrera SpringCM med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SpringCM.
* Du kan göra det möjligt för användarna att logga in automatiskt till SpringCM (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SpringCM behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SpringCM-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SpringCM stöder **SP** -INITIERAd SSO

## <a name="adding-springcm-from-the-gallery"></a>Lägga till SpringCM från galleriet

Om du vill konfigurera integreringen av SpringCM i Azure AD måste du lägga till SpringCM från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SpringCM från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SpringCM**, väljer **SpringCM** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SpringCM i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SpringCM baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SpringCM upprättas.

Om du vill konfigurera och testa enkel inloggning med SpringCM i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SpringCM enkel inloggning](#configure-springcm-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SpringCM test User](#create-springcm-test-user)** – om du vill ha en motsvarighet till Britta Simon i SpringCM som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SpringCM i Azure AD:

1. Välj **enkel inloggning** på sidan **SpringCM** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för SpringCM-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SpringCM client support team](https://knowledge.springcm.com/support) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera SpringCM** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-springcm-single-sign-on"></a>Konfigurera SpringCM Single Sign-On

1. Logga in på din **SpringCM** -företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **gå till** på menyn längst upp, klicka på **Inställningar** och klicka sedan på **SAML SSO** i avsnittet **konto inställningar** .

    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. I avsnittet konfiguration av identitets leverantör utför du följande steg:

    ![Konfiguration av identitetsprovider](./media/spring-cm-tutorial/ic797052.png "Konfiguration av identitetsprovider")

    a. Klicka på **Välj utfärdarcertifikat** eller **ändra utfärdarcertifikat** för att ladda upp den hämtade Azure Active Directory certifikatet.

    b. I text rutan **utgivare** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **Service Provider (SP) initierad slut punkt** , klistra in URL-värde för **inloggning** , som du har kopierat från Azure Portal.

    d. Välj **SAML Enabled** som **Enable**.

    e. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SpringCM.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **SpringCM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SpringCM**.

    ![SpringCM-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-springcm-test-user"></a>Skapa SpringCM test användare

Om du vill att Azure Active Directory användare ska kunna logga in på SpringCM måste de tillhandahållas i SpringCM. När det gäller SpringCM är etableringen en manuell uppgift.

> [!NOTE]
> Mer information finns i [skapa och redigera en SpringCM-användare](http://community.springcm.com/s/article/Create-and-Edit-a-SpringCM-User-1619481053). 

**Utför följande steg för att etablera ett användar konto för SpringCM:**

1. Logga in på din **SpringCM** företags webbplats som administratör.

1. Klicka på **gå** till och sedan på **Adress bok**.

    ![Skapa användare](./media/spring-cm-tutorial/ic797054.png "Skapa användare")

1. Klicka på **Skapa användare**.

1. Välj en **användar roll**.

1. Välj **Skicka aktiverings-e-post**.

1. Ange förnamn, efter namn och e-postadress för ett giltigt Azure Active Directory användar konto som du vill etablera i de relaterade text rutorna.

1. Lägg till användaren i en **säkerhets grupp**.

1. Klicka på **Spara**.

   > [!NOTE]
   > Du kan använda andra verktyg för SpringCM av användar konton eller API: er som tillhandahålls av SpringCM för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SpringCM på åtkomst panelen, bör du loggas in automatiskt på den SpringCM som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)