---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Netskope Administratörskonsol | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netskope Administratörskonsol.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 8435cab1855e9df871d17ff7fa393b6ab2cf0cb1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Netskope Administratörskonsol

I den här självstudien får du lära dig att integrera Netskope-Administratörskonsol med Azure Active Directory (Azure AD). När du integrerar Netskope-Administratörskonsol med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netskope Administratörskonsol.
* Gör det möjligt för användarna att logga in automatiskt för att Netskope Administratörskonsol med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Netskope Administratörskonsol enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Netskope Administratörskonsol stöder **SP-och IDP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Lägga till Netskope Administratörskonsol från galleriet

Om du vill konfigurera integreringen av Netskope-Administratörskonsol i Azure AD måste du lägga till Netskope Administratörskonsol från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriv **Netskope administratörskonsol** i sökrutan.
1. Välj **Netskope administratörskonsol** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Konfigurera och testa Azure AD SSO för Netskope Administratörskonsol

Konfigurera och testa Azure AD SSO med Netskope Administratörskonsol med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Netskope Administratörskonsol.

Utför följande steg för att konfigurera och testa Azure AD SSO med Netskope Administratörskonsol:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Netskope ADMINISTRATÖRSKONSOL SSO](#configure-netskope-administrator-console-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Netskope administratörskonsol test User](#create-netskope-administrator-console-test-user)** – om du vill ha en motsvarighet till B. Simon i Netskope-administratörskonsol som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Netskope administratörskonsol** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Du får det värde som beskrivs senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Inloggnings-URL-värdena är inte riktiga. Uppdatera inloggnings-URL-värdet med den faktiska inloggnings-URL: en. Kontakta [Netskope administratörskonsol client support team](mailto:support@netskope.com) för att få inloggnings-URL-värde. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Netskope Administratörskonsol-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Netskope-Administratörskonsol program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ---------| --------- |
    | admin-roll | user.assignedroles |

    > [!NOTE]
    > Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta hur du skapar roller i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Netskope administratörskonsol** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Netskope Administratörskonsol.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Netskope administratörskonsol**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurera Netskope Administratörskonsol SSO

1. Öppna en ny flik i webbläsaren och logga in på din Netskope Administratörskonsol företags plats som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigerings fönstret.

    ![Skärm bild som visar den valda inställningen i navigerings fönstret.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicka på fliken **Administration** .

    ![Skärm bild som visar administration som valts från inställningar.](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klicka på **SSO** -fliken.

    ![Skärm bild som visar S O valt i administration.](./media/netskope-cloud-security-tutorial/config-sso.png)

1. I avsnittet **nätverks inställningar** utför du följande steg:
    
    ![Skärm bild som visar nätverks inställningar där du kan ange de värden som beskrivs.](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Kopiera **intygets URL-** värde och klistra in det i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Kopiera **tjänste leverantörens ID-** värde och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. Klicka på **Redigera inställningarna** under Inställningar för **SSO/service nivå** .

    ![Skärm bild som visar S O/S L O-inställningar där du kan välja Redigera inställningar.](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. I popup-fönstret **Inställningar** utför du följande steg:

    ![Skärm bild som visar dialog rutan inställningar där du kan ange de värden som beskrivs.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Välj **Aktivera SSO**.

    b. I text rutan **IDP URL** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    c. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    d. Öppna det hämtade base64-kodade certifikatet i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan **IDP-certifikat** .

    e. Välj **Aktivera SSO**.

    f. I text rutan **IDP service nivå mål** klistrar du in URL-värdet för **utloggning** , som du har kopierat från Azure Portal.

    ex. Klicka på **Skicka**.

### <a name="create-netskope-administrator-console-test-user"></a>Skapa Netskope Administratörskonsol test användare

1. Öppna en ny flik i webbläsaren och logga in på din Netskope Administratörskonsol företags plats som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigerings fönstret.

    ![Skärm bilden visar valda inställningar.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicka på fliken **Active Platform** .

    ![Skärm bild som visar aktiv plattform som valts från inställningar.](./media/netskope-cloud-security-tutorial/user1.png)

1. Klicka på fliken **användare** .

    ![Skärm bild som visar användare som valts från aktiv plattform.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klicka på **Lägg till användare**.

    ![Skärm bild som visar dialog rutan användare där du kan välja Lägg till användare.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Ange e-postadressen för den användare som du vill lägga till och klicka på **Lägg till**.

    ![Skärm bild som visar Lägg till användare där du kan ange en lista över användare.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Netskope Administratörskonsol inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Netskope Administratörskonsol inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så ska du loggas in automatiskt på den Netskope administratörskonsol som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Netskope Administratörskonsol i Mina appar, om det kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på Netskope Administratörskonsol som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Netskope Administratörskonsol du använda sessionsbaserade, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
