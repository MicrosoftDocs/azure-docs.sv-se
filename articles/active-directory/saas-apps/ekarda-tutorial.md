---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ekarda | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: d9e118620cb38e94cfc18d01d31888ac0a444bb7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813436"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ekarda

I den här självstudien får du lära dig hur du integrerar ekarda med Azure Active Directory (Azure AD). När du integrerar ekarda med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ekarda.
* Gör det möjligt för användarna att logga in automatiskt på ekarda med hjälp av deras Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ekarda-prenumeration som är aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ekarda stöder SP-initierad och IDP-initierad SSO.
* ekarda stöder just-in-Time-etablering av användare.

## <a name="add-ekarda-from-the-gallery"></a>Lägg till ekarda från galleriet

Om du vill konfigurera integreringen av ekarda i Azure AD lägger du till ekarda från galleriet i listan över hanterade SaaS-appar:

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.

1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till det nya programmet väljer du **nytt program**.
1. I avsnittet **Lägg till från galleriet** , skriver du **ekarda** i sökrutan.
1. Välj **ekarda** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Konfigurera och testa Azure AD SSO för ekarda

Konfigurera och testa Azure AD SSO med ekarda med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i ekarda.

Utför följande steg för att konfigurera och testa Azure AD SSO med ekarda:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.

    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera EKARDA SSO](#configure-ekarda-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    * [Skapa en ekarda-testanvändare](#create-an-ekarda-test-user) som ska ha en motsvarighet till B. Simon i ekarda som är länkad till Azure AD-representation av användaren.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen i Azure Portal för att aktivera Azure AD SSO:

1. Logga in på Azure-portalen.
1. På sidan **ekarda** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för att redigera de grundläggande inställningarna för **SAML-konfigurationen** .

   ![Skärm bild som visar hur du konfigurerar enkla Sign-On med SAML-sidan med Penn ikonen markerad.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du ser **filen med tjänst leverantörens metadata**, följer du dessa steg:
    1. Välj **Ladda upp metadatafil**.
    1. Välj mappikonen för att välja metadatafilen och välj sedan **Ladda upp**.
    1. När metadatafilen har laddats upp visas värdena för **identifierare** och **svars-URL** automatiskt i text rutan ekarda-avsnitt.

    > [!Note]
    > Om **ID** -och **svars-URL** -värden inte visas automatiskt fyller du i värdena manuellt enligt dina krav.

1. Om du inte ser **metadata-filen för tjänst leverantören** i avsnittet **grundläggande SAML-konfiguration** och du vill konfigurera programmet i IDP läge, anger du värden för följande fält:

    1. I text rutan **identifierare** anger du en URL som följer det här mönstret: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. I text rutan **svars-URL** skriver du en URL som följer det här mönstret: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och gör följande:

    I text rutan **inloggnings-URL** anger du en URL som följer det här mönstret:  `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Värdena i de två föregående stegen är inte verkliga. Uppdatera dem med den faktiska identifieraren, svars-URL: en och inloggnings-URL-värden. Kontakta [ekarda-klientens support team](mailto:contact@ekarda.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Ladda ned** för att spara **certifikat (base64)** på datorn.

    ![Skärm bild av avsnittet SAML-signeringscertifikat på sidan Konfigurera enkla Sign-On med SAML, med hämtnings länken markerad för base64-certifikatet.](common/certificatebase64.png)

1. I avsnittet **Konfigurera ekarda** kopierar du lämpliga URL: er baserat på dina krav.

    ![Skärm bild av avsnittet Ställ in ekarda på sidan Konfigurera enskilda Sign-On med SAML, med länkar för länkad URL markerat.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du använda Azure Portal för att skapa en test användare som heter B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ange till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ekarda.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **ekarda**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-ekarda-sso"></a>Konfigurera ekarda SSO

1. Om du vill automatisera konfigurationen i ekarda måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera ekarda** för att dirigera dig till ekarda-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på ekarda. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera ekarda manuellt i ett annat webbläsarfönster loggar du in på ekarda Company-platsen som administratör.

1. Välj **admin**  >  **mitt konto**.

    ![Skärm bild av användar gränssnittet för ekarda-webbplatsen med mitt konto markerat på administratörs menyn.](./media/ekarda-tutorial/ekarda.png)

1. Längst ned på sidan, letar du upp avsnittet **SAML-inställningar** . I det här avsnittet konfigurerar du SAML-integreringen.
1. I avsnittet **SAML-inställningar** följer du dessa steg:

    ![Skärm bild av sidan med ekarda SAML-inställningar med fält för SAML-konfiguration markerade.](./media/ekarda-tutorial/ekarda1.png)

    1. Välj länken till **tjänst leverantörens metadata** och spara den som en fil på din dator.
    1. Markera kryss rutan **Aktivera SAML** .
    1. I text rutan **entitets-ID för IDP** klistrar du in det ID-värde för **Azure AD** som du kopierade tidigare från Azure Portal.
    1. I text rutan **inloggnings-URL för IDP** klistrar du in URL-värdet för **inloggning** som du kopierade tidigare från Azure Portal.
    1. I text rutan **IDP-utloggnings-URL** klistrar du in URL-värdet för **utloggning** som du kopierade tidigare från Azure Portal.
    1. Använd anteckningar för att öppna den **certifikat fil (base64)** som du laddade ned från Azure Portal. Klistra in innehållet i text rutan **IDP x509-certifikat** .
    1. Markera kryss rutan **Aktivera service nivå mål** i avsnittet **alternativ** .
    1. Välj **Uppdatera**.

### <a name="create-an-ekarda-test-user"></a>Skapa en ekarda-test användare

I det här avsnittet skapas en användare som heter B. Simon i ekarda. ekarda stöder just-in-Time-etablering, som är aktiverat som standard. Du har ingen åtgärd att vidta i det här avsnittet. Om en användare som heter B. Simon inte redan finns i ekarda skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till ekarda-inloggnings-URL där du kan starta inloggnings flödet.

* Gå till ekarda-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den ekarda som du har konfigurerat SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen ekarda i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på ekarda som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ekarda kan du framtvinga kontroll av sessionen. Den här försiktighets åtgärden skyddar mot exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från Appkontroll för villkorsstyrd åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).