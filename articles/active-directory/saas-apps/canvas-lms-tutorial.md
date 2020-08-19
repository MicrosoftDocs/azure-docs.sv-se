---
title: 'Självstudie: Azure Active Directory integrering med arbets ytan | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsytor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: jeedes
ms.openlocfilehash: 24350c63df04e55107ed32c2d61d1026aa27bdb9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547638"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Självstudie: Azure Active Directory integrering med arbets yta

Lär dig hur du integrerar Canvas med Azure Active Directory (AD Azure) i den här självstudien.
Integreringen av Canvas med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Canvas från Azure AD.
* Du kan konfigurera inställningar så att dina användare loggas in automatiskt i Canvas (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Canvas behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Canvas-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Canvas stöder **SP**-initierad enkel inloggning

## <a name="adding-canvas-from-the-gallery"></a>Lägga till Canvas från galleriet

För att konfigurera integreringen av Canvas i Azure AD måste du lägga till Canvas från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Canvas från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Canvas**, väljer **Canvas** från resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Canvas i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Canvas baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Canvas upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Canvas måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Canvas](#configure-canvas-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Canvas-testanvändare](#create-canvas-test-user)** – för att skapa en motsvarighet till Britta Simon i Canvas som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Canvas:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Canvas** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Canvas-domäner och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.instructure.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Canvas kundsupport](https://community.canvaslms.com/community/help) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera Canvas** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-canvas-single-sign-on"></a>Konfigurera enkel inloggning för Canvas

1. Öppna ett nytt webbläsarfönster och logga in på din Canvas-företagswebbplats som administratör.

2. Gå till **Kurser \> Hanterade konton \> Microsoft**.

    ![Arbetsyta](./media/canvas-lms-tutorial/ic775990.png "Arbetsyta")

3. Välj **Autentisering** i navigeringsfönstret till vänster och klicka sedan på **Lägg till ny SAML-konfiguration**.

    ![Autentisering](./media/canvas-lms-tutorial/ic775991.png "Autentisering")

4. Utför följande steg på sidan Aktuell integration:

    ![Aktuell integrering](./media/canvas-lms-tutorial/ic775992.png "Aktuell integrering")

    a. I textrutan **IdP Entity ID** (Entitets-ID för IdP) klistrar du in värdet för **Azure Ad-identifieraren**, som du har kopierat från Azure-portalen.

    b. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. I textrutan för **utloggnings-URL** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. I textrutan för **länken för lösenordsändring** klistrar du in värdet för **URL:en för lösenordsändring** som du har kopierat från Azure-portalen.

    e. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in **tumavtrycksvärdet** för certifikatet som du har kopierat från Azure-portalen.

    f. I listan med **attribut för inloggning** väljer du **NameID**.

    ex. Välj **emailAddress** i listan med **format för identifierare**.

    h. Klicka på **Spara autentiseringsinställningar**.

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

I det här avsnittet ska du göra så att Britta Simon kan använda enkel inloggning med Azure genom att ge åtkomst till Canvas.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Canvas**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Canvas** i listan med program.

    ![Canvas-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-canvas-test-user"></a>Skapa Canvas-testanvändare

För att Azure AD-användare ska kunna logga in i Canvas måste de vara etablerade i Canvas. När det gäller Canvas är etableringen av användare en manuell åtgärd.

**Gör följande för att etablera ett användarkonto:**

1. Logga in i din **Canvas**-klientorganisation.

2. Gå till **Kurser \> Hanterade konton \> Microsoft**.

   ![Arbetsyta](./media/canvas-lms-tutorial/ic775990.png "Arbetsyta")

3. Klicka på **Användare**.

   ![Användare](./media/canvas-lms-tutorial/ic775995.png "Användare")

4. Klicka på **Lägg till ny användare**.

   ![Användare](./media/canvas-lms-tutorial/ic775996.png "Användare")

5. Utför följande steg i dialogrutan Lägg till en ny användare:

   ![Lägg till användare](./media/canvas-lms-tutorial/ic775997.png "Lägg till användare")

   a. I textrutan **Fullständigt namn** anger du namnet på användaren, t.ex. **BrittaSimon**.

   b. I text rutan **e-post** anger du e-postadressen till användaren som **brittasimon \@ contoso.com**.

   c. I text rutan **inloggning** anger du användarens Azure AD-e-postadress som **brittasimon \@ contoso.com**.

   d. Välj alternativet för att **meddela användaren via e-post när kontot skapats**.

   e. Klicka på **Lägg till användare**.

> [!NOTE]
> Du kan använda andra verktyg för skapande av användar konton eller API: er från arbets ytan för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Canvas-panelen på åtkomstpanelen så bör du loggas in automatiskt i Canvas-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

