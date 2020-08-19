---
title: 'Självstudie: självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP NetWeaver | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP NetWeaver.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: jeedes
ms.openlocfilehash: 38ac4f1bf6a1dd4656b4e7d5783051f3b381940c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-netweaver"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP NetWeaver

I den här självstudien får du lära dig hur du integrerar SAP NetWeaver med Azure Active Directory (Azure AD). När du integrerar SAP NetWeaver med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP NetWeaver.
* Gör det möjligt för användarna att logga in automatiskt till SAP NetWeaver med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP NetWeaver-prenumeration med enkel inloggning (SSO) aktive rad.
* SAP NetWeaver V7.20 krävs minst

## <a name="scenario-description"></a>Scenariobeskrivning

SAP NetWeaver stöder både **SAML** (**SP initierad SSO**) och **OAuth**. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

> [!NOTE]
> Konfigurera programmet antingen i SAML eller i OAuth enligt organisationens behov. 

## <a name="adding-sap-netweaver-from-the-gallery"></a>Lägga till SAP NetWeaver från galleriet

Om du vill konfigurera integreringen av SAP NetWeaver i Azure Active Directory måste du lägga till SAP NetWeaver från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SAP NetWeaver** i sökrutan.
1. Välj **SAP-NetWeaver** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-netweaver"></a>Konfigurera och testa enkel inloggning med Azure AD för SAP NetWeaver

Konfigurera och testa Azure AD SSO med SAP NetWeaver med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP NetWeaver.

Om du vill konfigurera och testa Azure AD SSO med SAP NetWeaver slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera SAP-NetWeaver med SAML](#configure-sap-netweaver-using-saml)** för att konfigurera SSO-inställningar på program sidan.
    1. **[Skapa SAP NetWeaver-test](#create-sap-netweaver-test-user)** för att få en motsvarighet till B. Simon i SAP NetWeaver som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.
1. **[Konfigurera SAP-NetWeaver för OAuth](#configure-sap-netweaver-for-oauth)** för att konfigurera OAuth-inställningar på program sidan.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure Active Directory med SAP NetWeaver genom att göra följande:

1. Öppna ett nytt webbläsarfönster och logga in på din SAP NetWeaver-företags webbplats som administratör

1. Se till att tjänsterna **http** och **https** är aktiva och att lämpliga portar tilldelas i transaktionskoden **SMICM**.

1. Logga in på Business-klienten för SAP system (T01), där SSO krävs och aktivera hantering av HTTP-säkerhetssessioner.

    a. Gå till transaktionskod **SICF_SESSIONS**. Den visar alla relevanta profilparametrar med aktuella värden. De ser ut så här:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Justera parametrarna ovan enligt organisationens krav. Parametrarna ovan anges här endast som indikation.

    b. Om det behövs justerar du parametrarna i instans/standard profil för SAP-systemet och startar om SAP-systemet.

    c. Dubbelklicka på relevant klient för att aktivera HTTP-säkerhetssessionen.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivera nedan SICF-tjänsterna nedan:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
1. Gå till transaktionskod **SAML2** i SAP-systemets företagsklient [T01/122]. Ett användargränssnitt öppnas i en webbläsare. I det här exemplet antog vi att SAP-företagsklienten är 122.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

1. Ange ditt användarnamn och lösenord som ska anges i användargränssnittet och klicka på **Redigera**.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

1. Ersätt **providernamn** från T01122 till `http://T01122` och klicka på **Spara**.

    > [!NOTE]
    > Som standard visas namnet för providern `<sid><client>` , men Azure AD förväntar sig namn i formatet `<protocol>://<name>` , och rekommenderar att du behåller namnet på providern så `https://<sid><client>` att flera SAP NetWeaver ABAP-motorer kan konfigureras i Azure AD.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

1. **Generera metadata för tjänstleverantör**: – När vi är klara med att konfigurera inställningarna för den **lokala leverantören** och **betrodda leverantörer** i användargränssnittet för SAML 2.0 är nästa steg att generera tjänstleverantörens metadatafil (som innehåller alla inställningar, när det gäller autentisering och andra konfigurationer i SAP). När den här filen har genererats måste vi ladda upp den i Azure AD.

    ![Länk för nedladdning av certifikatet](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Gå till **fliken Local Provider** (Lokal provider).

    b. Klicka på **Metadata**.

    c. Spara den genererade **XML-filen för metadata** på datorn och ladda upp den i **grundläggande SAML-konfiguration** för att automatiskt fylla i **ID** -och **svars-URL** -värden i Azure Portal.

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integration i **SAP NetWeaver** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. Klicka på **Ladda upp metadatafil** för att ladda upp **metadata-filen för tjänst leverantören**som du har hämtat tidigare.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i textrutan i avsnittet **Grundläggande SAML-konfiguration** enligt följande:

    d. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Vi har fått rapporter från några kunder om ett fel med felaktig svars-URL som har konfigurerats för deras instans. Om du får ett sådant fel kan du använda följande PowerShell-skript som en tillfällig åtgärd för att ange rätt svars-URL för din instans:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ServicePrincipal Object ID ska anges av dig först, eller så kan du skicka det också här.

1. SAP NetWeaver-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Välj **ExtractMailPrefix()** i listan **Transformering**.

    c. I listan **parameter 1** väljer du **User. UserPrincipalName**.

    d. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SAP-NetWeaver** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP NetWeaver.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan över program väljer du **SAP NetWeaver**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-netweaver-using-saml"></a>Konfigurera SAP-NetWeaver med SAML

1. Logga in på SAP-systemet och gå till transaktions koden SAML2. Ett nytt webbläsarfönster öppnas med en skärm för konfiguration av SAML.

2. För att konfigurera slutpunkter för betrodda identitetsproviders (Azure AD) går du till fliken **Trusted Providers** (Betrodda providers).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Tryck på **Lägg till** och välj **Ladda upp metadatafil** på snabbmenyn.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Ladda upp metadatafilen som du har laddat ned från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Ange aliasnamnet på nästa skärm, Till exempel aadsts och Fortsätt genom att klicka på **Nästa** .

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Se till att din **sammandragsalgoritm** ska vara **SHA-256** och att den inte kräver några ändringar och tryck på **Nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. På **Single Sign-On Endpoints** (Slutpunkter för enkel inloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. På **Single Logout Endpoints** (Slutpunkter för enkel utloggning) använder du **HTTP POST** och klickar på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. På **Artifact Endpoints** (Slutpunkter för artefakter) trycker du på **Nästa** för att fortsätta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Vid **Autentiseringskrav** klickar du på **Slutför**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Gå till fliken **Trusted Provider**  >  **Identity Federation** (längst ned på skärmen). Klicka på **Redigera**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klicka på **Lägg till** under fliken **Identitetsfederation** (det nedre fönstret).

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. I popup-fönstret väljer du **ospecificerad** från de NameID- **format som stöds** och klickar på OK.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Observera att värdena för **användar-ID-källa** och **användar-ID-mappning** anger länken mellan SAP-användare och Azure AD-anspråk.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scenario: SAP-användare till användar mappning i Azure AD.

    a. Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scenario: Välj SAP-användar-ID baserat på konfigurerad e-postadress i SU01. I det här fallet ska e-post-ID konfigureras i su01 för varje användare som kräver SSO.

    a.  Skärmbild med NameID-information från SAP.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. skärmbild som nämner nödvändiga anspråk från Azure AD.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klicka på **Spara** och klicka sedan på **Aktivera** för att aktivera identitetsprovider.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration1.png)

16. Klicka på **OK** när du uppmanas till detta.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/configuration2.png)

    ### <a name="create-sap-netweaver-test-user"></a>Skapa SAP NetWeaver-testanvändare

    I det här avsnittet skapar du en användare som heter B. Simon i SAP NetWeaver. Arbeta med ditt interna SAP-expertteam eller med din organisations SAP-partner för att lägga till användare i SAP NetWeaver-plattformen.

## <a name="test-sso"></a>Testa SSO

1. När identitetsprovidern Azure AD aktiverades försöker du öppna nedanstående URL för att kontrollera SSO (det kommer ingen uppmaning för användarnamn och lösenord)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (eller) använd webbadressen nedan

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Ersätt sapurl med själva SAP-värdnamnet.

2. Webbadressen ovan bör ta dig under den nämnda skärmen. Om du kan komma till sidan nedan är Azure AD SSO-installationen klar.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/testingsso.png)

3. Om du ser en uppmaning för användarnamn och lösenord ska du diagnostisera problemet genom att aktivera spårning med hjälp av nedanstående URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="configure-sap-netweaver-for-oauth"></a>Konfigurera SAP-NetWeaver för OAuth

1. SAP-dokumenterad process finns på platsen: [NetWeaver Gateway Service som aktiverar och OAuth 2,0-omfånget skapas](https://wiki.scn.sap.com/wiki/display/Security/NetWeaver+Gateway+Service+Enabling+and+OAuth+2.0+Scope+Creation)

2. Gå till SPRO och hitta **Aktivera och underhålla tjänster**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth01.png)

3. I det här exemplet vill vi ansluta OData-tjänsten: `DAAG_MNGGRP` med OAuth till Azure AD SSO. Använd den tekniska tjänstens namns sökning för tjänsten `DAAG_MNGGRP` och aktivera om den inte är aktiv, redan (Sök efter `green` status under fliken ICF-noder). Se till att system Ali Aset (det anslutna Server dels systemet, där den tjänst som faktiskt körs) är korrekt.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth02.png)

    * Klicka sedan på knapp tryckning **OAuth** i det övre knapp fältet och tilldela `scope` (behåll standard namnet som erbjuds).

4. I vårt exempel är-området `DAAG_MNGGRP_001` genererat från tjänst namnet genom att automatiskt lägga till ett nummer. Rapporten `/IWFND/R_OAUTH_SCOPES` kan användas för att ändra namnet på omfånget eller skapa manuellt.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth03.png)

    > [!NOTE]
    > Meddelande `soft state status is not supported` – kan ignoreras utan problem. Mer information finns [här](https://help.sap.com/doc/saphelp_nw74/7.4.16/1e/c60c33be784846aad62716b4a1df39/content.htm?no_cache=true)

### <a name="create-a-service-user-for-the-oauth-20-client"></a>Skapa en tjänst användare för OAuth 2,0-klienten

1. OAuth2 använder en `service ID` för att hämta åtkomsttoken för slutanvändaren för dess räkning. Viktig begränsning av OAuth-design: `OAuth 2.0 Client ID` måste vara identisk med `username` OAuth 2,0-klienten använder för inloggning när en åtkomsttoken begärs. För vårt exempel ska vi registrera en OAuth 2,0-klient med namnet KLIENT1 och som en förutsättning att en användare med samma namn (KLIENT1) måste finnas i SAP-systemet och att den användaren ska konfigureras för användning av det hänvisade programmet. 

2. När du registrerar en OAuth-klient använder vi `SAML Bearer Grant type` .

    >[!NOTE]
    >Mer information finns i OAuth 2,0-klient registrering för SAML Bearer-beviljande typ [här](https://wiki.scn.sap.com/wiki/display/Security/OAuth+2.0+Client+Registration+for+the+SAML+Bearer+Grant+Type)

3. tcod: SU01/skapa användare KLIENT1 som `System type` och tilldela lösen ord, spara den som behov av att tillhandahålla autentiseringsuppgiften till API-programmerare, som ska bränna det med användar namnet för den anropande koden. Ingen profil eller roll ska tilldelas.

### <a name="register-the-new-oauth-20-client-id-with-the-creation-wizard"></a>Registrera det nya OAuth 2,0-klient-ID: t med guiden skapa

1. Registrera en ny **OAuth 2,0-klients** start transaktion **SOAUTH2**. Transaktionen visar en översikt över de OAuth 2,0-klienter som redan har registrerats. Välj **skapa** för att starta guiden för den nya OAuth-klienten med namnet KLIENT1 i det här exemplet.

2. Gå till T-Code: **SOAUTH2** och ange beskrivningen och klicka sedan på **Nästa**.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth04.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth05.png)

3. Välj den redan tillagda **SAML2-IDP – Azure AD** i list rutan och spara.

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth06.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth07.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth08.png)

4. Lägg till det tidigare skapade omfånget genom att klicka på **Lägg till** under tilldelning av scope: `DAAG_MNGGRP_001`

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth09.png)

    ![Konfigurera enkel inloggning](./media/sapnetweaver-tutorial/oauth10.png)

5. Klicka på **Slutför**.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP NetWeaver med Azure AD](https://aad.portal.azure.com/)