---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med PureCloud av gener | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0d2953710c559deb094761410e6d46401e103d8b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med PureCloud av gener

I den här självstudien får du lära dig hur du integrerar PureCloud av gener med Azure Active Directory (Azure AD). När du har gjort det kan du:

* Använd Azure AD för att kontrol lera vilka användare som har åtkomst till PureCloud av gener.
* Gör det möjligt för användarna att logga in automatiskt till PureCloud av gener med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En PureCloud med enkel inloggning (SSO) – aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PureCloud av gener stöder **SP och IDP**– initierad SSO.

> [!NOTE]
> Eftersom ID: t för det här programmet är ett fast sträng värde kan endast en instans konfigureras i en klient.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Lägga till PureCloud by Genesys från galleriet

Om du vill konfigurera integrering av PureCloud av gener i Azure AD måste du lägga till PureCloud från galleriet i listan över hanterade SaaS-appar. Det gör du genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller genom att använda en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **PureCloud av gener** i sökrutan.
1. Välj **PureCloud av gener** från resultat panelen och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Konfigurera och testa enkel inloggning med Azure AD för PureCloud av gener

Konfigurera och testa Azure AD SSO med PureCloud genom gener som använder en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PureCloud av gener.

Om du vill konfigurera och testa Azure AD SSO med PureCloud av gener, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera PureCloud genom gener SSO](#configure-purecloud-by-genesys-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en PureCloud av geners test användare](#create-purecloud-by-genesys-test-user)** för att få en motsvarighet till B. Simon i PureCloud av gener som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **PureCloud av generad** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** kan du ange värden för följande fält om du vill konfigurera programmet i **IDP**-initierat läge:

    a. I rutan **identifierare** anger du en URL som motsvarar din region:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. I rutan **svars-URL** anger du en URL som motsvarar din region:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Välj **Ange ytterligare URL: er** och gör följande om du vill konfigurera programmet i läget **SP** -initierat:

    I rutan **inloggnings-URL** anger du en URL som motsvarar din region:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. PureCloud by Genesys-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärm bild visar en lista över standardattribut:

    ![image](common/default-attributes.png)

1. Dessutom förväntar sig PureCloud av Generning att fler attribut skickas tillbaka i SAML-svaret, vilket visas i följande tabell. De här attributen är också i förväg ifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | ---------------| --------------- |
    | E-post | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PureCloud av gener** kopierar du lämplig URL (eller URL: er) baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal:

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du användar namnet i följande format: username@companydomain.extension . Till exempel: `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du konfigurera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PureCloud av gener.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **PureCloud by Genesys**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och väljer sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och väljer sedan knappen **Välj** längst ned på skärmen.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurera PureCloud av geners SSO

1. Logga in på PureCloud från gener som administratör i ett annat webbläsarfönster.

1. Välj **admin** överst och gå sedan till **enkel inloggning** under **integreringar**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Växla till fliken **ADFS/Azure AD (Premium)** och följ sedan dessa steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Välj **Bläddra** för att ladda upp det bas-64-kodade certifikatet som du laddade ned från Azure Portal till **ADFS-certifikatet**.

    b. I rutan **ADFS Issuer URI** klistrar du in värdet för **Azure AD-ID** som du kopierade från Azure Portal.

    c. I rutan **mål-URI** klistrar du in värdet för **inloggnings-URL: en** som du kopierade från Azure Portal.

    d. För den **förlitande partens ID** -värde går du till Azure Portal och väljer fliken **Egenskaper** på sidan **PureCloud av genered** application integration och kopierar **program-ID-** värdet. Klistra in den i rutan **identifierare för förlitande part** .

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Välj **Spara**.

### <a name="create-purecloud-by-genesys-test-user"></a>Skapa PureCloud by Genesys-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på PureCloud av gener, måste de tillhandahållas i PureCloud av gener. I PureCloud by Genesys är etablering en manuell uppgift.

**Följ dessa steg om du vill etablera ett användar konto:**

1. Logga in på PureCloud av gener som administratör.

1. Välj **admin** högst upp och gå till **personer** under **personer & behörigheter**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure03.png)

1. På sidan **personer** väljer du **Lägg till person**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure04.png)

1. I dialog rutan **Lägg till personer i organisationen** följer du dessa steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. I rutan **fullständigt namn** anger du namnet på en användare. Till exempel: **B. Simon**.

    b. I rutan **e-post** anger du användarens e-postadress. Till exempel: **b. simon \@ contoso.com**.

    c. Välj **Skapa**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen **PureCloud av gener** på åtkomst panelen, bör du loggas in automatiskt på PureCloud efter gener-konto som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure AD ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PureCloud av gener med Azure AD](https://aad.portal.azure.com/)
