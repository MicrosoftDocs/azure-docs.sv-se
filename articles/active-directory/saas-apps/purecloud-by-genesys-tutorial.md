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
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 8e31c050218e2069354d4d18f812a50f445e596f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med PureCloud av gener

I den här självstudien får du lära dig hur du integrerar PureCloud av gener med Azure Active Directory (Azure AD). När du integrerar PureCloud av gener med Azure AD kan du:

* Kontroll i Azure AD som har till gång till PureCloud av gener.
* Gör det möjligt för användarna att logga in automatiskt till PureCloud av gener med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En PureCloud med enkel inloggning (SSO) – aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PureCloud av gener stöder **SP och IDP**– initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Lägg till PureCloud från gener från galleriet

Om du vill konfigurera integrering av PureCloud av gener i Azure AD måste du lägga till PureCloud från galleriet i listan över hanterade SaaS-appar. Det gör du genom att följa dessa steg:

1. Logga in på Azure Portal med ett arbets-eller skol konto eller genom att använda en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **PureCloud av gener** i sökrutan.
1. Välj **PureCloud av gener** från resultat panelen och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Konfigurera och testa Azure AD SSO för PureCloud av gener

Konfigurera och testa Azure AD SSO med PureCloud genom gener som använder en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PureCloud av gener.

Utför följande steg för att konfigurera och testa Azure AD SSO med PureCloud av gener:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera PureCloud genom gener SSO](#configure-purecloud-by-genesys-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PureCloud av geners test användare](#create-purecloud-by-genesys-test-user)** för att få en motsvarighet till B. Simon i PureCloud av gener som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal på sidan **PureCloud av generad** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda Sign-On metod** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** kan du ange värden för följande fält om du vill konfigurera programmet i **IDP**-initierat läge:

    a. I rutan **identifierare** anger du de webb adresser som motsvarar din region:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. I rutan **svars-URL** anger du de webb adresser som motsvarar din region:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Välj **Ange ytterligare URL: er** och gör följande om du vill konfigurera programmet i läget **SP** -initierat:

    I rutan **inloggnings-URL** anger du de webb adresser som motsvarar din region:
    
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

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PureCloud av gener** kopierar du lämplig URL (eller URL: er) baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare med namnet B. Simon i Azure Portal:

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du användar namnet i följande format: username@companydomain.extension . Exempel: `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PureCloud av gener.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **PureCloud by Genesys**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurera PureCloud av geners SSO

1. Logga in på PureCloud från gener som administratör i ett annat webbläsarfönster.

1. Välj **admin** överst och gå sedan till **enkel inloggning** under **integreringar**.

    ![Skärm bild som visar PureCloud admin-fönstret där du kan välja enkel inloggning.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Växla till fliken **ADFS/Azure AD (Premium)** och följ sedan dessa steg:

    ![Skärm bild visar sidan integrationer där du kan ange de värden som beskrivs.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Välj **Bläddra** för att ladda upp det bas-64-kodade certifikatet som du laddade ned från Azure Portal till **ADFS-certifikatet**.

    b. I rutan **ADFS Issuer URI** klistrar du in värdet för **Azure AD-ID** som du kopierade från Azure Portal.

    c. I rutan **mål-URI** klistrar du in värdet för **inloggnings-URL: en** som du kopierade från Azure Portal.

    d. För den **förlitande partens ID** -värde går du till Azure Portal och väljer fliken **Egenskaper** på sidan **PureCloud av genered** application integration och kopierar **program-ID-** värdet. Klistra in den i rutan **identifierare för förlitande part** .

    ![Skärm bild som visar fönstret Egenskaper där du kan hitta programmets I/a-värde.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Välj **Spara**.

### <a name="create-purecloud-by-genesys-test-user"></a>Skapa PureCloud by Genesys-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på PureCloud av gener, måste de tillhandahållas i PureCloud av gener. I PureCloud by Genesys är etablering en manuell uppgift.

**Följ dessa steg om du vill etablera ett användar konto:**

1. Logga in på PureCloud av gener som administratör.

1. Välj **admin** högst upp och gå till **personer** under **personer & behörigheter**.

    ![Skärm bild som visar PureCloud admin-fönstret där du kan välja personer.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. På sidan **personer** väljer du **Lägg till person**.

    ![Skärm bild som visar sidan personer där du kan lägga till en person.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. I dialog rutan **Lägg till personer i organisationen** följer du dessa steg:

    ![Skärm bild som visar sidan där du kan ange de värden som beskrivs.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. I rutan **fullständigt namn** anger du namnet på en användare. Till exempel: **B. Simon**.

    b. I rutan **e-post** anger du användarens e-postadress. Till exempel: **b. simon \@ contoso.com**.

    c. Välj **Skapa**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till PureCloud av geners inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till PureCloud av geners inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på PureCloud av gener som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen PureCloud av gener i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du logga in automatiskt till PureCloud med gener som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat PureCloud av gener kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
