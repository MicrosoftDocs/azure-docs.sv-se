---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med EasySSO för BitBucket-| Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 56f2eea9dc485c69b6070fda6e9519887f7b30cb
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519664"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med EasySSO för BitBucket

I den här självstudien lär du dig att integrera EasySSO for BitBucket med Azure Active Directory (Azure AD). När du integrerar EasySSO for BitBucket med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till EasySSO för BitBucket.
* Gör så att dina användare automatiskt loggas in på EasySSO for BitBucket med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* En prenumeration på EasySSO for BitBucket som är aktiverad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EasySSO för BitBucket har stöd för SP-initierad och IdP-initierad enkel inloggning.
* EasySSO för BitBucket stöder just-in-time-användareablering.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Lägga till EasySSO för BitBucket från galleriet

För att konfigurera integreringen av EasySSO för BitBucket i Azure AD måste du lägga till EasySSO för BitBucket från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **EasySSO for BitBucket** i sökrutan.
1. Välj **EasySSO för BitBucket** i resultatet och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Konfigurera och testa enkel inloggning för Azure AD för EasySSO för BitBucket

Konfigurera och testa enkel inloggning för Azure AD med EasySSO för BitBucket med hjälp av en testanvändare med namnet **B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i EasySSO för BitBucket.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med EasySSO för BitBucket:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att B.Simon kan använda enkel inloggning med Azure AD.
1. [Konfigurera EasySSO för BitBucket SSO](#configure-easysso-for-bitbucket-sso) för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. [Skapa en EasySSO för BitBucket-testanvändare](#create-an-easysso-for-bitbucket-test-user) för att ha en motsvarighet för B.Simon i EasySSO för BitBucket som är länkad till en Azure AD-representation av användaren.
1. [Testa enkel inloggning](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal på **programintegreringssidan för EasySSO for BitBucket,** hittar **du avsnittet** Hantera. Välj **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML väljer** du pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Skärmbild av konfigurera enkel Sign-On med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält om du vill konfigurera programmet i **IdP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL som använder följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. I **textrutan Svars-URL** skriver du en URL som använder följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Välj **Ange ytterligare URL:er** och gör följande om du vill konfigurera programmet i **SP-initierat** läge:

    - I **textrutan Inloggnings-URL** anger du en URL som använder följande mönster: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Supportteamet för EasySSO och](mailto:support@techtime.co.nz) be om dessa värden om du är tveksam. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för BitBucket-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Skärmbild av standardattribut](common/default-attributes.png)

1. EasySSO för BitBucket-programmet förväntar sig också att några fler attribut skickas tillbaka i SAML-svaret. Dessa visas i följande tabell. Dessa attribut är också förifyllda, men du kan granska dem efter behov.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Om dina Azure AD-användare har **sAMAccountName** konfigurerat måste du mappa **urn:oid:0.9.2342.19200300.100.1.1** till **attributet sAMAccountName.**
    
1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet **SAML-signeringscertifikat** och väljer nedladdningslänkarna för alternativen **Certifikat (Base64)** eller **FEDERATION Metadata XML.**  Spara antingen eller båda på datorn. Du behöver den senare för att konfigurera BitBucket EasySSO.

    ![Skärmbild av avsnittet SAML-signeringscertifikat med nedladdningslänkar markerade](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Om du planerar att konfigurera EasySSO för BitBucket manuellt  med ett certifikat måste du också kopiera inloggnings-URL och **Azure AD-identifierare** och spara dem på datorn.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare, B.Simon, i Azure Portal.

1. I det vänstra fönstret i Azure Portal väljer du **Azure Active Directory**  >  **Användare**  >  **Alla användare**.
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn** anger du `B.Simon` .
   1. Som **Användarnamn anger** du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera **kryssrutan Visa** lösenord och skriv sedan ned lösenordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till EasySSO för BitBucket.

1. I den Azure Portal väljer du **Företagsprogram**  >  **Alla program**.
1. I programlistan väljer du **EasySSO för BitBucket**.
1. På appens översiktssida hittar du **avsnittet Hantera** och väljer Användare **och grupper.**
1. Välj **Lägg till användare**. I dialogrutan **Lägg till** tilldelning väljer du Användare **och grupper.**
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i listan **Användare** och väljer **sedan** Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurera EasySSO för BitBucket SSO

1. Om du vill automatisera konfigurationen i Zoom måste du installera **Mina appar** för säker inloggning genom att klicka **på Installera tillägget**.

    ![Mina appar tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar **du på Konfigurera Zoom** så dirigeras du till Zoom-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Zoom. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–10.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Zoom manuellt loggar du in på Zoom-företagswebbplatsen som administratör i ett annat webbläsarfönster.

1. Gå till **avsnittet** Administration.

    ![Skärmbild av BitBucket-instans med kugghjulsikonen markerad](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Leta upp och välj **EasySSO.**

    ![Skärmbild av alternativet Enkel enkel inloggning](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Välj **SAML**. Då kommer du till avsnittet SAML-konfiguration.

    ![Skärmbild av sidan EasySSO-administratör med SAML markerat](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Välj **fliken** Certifikat så visas följande skärm:

    ![Skärmbild av fliken Certifikat med olika alternativ markerade](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Leta upp **certifikatet (Base64) eller** **metadatafilen som** du sparade i föregående avsnitt i den här självstudien. Du kan fortsätta på något av följande sätt:

    - Använd filen med **appfederationmetadata** som du laddade ned till en lokal fil på datorn. Välj **alternativknappen** Ladda upp och följ sökvägen som är specifik för ditt operativsystem.

    - Öppna filen med metadata **för appfederation** om du vill se innehållet i filen i valfri redigerare med oformaterad text. Kopiera den till Urklipp. Välj **Input**(Indata) och klistra in urklippsinnehållet i textfältet.
 
    - Gör en helt manuell konfiguration. Öppna **appfederationscertifikatet (Base64)** om du vill se innehållet i filen i valfri redigeringsprogram med oformaterad text. Kopiera den till Urklipp och klistra in den i textfältet **IdP-tokensigneringscertifikat.** Gå sedan till fliken **Allmänt och** fyll i fälten **POST-bindnings-URL** och Entitets-ID med respektive värden för inloggnings-URL och **Azure AD-identifierare** som du sparade tidigare.  
 
1. Välj **Spara** längst ned på sidan. Du ser att innehållet i metadata- eller certifikatfilerna parsas till konfigurationsfälten. EasySSO för BitBucket-konfigurationen är klar.

1. Testa konfigurationen genom att gå till fliken **Look & Feel** (Sök efter känsla) och välja **SAML Login Button (SAML-inloggningsknapp).** Detta aktiverar en separat knapp på BitBucket-inloggningsskärmen, särskilt för att testa Azure AD SAML-integreringen från start till slut. Du kan lämna den här knappen på och konfigurera dess placering, färg och översättning för produktionsläge också.

    ![Skärmbild av SAML-sidan Look & Feel (Look & Feel) med SAML-inloggningsknappen markerad](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Kontakta Supportteamet för [EasySSO om](mailto:support@techtime.co.nz)du har problem.

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Skapa en EasySSO för BitBucket-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i BitBucket. EasySSO för BitBucket stöder just-in-time-användareablering, vilket är inaktiverat som standard. Om du vill aktivera det måste du uttryckligen markera **Skapa användare** vid lyckad inloggning i avsnittet **Allmänt** i Konfigurationen av EasySSO-plugin-programmet. Om det inte redan finns någon användare i BitBucket skapas en ny efter autentisering.

Men om du inte vill aktivera automatisk användareablering när användaren loggar in, måste användarna finnas i användarkataloger som instansen av BitBucket använder. Den här katalogen kan till exempel vara LDAP eller Atlassian Crowd.

![Skärmbild av avsnittet Allmänt i konfigurationen av EasySSO-plugin-programmet med Skapa användare vid lyckad inloggning markerat](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ.

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till EasySSO för BitBucket-inloggnings-URL där du kan initiera inloggningsflödet.

* Gå till Inloggnings-URL för EasySSO för BitBucket direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** programmet Azure Portal så bör du automatiskt loggas in på EasySSO för BitBucket som du har ställt in enkel inloggning för

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på EasySSO for BitBucket-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på EasySSO för BitBucket som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EasySSO för BitBucket kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).