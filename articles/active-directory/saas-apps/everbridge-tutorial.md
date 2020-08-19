---
title: 'Självstudie: Azure Active Directory integrering med EverBridge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EverBridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 1da2fd879dbeac1836469d46567566769f6163a2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555407"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudie: Azure Active Directory integrering med EverBridge

I den här självstudien får du lära dig hur du integrerar EverBridge med Azure Active Directory (Azure AD).
När du integrerar EverBridge med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EverBridge.
* Tillåt att användarna loggas in automatiskt på EverBridge med sina Azure AD-konton. Den här åtkomst kontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure Portal.
Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EverBridge behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En EverBridge-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EverBridge stöder IDP-initierad SSO.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Lägg till EverBridge från Azure Marketplace

Om du vill konfigurera integreringen av EverBridge i Azure AD lägger du till EverBridge från Azure Marketplace till din lista över hanterade SaaS-appar.

Följ dessa steg om du vill lägga till EverBridge från Azure Marketplace.

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret väljer du **Azure Active Directory**.

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **EverBridge**i rutan Sök. Välj **EverBridge** i resultat panelen och välj **Lägg till**.

     ![EverBridge i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med EverBridge baserat på test User Britta Simon.
För att enkel inloggning ska fungera upprättar du en länk relation mellan en Azure AD-användare och den relaterade användaren i EverBridge.

Om du vill konfigurera och testa enkel inloggning med EverBridge i Azure AD, slutför du följande Bygg stenar:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- Konfigurera EverBridge för enkel inloggning med [EverBridge Manager-portalen](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) för att konfigurera inställningar för enkel inloggning på program sidan.
- [Konfigurera EverBridge som EverBridge-medlems Portal med enkel inloggning](#configure-everbridge-as-everbridge-member-portal-single-sign-on) för att konfigurera inställningar för enkel inloggning på program sidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en EverBridge-testanvändare](#create-an-everbridge-test-user) för att få en motsvarighet till Britta Simon i EverBridge som är länkad till Azure AD-representation av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg om du vill konfigurera enkel inloggning med EverBridge i Azure AD.

1. Välj **enkel inloggning**på sidan **EverBridge** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurera programmet antingen som chefs Portal *eller* som medlems Portal på både Azure Portal-och EverBridge-portalen.

4. Följ dessa steg för att konfigurera **EverBridge** -programmet som **EverBridge Manager-portalen**i avsnittet **grundläggande SAML-konfiguration** :

    ![Information om enkel inloggning för EverBridge-domän och URL: er](common/idp-intiated.png)

    a. I rutan **identifierare** anger du en URL som följer mönstret `https://sso.everbridge.net/<API_Name>`

    b. I rutan **svars-URL** anger du en URL som följer mönstret `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska ID-och svars-URL-värden. Kontakta [EverBridge support-teamet](mailto:support@everbridge.com)om du vill hämta dessa värden. Du kan också referera till mönstren som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

5. Följ dessa steg för att konfigurera **EverBridge** -programmet som **EverBridge-medlems Portal**i avsnittet **grundläggande SAML-konfiguration** :

  * Följ dessa steg om du vill konfigurera programmet i IDP läge:

     ![Information om enkel inloggning för EverBridge-domän och URL: er för IDP-initierat läge](common/idp-intiated.png)

    a. I rutan **identifierare** anger du en URL som följer mönstret `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. I rutan **svars-URL** anger du en URL som följer mönstret `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och följer det här steget:

     ![Information om enkel inloggning för EverBridge-domän och URL: er för SP-initierat läge](common/both-signonurl.png)

     a. I rutan **inloggnings-URL** anger du en URL som följer mönstret `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL-värden. Kontakta [EverBridge support-teamet](mailto:support@everbridge.com)om du vill hämta dessa värden. Du kan också referera till mönstren som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

6. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata**. Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera EverBridge** kopierar du de webb adresser som du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggnings-URL
    - Azure AD-identifierare
    - Utloggnings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurera EverBridge för enkel inloggning med EverBridge Manager-portalen

Följ dessa steg om du vill konfigurera SSO på **EverBridge** som ett **EverBridge Manager-portalprogram** .
 
1. Logga in på EverBridge som administratör i ett annat webbläsarfönster.

1. I menyn högst upp väljer du fliken **Inställningar** . Under **säkerhet**väljer du **enkel inloggning**.
   
     ![Konfigurera enkel inloggning](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. I rutan **namn** anger du namnet på ID-providern. Ett exempel är företagets namn.
   
     b. I rutan **API-namn** anger du namnet på API: et.
   
     c. Välj **Välj fil** för att ladda upp metadatafilen som du laddade ned från Azure Portal.
   
     d. För **SAML-identitets platsen**väljer **du identitet i NameIdentifier-elementet för ämnes instruktionen**.
   
     e. I rutan **inloggnings-URL för identitetsprovider** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.
   
     f. Välj **http-omdirigering**för **tjänste leverantörens initierade begär ande bindning**.

     ex. Välj **Spara**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurera EverBridge som EverBridge-medlems Portal med enkel inloggning

Om du vill konfigurera enkel inloggning på **EverBridge** som en **EverBridge-medlems Portal**skickar du den hämtade **XML-koden för federationsmetadata** till [EverBridge support-teamet](mailto:support@everbridge.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Följ dessa steg om du vill skapa test User Britta Simon i Azure Portal.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Användare och alla användare länkar](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialogrutan **Användare** följer du dessa steg.

    ![Dialog rutan användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Markera kryss rutan **Visa lösen ord** . Skriv ned värdet som visas i rutan **lösen ord** .

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EverBridge.

1. I Azure Portal väljer du **företags program**  >  **alla program**  > **EverBridge**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **EverBridge**.

    ![EverBridge-länk i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länk till användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Dialog rutan Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare. Välj **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-everbridge-test-user"></a>Skapa en EverBridge-test användare

I det här avsnittet skapar du test User Britta Simon i EverBridge. Om du vill lägga till användare i EverBridge-plattformen arbetar du med [EverBridge support-teamet](mailto:support@everbridge.com). Användare måste skapas och aktive ras i EverBridge innan du använder enkel inloggning. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen EverBridge på åtkomst panelen, bör du loggas in automatiskt på det EverBridge-konto som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

