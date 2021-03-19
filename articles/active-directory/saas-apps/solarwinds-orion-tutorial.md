---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med SolarWinds Orion | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SolarWinds Orion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: jeedes
ms.openlocfilehash: 4ac5bf2756b82361388ab9f2866b80c63395f90d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591392"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SolarWinds Orion

I den här självstudien får du lära dig att integrera SolarWinds-Orion med Azure Active Directory (Azure AD). När du integrerar SolarWinds-Orion med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SolarWinds-Orion.
* Gör det möjligt för användarna att logga in automatiskt till SolarWinds Orion med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SolarWinds Orion-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SolarWinds Orion har stöd för **SP-och IDP** -initierad SSO.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Lägg till SolarWinds Orion från galleriet

Om du vill konfigurera integreringen av SolarWinds-Orion i Azure AD måste du lägga till SolarWinds Orion från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SolarWinds Orion** i sökrutan.
1. Välj **SolarWinds Orion** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Konfigurera och testa Azure AD SSO för SolarWinds-Orion

Konfigurera och testa Azure AD SSO med SolarWinds Orion med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SolarWinds Orion.

Utför följande steg för att konfigurera och testa Azure AD SSO med SolarWinds Orion:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SolarWinds Orion SSO](#configure-solarwinds-orion-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SolarWinds Orion test User](#create-solarwinds-orion-test-user)** – om du vill ha en motsvarighet till B. Simon i SolarWinds Orion som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **SolarWinds Orion** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [SolarWinds Orion client support team](mailto:technicalsupport@solarwinds.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SolarWinds Orion-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig SolarWinds Orion-program att fler attribut skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post |user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera SolarWinds Orion** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Evergreen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Evergreen**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-solarwinds-orion-sso"></a>Konfigurera SolarWinds Orion SSO

1. Logga in på SolarWinds-Orion och gå till **Inställningar**  ->  **alla inställningar**.

    ![Skärm bilden visar alla inställningar som valts från inställningar.](./media/solarwinds-orion-tutorial/settings.png)

1. I avsnittet **användar konton** väljer du **SAML-konfiguration**.

    ![Skärm bild som visar SAML-konfiguration som valts från användar konton.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klicka på **Lägg till identitets leverantör**.

    ![Skärm bild som visar SAML-konfiguration där du kan välja Lägg till identitets leverantör.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Utför följande steg på sidan **Lägg till identitetsprovider** :

    ![Skärm bild som visar sidan Lägg till identitets leverantör där du kan ange de värden som beskrivs.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Gå till fliken **Konfigurera** .

    b. Ange ett giltigt namn som i text rutan **namn på identitets leverantör** `My SSO service` .

    c. I text rutan för **SSO-mål-URL** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    d.  I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    e. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **signerings certifikat för X. 509** .

    f. Klicka på **Spara**.

### <a name="create-solarwinds-orion-test-user"></a>Skapa SolarWinds Orion-test användare

1. Logga in på SolarWinds Orion-webbplatsen och gå till **Inställningar**  ->  **alla inställningar**.

    ![Skärm bilden visar alla inställningar som valts från inställningar.](./media/solarwinds-orion-tutorial/settings.png)

1. I avsnittet **användar konton** väljer du **Hantera konton**.

    ![Skärm bild som visar att SAML-konfigurationen har valts.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. På fliken **enskilda konton** klickar du på **Lägg till nytt konto**.

    ![Skärm bild som visar Lägg till nytt konto valt i Hantera konton.](./media/solarwinds-orion-tutorial/create-user.png)

1. Välj den typ av konto som du behöver för att skapa antingen SAML-enskilda användare eller grupper.

    ![Skärm bild som visar Lägg till nytt konto där du kan välja typ av konto.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  I text rutan **namn-ID** anger du det namn som måste matcha användar namnet eller grupp namnet exakt som i Azure AD.

1.  Klicka på **Nästa** och skicka sedan sidan.

    ![Skärm bild som visar Lägg till nytt konto där du kan ange namnet I D från Azure A D.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SolarWinds Orion-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till SolarWinds Orion-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den SolarWinds-Orion som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SolarWinds Orion i Mina appar, om det kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på SolarWinds-Orion som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SolarWinds Orion kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
