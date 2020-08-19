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
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: f3029acd791e7c45eb5943d298189430ac308e99
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528479"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med administrations konsolen för Mimecast

I den här självstudien får du lära dig hur du integrerar Mimecast administrations konsol med Azure Active Directory (Azure AD). När du integrerar Mimecast admin-konsolen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Mimecast-administratörskonsolen.
* Gör det möjligt för användarna att logga in automatiskt för att Mimecast administrations konsolen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Mimecast-prenumeration för enkel inloggning (SSO) för administratörs konsolen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Mimecast-administratörskonsolen stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat Mimecast-administratörskonsolen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Lägga till Mimecast Admin Console från galleriet

För att konfigurera integreringen av Mimecast Admin Console med Azure AD måste du lägga till Mimecast Admin Console från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Mimecast Admin Console** i sökrutan.
1. Välj **Mimecast Admin Console** från panelen resultat och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-admin-console"></a>Konfigurera och testa enkel inloggning med Azure AD för Mimecast-administratörskonsolen

Konfigurera och testa Azure AD SSO med administratörs konsolen för Mimecast med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Mimecast-administratörskonsolen.

Om du vill konfigurera och testa Azure AD SSO med Mimecast-administratörskonsolen slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Mimecast Admin Console SSO](#configure-mimecast-admin-console-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Mimecast Admin Console test User](#create-mimecast-admin-console-test-user)** -för att få en motsvarighet till B. Simon i Mimecast-administratörskonsolen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Mimecast administrations konsol** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP initierat läge, utför följande steg:

    a. I textrutan **Identifierare** anger du en URL med följande mönster:

    | Region  |  Värde | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Sydafrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australien       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Till havs        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Du hittar `accountcode` värdet i administratörs konsolen för Mimecast under **konto**  >  **Inställningar**  >  **konto kod**. Lägg `accountcode` till i identifieraren.

    b. I textrutan **Svars-URL** anger du en URL: 

    | Region  |  Värde | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Sydafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Till havs        | `https://jer-api.mimecast.com/login/saml`|

1. Om du vill konfigurera programmet i **SP** initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: 

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

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Mimecast-administratörskonsolen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Mimecast Admin Console**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-mimecast-admin-console-sso"></a>Konfigurera Mimecast Admin Console SSO

1. Logga in på Mimecast-administratörskonsolen i ett annat webbläsarfönster.

1. Gå till **administrations**  >  **tjänst**  >  **program**.

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/services.png)

1. Klicka på fliken **autentiseringsinställningar** .
    
    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. Klicka på fliken **ny autentiserings profil** .

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. Ange en giltig beskrivning i text rutan **Beskrivning** och markera kryss rutan **tvinga SAML-autentisering för administrations konsol** .

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. Utför följande steg på sidan **SAML-konfiguration för administrations konsol** :

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. För **Provider**väljer du **Azure Active Directory** i list rutan.

    b. I text rutan **metadata-URL** klistrar du in URL-värdet för **app Federation-Metadata** , som du har kopierat från Azure Portal.

    c. Klicka på **Importera**. När du har importerat metadata-URL: en fylls fälten i automatiskt, utan att du behöver utföra några åtgärder på dessa fält.

    d. Se till att avmarkera kryss rutorna **Använd lösenordsskyddad lösen ords skydd** och **Använd integrerade kontexter för autentisering** .

    e. Klicka på **Spara**.

### <a name="create-mimecast-admin-console-test-user"></a>Skapa Mimecast Admin Console-testanvändare

1. Logga in på Mimecast-administratörskonsolen i ett annat webbläsarfönster.

1. Gå till **administrations**  >  **kataloger**  >  **interna kataloger**.

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. Välj på din domän, om domänen nämns nedan, annars skapar du en ny domän genom att klicka på den **nya domänen**.

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/domain-name.png)

1. Klicka på fliken **ny adress** .

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/new-address.png)

1. Ange nödvändig användar information på följande sida:

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/user-information.png)

    a. I text rutan **e-postadress** anger du användarens e-postadress `B.Simon@yourdomainname.com` .

    b. I text rutan **globala namn** anger du användarens **fullständiga namn** .

    c. I text rutorna **lösen** ord och **Bekräfta lösen ord** anger du användarens lösen ord.

    d. Markera kryss rutan **Framtvinga ändring vid inloggning** .

    e. Klicka på **Spara**.

    f. Om du vill tilldela roller till användaren klickar du på **roll redigera** och tilldelar den nödvändiga rollen till användaren enligt organisationens krav.

    ![Konfiguration av Mimecast-administratörs konsol](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Mimecast Admin Console på åtkomstpanelen bör du loggas in automatiskt i Mimecast Admin Console-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Mimecast admin-konsolen med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Mimecast-administratörskonsolen med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
