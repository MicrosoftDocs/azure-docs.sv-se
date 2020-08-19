---
title: 'Självstudie: Azure Active Directory integrering med Zscaler beta | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546085"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Självstudie: Azure Active Directory integrering med Zscaler beta

I den här självstudien lär du dig att integrera Zscaler Beta med Azure Active Directory (AD Azure).
När du integrerar Zscaler beta med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler-beta.
* Tillåt att användarna loggas in automatiskt till Zscaler beta med sina Azure AD-konton. Den här åtkomst kontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure Portal.

Mer information om program vara som en tjänst (SaaS) för program integrering med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Zscaler Beta behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Zscaler beta-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler beta stöder SP-initierad SSO.
* Zscaler beta stöder just-in-Time-etablering av användare.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Lägg till Zscaler beta från Azure Marketplace

Om du vill konfigurera integrationen av Zscaler beta i Azure AD lägger du till Zscaler beta från Azure Marketplace till din lista över hanterade SaaS-appar.

Följ dessa steg om du vill lägga till Zscaler beta från Azure Marketplace.

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret väljer du **Azure Active Directory**.

    ![Knappen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Zscaler beta**i rutan Sök. Välj **Zscaler beta** i resultat panelen och välj sedan **Lägg till**.

     ![Zscaler Beta i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Zscaler-beta baserat på test User Britta Simon.
För att enkel inloggning ska fungera upprättar du en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler beta.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Zscaler beta, fyller du i följande Bygg stenar:

- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera Zscaler beta-enkel inloggning](#configure-zscaler-beta-single-sign-on) för att konfigurera inställningarna för enkel inloggning på program sidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en Zscaler beta test-användare](#create-a-zscaler-beta-test-user) för att få en motsvarighet till Britta Simon i Zscaler beta som är länkad till Azure AD-representationen av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Följ dessa steg om du vill konfigurera enkel inloggning med Azure AD med Zscaler beta.

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Zscaler Beta**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggnings länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du **Redigera** för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** följer du det här steget:

    ![Information om enkel inloggning för Zscaler beta-domän och URL: er](common/sp-intiated.png)

    - I rutan **inloggnings-URL** anger du den URL som används av användarna för att logga in på ditt Zscaler beta-program.

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med värdet faktiskt inloggnings-URL. Kontakta [support teamet för Zscaler beta client](https://www.zscaler.com/company/contact)för att få värdet.

5. Zscaler beta-programmet förväntar sig SAML-intyg i ett särskilt format. Du måste lägga till anpassade mappningar för attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Välj **Redigera** för att öppna dialog rutan **användarattribut** .

    ![Dialogrutan Användarattribut](common/edit-attribute.png)

6. Zscaler beta-programmet förväntar sig att fler attribut ska skickas tillbaka i SAML-svar. I avsnittet **användar anspråk** **i dialog rutan användarattribut,** följer du dessa steg för att lägga till attributet SAML-token, som visas i följande tabell.
    
    | Name | Källattribut | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Dialog rutan användar anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    b. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    c. Lämna rutan **namn område** tom.

    d. För **källa**väljer du **attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    ex. Välj **Spara**.

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns i [Konfigurera roll anspråk](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **certifikatet (base64)**. Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler beta** kopierar du de webb adresser som du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    - Inloggnings-URL
    - Azure AD-identifierare
    - Utloggnings-URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Konfigurera Zscaler beta-enkel inloggning

1. Om du vill automatisera konfigurationen i Zscaler beta installerar du **tillägget Mina appar säker inloggnings webbläsare** genom att välja **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren kan du välja **Konfigurera Zscaler beta** leder dig till Zscaler beta-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler beta. Webb läsar tillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3 till 6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Öppna ett nytt webbläsarfönster om du vill konfigurera Zscaler-beta manuellt. Logga in på din Zscaler beta-företags webbplats som administratör och följ de här stegen.

4. Gå till autentiseringsinställningarna för **administrations**  >  **autentisering**  >  **Authentication Settings**och följ de här stegen.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. Under **Autentiseringstyp**väljer du **SAML**.

    b. Välj **Konfigurera SAML**.

5. I fönstret **Redigera SAML** följer du dessa steg: 
            
    ![Hantera användare & autentisering](./media/zscaler-beta-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. I rutan **URL för SAML-Portal** klistrar du in den **inloggnings-URL** som du kopierade från Azure Portal.

    b. I rutan **inloggnings namn** anger du **NameID**.

    c. I rutan **offentligt SSL-certifikat** väljer du **Ladda upp** för att ladda upp det Azure SAML-signeringscertifikat som du laddade ned från Azure Portal.

    d. Växla **Aktivera SAML Auto-Provisioning**.

    e. I rutan **användarens visnings namn** anger du **DisplayName** om du vill aktivera autoetablering för SAML för DisplayName-attribut.

    f. I rutan **gruppnamn-attribut** anger du **memberOf** om du vill aktivera SAML autoetablering för memberOf-attribut.

    ex. I rutan **avdelnings namn** anger du **avdelning** om du vill aktivera SAML autoetablering för avdelnings-attribut.

    h. Välj **Spara**.

6. Följ dessa steg på dialog sidan **Konfigurera användarautentisering** :

    ![Aktiverings menyn och knappen Aktivera](./media/zscaler-beta-tutorial/ic800207.png)

    a. Hovra över **aktiverings** menyn längst ned till vänster.

    b. Välj **Aktivera**.

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar
Följ dessa steg om du vill konfigurera proxyinställningarna i Internet Explorer.

1. Starta **Internet Explorer**.

2. Öppna dialog rutan **Internet alternativ** genom att välja **Internet alternativ** på **verktyg** -menyn. 
    
     ![Dialog rutan Internet alternativ](./media/zscaler-beta-tutorial/ic769492.png "Internet alternativ")

3. Välj fliken **anslutningar** . 
  
     ![Fliken Anslutningar](./media/zscaler-beta-tutorial/ic769493.png "Anslutningar")

4. Välj **LAN-inställningar** för att öppna dialog rutan **Inställningar för lokalt nätverk (LAN)** .

5. I avsnittet **proxyserver** följer du dessa steg: 
   
    ![Avsnittet proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Markera kryss rutan **Använd en proxyserver för ditt lokala nätverk** .

    b. I rutan **adress** anger du **Gateway. Zscaler-Beta.net**.

    c. I rutan **port** anger du **80**.

    d. Markera kryss rutan **Använd ingen proxyserver för lokala adresser** .

    e. Välj **OK** för att stänga dialog rutan **Inställningar för lokalt nätverk (LAN)** .

6. Välj **OK** för att stänga dialog rutan **Internet alternativ** .

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Skapa en test användare i Azure Portal som kallas Britta Simon.

1. I Azure Portal i det vänstra fönstret väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Användare och alla användare länkar](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan **användare** följer du de här stegen:

    ![Dialog rutan användare](common/user-properties.png)

    a. I rutan **Namn** anger du **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`. Ett exempel är BrittaSimon@contoso.com.

    c. Markera kryss rutan **Visa lösen ord** . Skriv ned värdet som visas i rutan **lösen ord** .

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler beta.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **Zscaler beta**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program anger du och väljer **Zscaler beta**.

    ![Zscaler beta-länk i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länk till användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Knappen Lägg till användare](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du användaren som **Britta Simon** i listan. Välj sedan **Välj** längst ned på skärmen.

    ![Dialog rutan användare och grupper](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. I dialog rutan **Välj roll** väljer du lämplig användar roll i listan. Välj sedan **Välj** längst ned på skärmen.

    ![Dialog rutan Välj roll](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

    ![Dialog rutan Lägg till tilldelning](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Skapa en Zscaler beta test användare

I det här avsnittet skapas User Britta Simon i Zscaler beta. Zscaler Beta stöder **just-in-time-användaretablering**, vilket är aktiverat som standard. Det finns inget som du kan göra i det här avsnittet. Om det inte redan finns någon användare i Zscaler Beta skapas en ny efter autentisering.

>[!Note]
>Kontakta [support teamet för Zscaler beta](https://www.zscaler.com/company/contact)för att skapa en användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Testa konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer Zscaler beta-panelen på åtkomst panelen, bör du loggas in automatiskt på den Zscaler-beta version som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

