---
title: 'Självstudie: Azure Active Directory integrering med HackerOne | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och HackerOne.
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
ms.openlocfilehash: 530ef271091afc3e646d375ea471d8c48f0f8651
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544204"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Självstudie: Azure Active Directory integrering med HackerOne

I den här självstudien lär du dig att integrera HackerOne med Azure Active Directory (Azure AD).
När du integrerar HackerOne med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till HackerOne i Azure AD.
* Du kan låta dina användare loggas in automatiskt på HackerOne (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med HackerOne behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad HackerOne-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* HackerOne stöder **SP**-initierad enkel inloggning
* HackerOne stöder **Just-in-time**-användaretablering

## <a name="adding-hackerone-from-the-gallery"></a>Lägga till HackerOne från galleriet

När du konfigurerar integreringen av HackerOne i Azure AD, måste du lägga till HackerOne från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till HackerOne från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **HackerOne**, väljer **HackerOne** i resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![HackerOne i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med HackerOne baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i HackerOne upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med HackerOne, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för HackerOne](#configure-hackerone-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HackerOne-testanvändare](#create-hackerone-test-user)** – för att ha en motsvarighet till Britta Simon i HackerOne som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med HackerOne:

1. Välj **Enkel inloggning** på sidan för programintegrering av **HackerOne** i [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om HackerOne-domän och URL:er med enkel inloggning](common/sp-identifier.png)

    a. I text rutan **inloggnings-URL** anger du följande: `https://hackerone.com/users/saml/sign_in?email=<configured domain>`

    b. I text rutan **identifierare (enhets-ID)** anger du följande: `hackerone.com`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera HackerOne** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-hackerone-single-sign-on"></a>Konfigurera enkel inloggning i HackerOne

1. Inloggning till HackerOne-klientorganisationen som administratör.

2. Klicka på menyn längst upp i **Inställningar**.

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_001.png)

3. Gå till **Autentisering** och klicka på **Lägg till SAML-inställningar**.

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_003.png)

4. Gör följande i dialogrutan **SAML-inställningar**:

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_004.png)

    a. I textrutan **E-postdomän** skriver du en registrerad domän.

    b. I text rutor i  **enkel inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Öppna den hämtade **certifikat filen** från Azure Portal i anteckningar, kopiera innehållet i den till Urklipp och klistra sedan in den i text rutan **X509-certifikat**  .

    d. Klicka på **Spara**.

5. Gör följande i dialogrutan Inställningar för autentisering:

    ![Konfigurera enkel inloggning](./media/hackerone-tutorial/tutorial_hackerone_005.png)

    a. Klicka på **Kör test**.

6. När testet har slutförts och fältet **status** visar **senaste test status: slutfört**, väljer du **verifierings** knappen för begäran för att skicka till HackerOne för godkännande.

    ![Skicka till HackerOne för godkännande](./media/hackerone-tutorial/tutorial-hackerone-006.png)

7. När HackerOne godkänner inställningarna kan du välja knappen **migrera användare** för att kräva SSO-autentisering för alla användare.

    ![Aktivera SAML](./media/hackerone-tutorial/tutorial-hackerone-007.png)

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

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till HackerOne.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **HackerOne**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **HackerOne**.

    ![HackerOne-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hackerone-test-user"></a>Skapa HackerOne-testanvändare

I det här avsnittet skapas användaren Britta Simon i HackerOne. HackerOne har stöd för just-in-time-användaretablering och det är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte finns någon användare i HackerOne skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på HackerOne-ikonen i åtkomstpanelen bör du automatiskt loggas in på den HackerOne som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
