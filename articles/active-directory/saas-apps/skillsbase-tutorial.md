---
title: 'Självstudie: Azure Active Directory integrering med kunskaps bas | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och kunskaps bas.
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
ms.openlocfilehash: 1c8135f39517a5983f700c35f08a5c0749113450
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552251"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Självstudie: Azure Active Directory integrering med kunskaps bas

I den här självstudien får du lära dig hur du integrerar kunskaps basen med Azure Active Directory (Azure AD).
Att integrera kunskaps bas med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till kunskaps bas.
* Du kan göra det möjligt för användarna att logga in automatiskt till kunskaps bas (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med kunskaps bas behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Kunskap om enkel inloggning med aktiverade prenumerationer

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kunskaps bas stöder **SP** -INITIERAd SSO
* Kunskaps basen stöder **just-in-Time** User-etablering

## <a name="adding-skills-base-from-the-gallery"></a>Lägga till kunskaps bas från galleriet

Om du vill konfigurera en integrering av kunskaps basen i Azure AD måste du lägga till kunskaps bas från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till kunskaps bas från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **kunskaps bas**, väljer **kunskaps bas** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Kunskaps bas i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med kunskaps bas baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i kunskaps bas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med kunskaps bas måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera kunskaper grundläggande enkel inloggning](#configure-skills-base-single-sign-on)** -konfigurera för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa kunskaps bas test användare](#create-skills-base-test-user)** – för att få en motsvarighet till Britta Simon i kunskaps basen som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med kunskaps bas:

1. På sidan **kunskaps bas** program integration i [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för kunskaps bas domäner och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE]
    > Du kan hämta inloggnings-URL: en från kunskaps bas programmet. Logga in som administratör och gå till admin-> inställningar-> instans information – > gen vägs länk. Kopiera inloggnings-URL och klistra in den i ovanstående text ruta.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera kunskaps bas** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-skills-base-single-sign-on"></a>Konfigurera enkel inloggning för kunskaps bas

1. Logga in på kunskaps basen som säkerhets administratör i ett annat webbläsarfönster.

2. Klicka på **autentisering**under **admin** på vänster sida av menyn.

    ![Administratören](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

3. På sidan **autentisering** väljer du enkel inloggning som **SAML 2**.

    ![Enskild](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

4. Utför följande steg på sidan **autentisering** :

    ![Enskild](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Klicka på knappen **Uppdatera IDP metadata** bredvid **status** alternativ och klistra in innehållet i metadata-XML som du laddade ned från Azure Portal i den angivna text rutan.

    > [!Note]
    > Du kan också validera IDP metadata via verktyget för **metadata-verifiering** som marker ATS i skärm bilden ovan.

    b. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till kunskaps bas.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **kunskaps bas**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **kunskaps bas**.

    ![Länken kunskaps bas i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-skills-base-test-user"></a>Skapa kunskaps bas test användare

I det här avsnittet skapas en användare som kallas Britta Simon i kunskaps basen. Kunskaps basen stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i kunskaps basen skapas en ny efter autentiseringen.

> [!Note]
> Om du behöver skapa en användare manuellt följer du anvisningarna [här](http://wiki.skills-base.net/index.php?title=Adding_people_and_enabling_them_to_log_in).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen kunskaper i åtkomst panelen bör du loggas in automatiskt på den kunskaps bas som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)