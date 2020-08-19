---
title: 'Självstudie: Azure Active Directory integrering med StatusPage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ee2eb7586489527272d92dd1eaef6976bc41f94
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524858"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Självstudie: Azure Active Directory integrering med StatusPage

I den här självstudien får du lära dig hur du integrerar StatusPage med Azure Active Directory (Azure AD).
Genom att integrera StatusPage med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till StatusPage.
* Du kan göra det möjligt för användarna att logga in automatiskt till StatusPage (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med StatusPage behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* StatusPage-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* StatusPage stöder **IDP** INITIERAd SSO

## <a name="adding-statuspage-from-the-gallery"></a>Lägga till StatusPage från galleriet

Om du vill konfigurera integreringen av StatusPage i Azure AD måste du lägga till StatusPage från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till StatusPage från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **StatusPage**, väljer **StatusPage** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![StatusPage i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med StatusPage baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i StatusPage upprättas.

Om du vill konfigurera och testa enkel inloggning med StatusPage i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera StatusPage-enkel inloggning](#configure-statuspage-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa StatusPage test User](#create-statuspage-test-user)** – om du vill ha en motsvarighet till Britta Simon i StatusPage som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med StatusPage i Azure AD:

1. Välj **enkel inloggning**på sidan **StatusPage** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning för StatusPage-domän och URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Kontakta StatusPage support-teamet på [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) för att begära metadata som krävs för att konfigurera enkel inloggning. 
    >
    > a. Kopiera utfärdarens värde från metadata och klistra in det i text rutan **identifierare** .
    >
    > b. Kopiera svars-URL: en från metadata och klistra in den i text rutan **svars-URL** .

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera StatusPage** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-statuspage-single-sign-on"></a>Konfigurera StatusPage enkel inloggning

1. Logga in på din StatusPage företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Hantera konto**i huvud verktygsfältet.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **enkel inloggning** .

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Utför följande steg på sidan SSO-konfiguration:

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. I text rutan för **SSO-mål-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. Öppna det hämtade certifikatet i anteckningar, kopiera innehållet och klistra in det i text rutan **certifikat** .

    c. Klicka på **Spara konfiguration**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till StatusPage.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **StatusPage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **StatusPage**.

    ![StatusPage-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-statuspage-test-user"></a>Skapa StatusPage test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i StatusPage.

StatusPage stöder just-in-Time-etablering. Du har redan aktiverat det i [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on).

**Utför följande steg för att skapa en användare med namnet Britta Simon i StatusPage:**

1. Logga in på din StatusPage företags webbplats som administratör.

1. I menyn högst upp klickar du på **Hantera konto**.

    ![Konfigurera enkel inloggning](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **grupp medlemmar** .
  
    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicka på **Lägg till grupp medlem**.
  
    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Ange **e-postadress**, **förnamn**och efter **namn** för en giltig användare som du vill etablera i de relaterade text rutorna. 

    ![Skapa en testanvändare för Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Som **roll**väljer du **klient administratör**.

1. Klicka på **Skapa konto**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen StatusPage på åtkomst panelen, bör du loggas in automatiskt på den StatusPage som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
