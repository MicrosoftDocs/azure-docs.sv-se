---
title: 'Självstudie: Azure Active Directory integration med Mimecast-administratörskonsolen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast Admin Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 7198d341de9de42f28aeafe0fb94273b916482f1
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621286"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med administrations konsolen för Mimecast

I den här självstudien får du lära dig hur du integrerar Mimecast administrations konsol med Azure Active Directory (Azure AD). När du integrerar Mimecast admin-konsolen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Mimecast-administratörskonsolen.
* Gör det möjligt för användarna att logga in automatiskt för att Mimecast administrations konsolen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Mimecast-prenumeration för enkel inloggning (SSO) för administratörs konsolen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Mimecast-administratörskonsolen stöder **SP-och IDP** -INITIERAd SSO

## <a name="add-mimecast-admin-console-from-the-gallery"></a>Lägg till Mimecast-administratörskonsolen från galleriet

För att konfigurera integreringen av Mimecast Admin Console med Azure AD måste du lägga till Mimecast Admin Console från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Mimecast Admin Console** i sökrutan.
1. Välj **Mimecast Admin Console** från panelen resultat och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>Konfigurera och testa Azure AD SSO för Mimecast-administratörskonsolen

Konfigurera och testa Azure AD SSO med administratörs konsolen för Mimecast med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Mimecast-administratörskonsolen.

Utför följande steg för att konfigurera och testa Azure AD SSO med Mimecast-administratörs konsolen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Mimecast Admin Console test User](#create-mimecast-admin-console-test-user)** -för att få en motsvarighet till B. Simon i Mimecast-administratörskonsolen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Mimecast administrations konsol** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP initierat läge, utför följande steg:

    a. I text rutan **identifierare** skriver du URL: en med följande mönster:

    | Region  |  Värde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Sydafrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australien       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Till havs        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Du hittar `accountcode` värdet i administratörs konsolen för Mimecast under **konto**  >  **Inställningar**  >  **konto kod**. Lägg `accountcode` till i identifieraren.

    b. I textrutan **Svars-URL** anger du URL:en: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Till havs        | `https://jer-api.mimecast.com/login/saml`|

1. Om du vill konfigurera programmet i **SP** initierat läge:

    I rutan **Inloggnings-URL** anger du URL:en: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | USA   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | Sydafrika    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | Australien       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | Till havs        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Mimecast-administratörskonsolen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Mimecast Admin Console**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-mimecast-admin-console-sso"></a>Konfigurera Mimecast Admin Console SSO

1. Logga in på Mimecast-administratörskonsolen i ett annat webbläsarfönster.

1. Gå till **administrations**  >  **tjänst**  >  **program**.

    ![Skärm bild som visar Mimecast-fönstret med program valt.](./media/mimecast-admin-console-tutorial/services.png)

1. Klicka på fliken **autentiseringsinställningar** .
    
    ![Skärm bild som visar fliken program med de valda autentiseringsinställningarna.](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Klicka på fliken **ny autentiserings profil** .

    ![Skärm bild som visar ny Autentiseringsidentitet vald.](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Ange en giltig beskrivning i text rutan **Beskrivning** och markera kryss rutan **tvinga SAML-autentisering för administrations konsol** .

    ![Skärm bild som visar var du väljer framtvinga SAML-autentisering för administrations konsolen.](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Utför följande steg på sidan **SAML-konfiguration för administrations konsol** :

    ![Skärm bild som visar sidan SAML-konfiguration för administrations konsolen där du kan ange de värden som beskrivs.](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. För **Provider** väljer du **Azure Active Directory** i list rutan.

    b. I text rutan **metadata-URL** klistrar du in URL-värdet för **app Federation-Metadata** , som du har kopierat från Azure Portal.

    c. Klicka på **Importera**. När du har importerat metadata-URL: en fylls fälten i automatiskt, utan att du behöver utföra några åtgärder på dessa fält.

    d. Se till att avmarkera kryss rutorna **Använd lösenordsskyddad lösen ords skydd** och **Använd integrerade kontexter för autentisering** .

    e. Klicka på **Spara**.

### <a name="create-mimecast-admin-console-test-user"></a>Skapa Mimecast Admin Console-testanvändare

1. Logga in på Mimecast-administratörskonsolen i ett annat webbläsarfönster.

1. Gå till **administrations**  >  **kataloger**  >  **interna kataloger**.

    ![Skärm bild som visar Mimecast-fönster med interna kataloger valda.](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Välj på din domän, om domänen nämns nedan, annars skapar du en ny domän genom att klicka på den **nya domänen**.

    ![Skärm bild som visar den valda domänen.](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Klicka på fliken **ny adress** .

    ![Skärm bild som visar ny adress vald.](./media/mimecast-admin-console-tutorial/new-address.png)

1. Ange nödvändig användar information på följande sida:

    ![Skärm bild som visar sidan där du kan ange de värden som beskrivs.](./media/mimecast-admin-console-tutorial/user-information.png)

    a. I text rutan **e-postadress** anger du användarens e-postadress `B.Simon@yourdomainname.com` .

    b. I text rutan **globala namn** anger du användarens **fullständiga namn** .

    c. I text rutorna **lösen** ord och **Bekräfta lösen ord** anger du användarens lösen ord.

    d. Markera kryss rutan **Framtvinga ändring vid inloggning** .

    e. Klicka på **Spara**.

    f. Om du vill tilldela roller till användaren klickar du på **roll redigera** och tilldelar den nödvändiga rollen till användaren enligt organisationens krav.

    ![Skärm bild som visar adress inställningar där du kan välja roll redigering.](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Mimecast Admin Console-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Mimecast Admin Console inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till Mimecast-administratörskonsolen som du konfigurerar SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen för Mimecast-administratörskonsolen i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Mimecast admin-konsolen som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Mimecast-administratörskonsolen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).