---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med EasySSO for Confluence | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: ef121112bb4ff3ff6a297677a5c713f642cb3b78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519602"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med EasySSO for Confluence

I den här självstudien lär du dig att integrera EasySSO for Confluence med Azure Active Directory (Azure AD). När du integrerar EasySSO for Confluence med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till Confluence.
* Gör så att dina användare automatiskt loggas in på Confluence med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* EasySSO for Confluence-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EasySSO for Confluence stöder **SP- och IDP-initierad** enkel inloggning.
* EasySSO för Confluence stöder **just-in-time-användareablering.**

## <a name="add-easysso-for-confluence-from-the-gallery"></a>Lägga till EasySSO for Confluence från galleriet

För att konfigurera integreringen av EasySSO for Confluence i Azure AD måste du lägga till EasySSO for Confluence från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **EasySSO for Confluence** i sökrutan.
1. Välj **EasySSO for Confluence** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Konfigurera och testa Azure AD SSO för EasySSO for Confluence

Konfigurera och testa enkel inloggning med Azure AD med EasySSO for Confluence med hjälp av en testanvändare med namnet **B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i EasySSO for Confluence.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med EasySSO for Confluence:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera EasySSO för Confluence SSO](#configure-easysso-for-confluence-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa EasySSO för Confluence-testanvändare](#create-easysso-for-confluence-test-user)** – för att ha en motsvarighet för B.Simon i EasySSO for Confluence som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för EasySSO for Confluence** går du till avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I **textrutan Inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för EasySSO och](mailto:support@techtime.co.nz) be om dessa värden om du är osäker. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för Confluence-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig EasySSO for Confluence-programmet att några fler attribut skickas tillbaka i SAML-svaret som visas nedan. Dessa attribut är också ifyllda i förväg, men du kan granska dem enligt dina behov.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Om dina Azure AD-användare har **sAMAccountName** konfigurerat måste du mappa **urn:oid:0.9.2342.19200300.100.1.1** till **attributet sAMAccountName.**
    
1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** klickar på Ladda ned länkar för certifikat **(Base64)** eller **XML-alternativ** för federationsmetadata och sparar antingen eller alla på datorn.  Du behöver den senare för att konfigurera Confluence EasySSO.

    ![Länk för nedladdning av certifikatet](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Om du planerar att utföra EasySSO för Confluence-konfiguration  manuellt med certifikat, måste du också kopiera inloggnings-URL och **Azure AD-identifierare** från avsnittet nedan och spara dem på datorn.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till EasySSO for Confluence.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **EasySSO för Confluence**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-easysso-for-confluence-sso"></a>Konfigurera EasySSO för Confluence SSO

1. Om du vill automatisera konfigurationen i EasySSO for Confluence måste du installera **Mina appar-webbläsartillägget** för säker inloggning genom att klicka **på Installera tillägget**.

    ![Mina appar tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på Konfigurera **EasySSO for Confluence** så dirigeras du till EasySSO for Confluence-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på EasySSO för Confluence. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–9.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera EasySSO för Confluence manuellt loggar du in på din Atlassian Confluence-instans med administratörsbehörighet och går till **avsnittet Hantera** appar. 

    ![Hantera appar](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. På vänster sida letar du upp **EasySSO** och klickar på den. Klicka sedan **på knappen** Konfigurera.

    ![Enkel enkel inloggning](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Välj **SAML-alternativ.** Då kommer du till avsnittet SAML-konfiguration.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Välj **fliken** Certifikat högst upp så visas följande skärm: 

    ![Metadata-URL](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Leta nu upp **Certifikatet (Base64) eller** **metadatafilen som** du har sparat i de tidigare stegen i Konfigurationen för enkel inloggning med Azure **AD.** Du har följande alternativ för hur du fortsätter:

    a. Använd filen med **appfederationmetadata** som du laddade ned till en lokal fil på datorn. Välj **alternativknappen** Ladda upp och följ dialogrutan ladda upp fil som är specifik för ditt operativsystem

    **OR**

    b. Öppna filen med metadata **för appfederation** för att se innehållet (i valfri redigeringsprogram för oformaterad text) och kopiera den till Urklipp. Välj **alternativet Indata** och klistra in urklippsinnehåll i textfältet.
 
    **OR**

    c. Fullständigt manuell konfiguration. Öppna **appfederationscertifikatet (Base64)** för att se innehållet (i valfri redigeringsprogram med oformaterad text) och kopiera det till Urklipp. Klistra in den i **textfältet IdP-tokensigneringscertifikat.** Gå sedan till **fliken Allmänt och** fyll i fälten POST Binding URL **(POST-bindnings-URL)** och Entity ID (Entitets-ID) med respektive värden för Inloggnings-URL och **Azure AD-identifierare** som du sparade tidigare.  
 
6. Klicka **på** knappen Spara längst ned på sidan. Du ser att innehållet i metadata- eller certifikatfilerna parsas till konfigurationsfälten. EasySSO för Confluence-konfigurationen är klar.

7. För bästa testupplevelse går du till fliken Look & Feel (Look **& Feel)** och markerar alternativet **SAML Login Button (SAML-inloggningsknapp)** på. Detta aktiverar separat knapp på Confluence-inloggningsskärmen specifikt för att testa Azure AD SAML-integreringen från start till slut. Du kan lämna den här knappen på och konfigurera dess placering, färg och översättning för produktionsläge också.

    ![Look & Feel](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Om du har problem kontaktar du [supportteamet för EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-confluence-test-user"></a>Skapa EasySSO för Confluence-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Confluence. EasySSO för Confluence stöder just-in-time-användareablering, vilket är **inaktiverat** som standard. Om du vill aktivera användareablering  måste du uttryckligen markera alternativet Skapa användare vid lyckad inloggning i avsnittet Allmänt i konfiguration av EasySSO-plugin-programmet. Om det inte redan finns någon användare i Confluence skapas en ny efter autentisering.

Men om du inte vill aktivera automatisk användareablering vid användarens första inloggning måste användarna finnas i de kataloger för backend-användare som Confluence-instansen använder, till exempel LDAP eller Atlassian Crowd.

![Användaretablering](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Testa enkel inloggning 

### <a name="idp-initiated-workflow"></a>IdP-initierat arbetsflöde

I det här avsnittet testar du konfigurationen för enkel inloggning med Azure AD med hjälp av Mina appar.

När du klickar på panelen EasySSO for Confluence i Mina appar bör du automatiskt loggas in på Confluence-instansen som du har ställt in enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>SP-initierat arbetsflöde

I det här avsnittet testar du konfigurationen av enkel inloggning i Azure AD med hjälp av confluence **SAML-inloggningsknappen.**

![SAML-användarinloggning](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Det här scenariot förutsätter att du har aktiverat **SAML-inloggningsknappen** i fliken Look **& Feel** på konfigurationssidan för Confluence EasySSO (se ovan). Öppna confluence-inloggnings-URL:en i webbläsarens inkognitoläge för att undvika störningar i dina befintliga sessioner. Klicka **på knappen SAML Login (SAML-inloggning)** så omdirigeras du till azure AD-användarautentiseringsflödet. När det är klart omdirigeras du tillbaka till confluence-instansen som autentiserad användare via SAML.

Det finns en risk att du stöter på följande skärm när du har omdirigerats tillbaka från Azure AD

![Skärmen EasySSO-fel](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

I det här fallet måste du följa [anvisningarna på den här sidan för]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) att få åtkomst till filen **atlassian-confluence.log.** Information om felet kommer att vara tillgänglig via referens-ID:t som finns på felsidan för EasySSO.

Om du har problem med att sammanfatta loggmeddelandena kontaktar du [supportteamet för EasySSO](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EasySSO för Confluence kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).