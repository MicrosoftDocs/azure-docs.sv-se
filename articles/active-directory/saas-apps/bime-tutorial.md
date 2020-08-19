---
title: 'Självstudie: Azure Active Directory integrering med Bime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bime.
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
ms.openlocfilehash: 149656bebac7e8fab24dc1e1847a4e56bf5f3888
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547349"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Självstudie: Azure Active Directory integrering med Bime

I den här självstudien lär du dig att integrera Bime med Azure Active Directory (AD Azure).
Integreringen av Bime med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Bime.
* Du kan göra så att dina användare automatiskt loggas in på Bime (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Bime behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Bime-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bime har stöd för **SP**-initierad enkel inloggning

## <a name="adding-bime-from-the-gallery"></a>Lägga till Bime från galleriet

För att konfigurera integrering av Bime i Azure AD behöver du lägga till Bime från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bime från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Bime**, väljer **Bime** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Bime i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD till Bime baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Bime upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Bime behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Bime](#configure-bime-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Bime-testanvändare](#create-bime-test-user)** – för att ha en motsvarighet för Britta Simon i Bime som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Bime:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Bime** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Bime-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.Bimeapp.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera Bime** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bime-single-sign-on"></a>Konfigurera enkel inloggning för Bime

1. I ett annat webbläsarfönster loggar du in på din Bime-företagswebbplats som administratör.

2. I verktygsfältet klickar du på **Admin** och sedan på **Konto**.
  
    ![Administratör](./media/bime-tutorial/ic775558.png "Administratör")

3. På sidan för kontokonfiguration utför du följande steg:
  
    ![Konfigurera enkel inloggning](./media/bime-tutorial/ic775559.png "Konfigurera enkel inloggning")

    a. Välj **Aktivera SAML-autentisering**.

    b. I textrutan för **fjärrinloggnings-URL** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in värdet för det **THUMBPRINT** (tumavtryck) som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Bime.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Bime**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Bime**.

    ![Länken för Bime i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bime-test-user"></a>Skapa Bime-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Bime måste de etableras till Bime. När det gäller Bime är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Bime**-klientorganisation.

2. I verktygsfältet klickar du på **Admin** och sedan på **Användare**.

    ![Administratör](./media/bime-tutorial/ic775561.png "Administratör")

3. I **användarlistan** klickar du på **Lägg till ny användare** (”+”).

    ![Användare](./media/bime-tutorial/ic775562.png "Användare")

4. I dialogrutan **Användarinformation** utför du följande steg:

    ![Användar information](./media/bime-tutorial/ic775563.png "Användar information")

    a. I text rutan för det **första namnet** anger du det första namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. I text rutan **e-post** anger du e-postadressen till användaren som **brittasimon \@ contoso.com**.

    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för Bime av användar konton eller API: er som tillhandahålls av Bime för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Bime-panelen i åtkomstpanelen bör du automatiskt loggas in på Bime som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

