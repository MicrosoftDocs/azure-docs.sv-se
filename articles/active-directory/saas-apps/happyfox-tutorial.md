---
title: 'Självstudie: Azure Active Directory integrering med HappyFox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HappyFox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 1c359f6eb61124f7b1c3d2b38c25fd50041c5710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92446127"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Självstudie: Azure Active Directory integrering med HappyFox

I den här självstudien lär du dig att integrera HappyFox med Azure Active Directory (AD Azure).
Integreringen av HappyFox med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till HappyFox.
* Du kan göra så att dina användare loggas in automatiskt på HappyFox (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med HappyFox behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* HappyFox-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* HappyFox har stöd för **SP**-initierad enkel inloggning


* HappyFox har stöd för **just-in-time**-användaretablering


## <a name="adding-happyfox-from-the-gallery"></a>Lägga till HappyFox från galleriet

För att konfigurera integreringen av HappyFox i Azure AD måste du lägga till HappyFox från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HappyFox från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **HappyFox**, väljer **HappyFox** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![HappyFox i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med HappyFox baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i HappyFox upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med HappyFox behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för HappyFox](#configure-happyfox-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HappyFox-testanvändare](#create-happyfox-test-user)** – för att ha en motsvarighet för Britta Simon i HappyFox som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för HappyFox:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **HappyFox** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![HappyFox-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.happyfox.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för HappyFox](https://support.happyfox.com/home) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera HappyFox** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-happyfox-single-sign-on"></a>Konfigurera enkel inloggning för HappyFox

1. Logga in på din HappyFox-klientorganisation som administratör i ett annat webbläsarfönster.

2. Gå till **Hantera** och klicka fliken **Integreringar**.

    ![Skärm bild som visar sidan "hantera" med fliken "integrationer" vald.](./media/happyfox-tutorial/header.png) 

3. På fliken Integreringar klickar du på **Konfigurera** under **SAML-integrering** för att öppna inställningarna för enkel inloggning.

    ![Skärm bild som visar inställningen "S A M L-integration" med åtgärden "Konfigurera" vald.](./media/happyfox-tutorial/configure.png)

4. I avsnittet för SAML-konfiguration klistrar du in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **SSO Target URL** (Mål-URL för enkel inloggning).

    ![Skärm bild som visar avsnittet "S A M L-konfiguration" med text rutan "S S O Target U R L" markerad.](./media/happyfox-tutorial/targeturl.png)

5. Öppna det certifikat som laddats ned från Azure-portalen i Anteckningar och klistra in dess innehåll i avsnittet **IdP Signature** (IdP-signatur).

    ![Skärm bild som visar avsnittet "I d P-signaturen" markerat.](./media/happyfox-tutorial/cert.png)

6. Klicka på knappen **Spara inställningar**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/savesettings.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till HappyFox.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **HappyFox**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **HappyFox**.

    ![Länken för HappyFox i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-happyfox-test-user"></a>Skapa HappyFox-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i HappyFox. HappyFox har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i HappyFox skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. När du klickar på HappyFox-panelen i Åtkomstpanelen bör du se inloggningssidan för HappyFox-programmet. Du bör se knappen **”SAML”** på inloggningssidan.

    ![Plugin-program](./media/happyfox-tutorial/saml.png)

2. Klicka på knappen **SAML** för att logga in på HappyFox med hjälp av ditt Azure AD-konto.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)