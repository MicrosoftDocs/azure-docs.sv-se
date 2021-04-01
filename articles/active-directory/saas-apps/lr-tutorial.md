---
title: 'Självstudie: Azure Active Directory integrering med LoginRadius | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606427"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Självstudie: Azure Active Directory integrering med LoginRadius

I den här självstudien får du lära dig hur du integrerar LoginRadius med Azure Active Directory (Azure AD).

Genom att integrera LoginRadius med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till LoginRadius.
* Du kan göra det möjligt för användarna att logga in automatiskt till LoginRadius (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med LoginRadius behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* En aktive rad LoginRadius-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* LoginRadius stöder **SP** -INITIERAd SSO

## <a name="adding-loginradius-from-the-gallery"></a>Lägga till LoginRadius från galleriet

Om du vill konfigurera integreringen av LoginRadius i Azure AD måste du lägga till LoginRadius från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till LoginRadius från galleriet:**

1. Välj ikonen **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj alternativet **alla program** .

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** :

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **LoginRadius** i sökrutan, Välj **LoginRadius** i resultat panelen och välj sedan knappen **Lägg till** för att lägga till programmet.

    ![LoginRadius i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med LoginRadius baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i LoginRadius upprättas.

Om du vill konfigurera och testa enkel inloggning med LoginRadius i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera LoginRadius enkel inloggning](#configure-loginradius-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa LoginRadius test User](#create-loginradius-test-user)** – om du vill ha en motsvarighet till Britta Simon i LoginRadius som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med LoginRadius i Azure AD:

1. Välj **enkel inloggning** på sidan **LoginRadius** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du **Redigera** -ikonen för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** :

   ![Information om enkel inloggning för LoginRadius-domän och URL: er](common/sp-identifier.png)

   1. I text rutan **inloggnings-URL** anger du URL: en `https://secure.loginradius.com/login`

   1. I text rutan **identifierare (enhets-ID)** anger du URL: en `https://lr.hub.loginradius.com/`

   1. I text rutan **svars-URL (intyg om mottagar tjänst)** anger du LoginRadius ACS-URL: en `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Ladda ned** för att ladda ned **XML-metadata för federationsmetadata** från de angivna alternativen enligt ditt krav och sparar det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera LoginRadius** kopierar du lämpliga URL: er enligt ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

   - Inloggnings-URL

   - Azure AD-identifierare

   - Utloggnings-URL

## <a name="configure-loginradius-single-sign-on"></a>Konfigurera LoginRadius Single Sign-On

I det här avsnittet aktiverar du enkel inloggning med Azure AD i administrations konsolen för LoginRadius.

1. Logga in på ditt LoginRadius- [Administratörs konsol](https://adminconsole.loginradius.com/login) konto.

2. Gå till **Team Management** -avsnittet i [LoginRadius-administratörskonsolen](https://secure.loginradius.com/account/team).

3. Välj fliken **enkel inloggning** och välj sedan **Azure AD**:

   ![Skärm bild som visar menyn för enkel inloggning i LoginRadius Team Management-konsolen](./media/loginradius-tutorial/azure-ad.png)
4. På installations sidan för Azure AD utför du följande steg:

   ![Skärm bild som visar Azure Active Directory konfiguration i LoginRadius Team Management-konsolen](./media/loginradius-tutorial/single-sign-on.png)

    1. I **ID-providerns plats** anger du inloggnings slut punkten, som du får från ditt Azure AD-konto.

    1. I **ID-providerns utloggnings-URL** anger du slut punkten för utloggning, som du får från ditt Azure AD-konto.
 
    1. I **ID-providerns certifikat** anger du det Azure AD-certifikat som du får från ditt Azure AD-konto. Ange certifikatets värde med sidhuvud och sidfot. Exempel: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. I certifikat nyckel för **Service Provider** och **Server Provider** anger du ditt certifikat och nyckel. 

       Du kan skapa ett självsignerat certifikat genom att köra följande kommandon på kommando raden (Linux/Mac):

       - Kommando för att hämta certifikat nyckeln för SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Kommando för att hämta certifikatet för SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Se till att ange certifikat-och certifikat nyckel värden med sidhuvud och sidfot:
       > - Exempel format för certifikat värde: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Exempel format för certifikat nyckel värde: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. I avsnittet **data mappning** väljer du fälten (SP Fields) och anger motsvarande Azure AD-fält (IDP-fält).

    Nedan visas några fält namn för Azure AD.

    | Fält    | Profil nyckel                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-post     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Mappning av **e-** postfält krävs. Fält mappningarna **FirstName** och **LastName** är valfria.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Syftet med det här avsnittet är att skapa en test användare som kallas Britta Simon i Azure Portal.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I **användar** egenskaper utför du följande steg.

   ![Dialogrutan Användare](common/user-properties.png)

   1. I fältet **namn** anger du **BrittaSimon**.
  
   1. I fältet **användar namn** anger du `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com.

   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LoginRadius.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **LoginRadius**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **LoginRadius**.

    ![LoginRadius-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj knappen **Lägg till användare** och välj sedan **användare och grupper** i fönstret **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I fönstret **användare och grupper** väljer du **Britta Simon** i listan **användare** och väljer sedan knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i fönstret **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.

7. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** .

### <a name="create-loginradius-test-user"></a>Skapa LoginRadius test användare

1. Logga in på ditt LoginRadius- [Administratörs konsol](https://adminconsole.loginradius.com/login) konto.

2. Gå till Team Management-avsnittet i LoginRadius-administratörskonsolen.

   ![Skärm bild som visar LoginRadius-administratörskonsolen](./media/loginradius-tutorial/team-management.png)
3. Välj **Lägg till grupp medlem** på sido menyn för att öppna formuläret. 

4. I formuläret **Lägg till grupp medlem** skapar du en användare med namnet Britta Simon på din LoginRadius-webbplats genom att ange användarens information och tilldela de behörigheter som du vill att användaren ska ha. Om du vill veta mer om behörigheterna baserat på roller, se avsnittet [roll åtkomst behörigheter](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) i dokumentet LoginRadius [Hantera grupp medlemmar](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) . Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. I en webbläsare går du till https://accounts.loginradius.com/auth.aspx och väljer **mata in enkel inloggning**.
2. Ange namnet på LoginRadius-appen och välj sedan **Logga in**.
3. Det ska öppna ett popup-fönster som ber dig att logga in på ditt Azure AD-konto.
4. Efter autentiseringen stängs ditt popup-fönster och du kommer att loggas in i LoginRadius-administratörskonsolen.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
