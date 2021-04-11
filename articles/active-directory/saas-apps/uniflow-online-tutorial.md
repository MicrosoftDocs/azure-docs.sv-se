---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med uniFLOW online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och uniFLOW online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952719"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med uniFLOW online

I den här självstudien får du lära dig hur du integrerar uniFLOW online med Azure Active Directory (Azure AD). När du integrerar uniFLOW online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till uniFLOW online.
* Gör det möjligt för användarna att logga in på uniFLOW online med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* uniFLOW online-klient.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* uniFLOW online stöder **SP** -INITIERAd SSO

## <a name="add-uniflow-online-from-the-gallery"></a>Lägg till uniFLOW online från galleriet

Om du vill konfigurera integreringen av uniFLOW online i Azure AD måste du lägga till uniFLOW online från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **uniFLOW online** i sökrutan.
1. Välj **uniFLOW online** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>Konfigurera och testa Azure AD SSO för uniFLOW online

Konfigurera och testa Azure AD SSO med uniFLOW online med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i uniFLOW online.

Utför följande steg för att konfigurera och testa Azure AD SSO med uniFLOW online:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
   1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera uniFLOW online SSO](#configure-uniflow-online-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Logga in på uniFLOW online med hjälp av den skapade test användaren](#sign-in-to-uniflow-online-using-the-created-test-user)** – för att testa användar inloggningen på program sidan.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **uniFLOW online** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med något av följande mönster:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL med något av följande mönster:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [uniFLOW online](mailto:support@nt-ware.com) för att hämta dessa värden. Du kan också se de mönster som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal eller referera till svars-URL: en som visas i uniFLOW online-klienten.

1. uniFLOW online-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig uniFLOW-online-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | -----------| --------------- |
    | displayname (visningsnamn) | user.displayname |
    | smek namn | User. egna namnet onpremisessamaccountname |

   > [!NOTE]
   > `user.onpremisessamaccountname`Attributet innehåller bara ett värde om dina Azure AD-användare synkroniseras från en lokal Windows-Active Directory.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till uniFLOW online.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **uniFLOW online**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

> [!NOTE]
> Om du vill tillåta alla användare att komma åt programmet utan manuell tilldelning går du till avsnittet **Hantera** och väljer **Egenskaper**. Ändra sedan parametern för **användar tilldelning som krävs** till **Nej**.

## <a name="configure-uniflow-online-sso"></a>Konfigurera uniFLOW online SSO

1. Logga in på uniFLOW Online-webbplatsen som administratör i ett annat webbläsarfönster.

1. Välj fliken **användare** i den vänstra navigerings panelen.

    ![Skärm bild som visar användaren som valts från webbplatsen uniFLOW online.](./media/uniflow-online-tutorial/configure-1.png)

1. Klicka på **identitets leverantörer**.

    ![Skärm bild som visar de valda identitets leverantörerna.](./media/uniflow-online-tutorial/configure-2.png)

1. Klicka på **Lägg till identitets leverantör**.

    ![Skärm bild som visar Lägg till identitets leverantör vald.](./media/uniflow-online-tutorial/configure-3.png)

1. I avsnittet **Lägg till identitetsprovider** utför du följande steg:

    ![Skärm bild som visar avsnittet Lägg till identitets leverantör där du kan ange de värden som beskrivs.](./media/uniflow-online-tutorial/configure-4.png)

    a. Ange visnings namnet t. ex.: *AZUREAD SSO*.

    b. För **typ av Provider** väljer du alternativet **WS-utfodras** i list rutan.

    c. För **WS-utfodras typ** väljer du **Azure Active Directory** alternativ i list rutan.

    d. Klicka på **Spara**.

1. Utför följande steg på fliken **Allmänt** :

    ![Skärm bild som visar fliken Allmänt där du kan ange de värden som beskrivs.](./media/uniflow-online-tutorial/configure-5.png)

    a. Ange visnings namnet t. ex.: *AZUREAD SSO*.

    b. Välj alternativet **från URL** för **ADFS federation-metadata**.

    c. I text rutan **URL för federationsmetadata** klistrar du in URL-värdet för **app Federation-Metadata** , som du har kopierat från Azure Portal.

    d. Välj **identitets leverantör** som **aktive rad**.

    e. Välj **Automatisk användar registrering** som **aktive rad**.

    f. Klicka på **Spara**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Logga in på uniFLOW online med den skapade test användaren

1. Gå till uniFLOW online-URL: en för din klient organisation i ett annat webbläsarfönster.

1. Välj den tidigare skapade identitets leverantören för inloggning via Azure AD-instansen.

1. Logga in med test användaren.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till uniFLOW online-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till uniFLOW online-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen uniFLOW online i Mina appar omdirigeras detta till uniFLOW online-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat uniFLOW online kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).