---
title: 'Självstudie: Azure Active Directory integration med IMPAC risk hanteraren | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IMPAC risk hanteraren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 28b754a90b20a26d55b3c905f7cddafee4f0aa3d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545219"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Självstudie: Azure Active Directory integration med IMPAC risk hanteraren

I den här självstudien får du lära dig att integrera IMPAC riskhantering med Azure Active Directory (Azure AD).
Genom att integrera IMPAC risk Manager med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till IMPAC risk Manager.
* Du kan göra det möjligt för användarna att logga in automatiskt till IMPAC risk hanteraren (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IMPAC-riskhantering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IMPAC-prenumeration med enkel inloggning aktive rad för riskhantering

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* IMPAC-riskhantering stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Lägga till IMPAC risk hanteraren från galleriet

Om du vill konfigurera integreringen av IMPAC-riskhantering i Azure AD måste du lägga till IMPAC riskhantering från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till IMPAC-riskhantering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **IMPAC riskhantering**i rutan Sök och välj **IMPAC riskhantering** från resultat panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![IMPAC risk hanteraren i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med IMPAC riskhantering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i IMPAC risk Manager.

Om du vill konfigurera och testa enkel inloggning med IMPAC-riskhantering i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IMPAC riskhantering-hanteraren för enkel inloggning](#configure-impac-risk-manager-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa IMPAC riskhantering-test](#create-impac-risk-manager-test-user)** för att få en motsvarighet till Britta Simon i IMPAC riskhanterings hanterare som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med IMPAC riskhantering i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan för program integrering i **IMPAC riskhantering** väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för IMPAC risk Manager-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du ett värde som tillhandahålls av IMPAC

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster |
    | ---------------|--------------- |
    | För produktion |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För frågor och svar |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | För test |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | | |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för IMPAC risk Manager-domän och URL: er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    | Miljö | URL-mönster |
    | ---------------|--------------- |
    | För produktion |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För mellanlagring och utbildning  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För utveckling  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För frågor och svar |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | För test |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [IMPAC riskfylld Manager client support team](mailto:rmsupport@Impac.co.nz) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera IMPAC-riskhantering** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-impac-risk-manager-single-sign-on"></a>Konfigurera IMPAC risk hanteraren enkel inloggning

Om du vill konfigurera enkel inloggning på **IMPAC riskhanterings hanterare** måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för IMPAC riskhantering Manager](mailto:rmsupport@Impac.co.nz). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till IMPAC risk Manager.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **IMPAC risk Manager**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **IMPAC riskhantering**.

    ![Länken IMPAC riskhantering i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-impac-risk-manager-test-user"></a>Skapa test användare av IMPAC-riskhantering

I det här avsnittet skapar du en användare som heter Britta Simon i IMPAC risk Manager. Arbeta med [support teamet för IMPAC riskhantering](mailto:rmsupport@Impac.co.nz) för att lägga till användare i IMPAC riskhanterings plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen IMPAC riskhantering på åtkomst panelen, bör du loggas in automatiskt till den IMPAC-riskhantering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
