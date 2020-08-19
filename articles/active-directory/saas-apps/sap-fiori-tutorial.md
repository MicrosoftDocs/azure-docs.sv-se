---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP Fiori | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.openlocfilehash: 4d2f6766fa32beb9cebaa8f77c04f6865e15e14d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP Fiori

I den här självstudien får du lära dig hur du integrerar SAP Fiori med Azure Active Directory (Azure AD). När du integrerar SAP Fiori med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Fiori.
* Gör det möjligt för användarna att logga in automatiskt till SAP Fiori med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP Fiori-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAP Fiori stöder **SP** -INITIERAd SSO

> [!NOTE]
> För SAP Fiori-initierad iFrame-autentisering rekommenderar vi att du använder **IsPassive** -parametern i SAML-AuthnRequest för tyst autentisering. Mer information om **IsPassive** -parametern finns i information om [enkel inloggning med Azure AD SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)

## <a name="adding-sap-fiori-from-the-gallery"></a>Lägga till SAP-Fiori från galleriet

Om du vill konfigurera integreringen av SAP-Fiori i Azure AD måste du lägga till SAP Fiori från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SAP Fiori** i sökrutan.
1. Välj **SAP-Fiori** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-fiori"></a>Konfigurera och testa enkel inloggning med Azure AD för SAP Fiori

Konfigurera och testa Azure AD SSO med SAP Fiori med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP Fiori.

Om du vill konfigurera och testa Azure AD SSO med SAP Fiori slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SAP Fiori SSO](#configure-sap-fiori-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en SAP Fiori-test](#create-sap-fiori-test-user)** för att få en motsvarighet till B. Simon i SAP Fiori som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. Öppna ett nytt webbläsarfönster och logga in på din SAP Fiori-företags webbplats som administratör.

1. Se till att **http-** och **https** -tjänsterna är aktiva och att relevanta portar är kopplade till transaktions koden **SMICM**.

1. Logga in på SAP Business-klienten för SAP system **T01**, där enkel inloggning krävs. Aktivera sedan hantering av HTTP-säkerhetssession.

    1. Gå till transaktions kod **SICF_SESSIONS**. Alla relevanta profil parametrar med aktuella värden visas. De ser ut som i följande exempel:

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
        > Justera parametrarna utifrån dina organisations krav. Föregående parametrar anges bara som ett exempel.

    1. Vid behov kan du justera parametrarna i instansens (standard) profil i SAP-systemet och starta om SAP-systemet.

    1. Dubbelklicka på den aktuella klienten om du vill aktivera en HTTP-säkerhetssession.

        ![Sidan aktuella värden för relevanta profil parametrar i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivera följande SICF-tjänster:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Gå till transaktions koden **SAML2** i Business-klienten för SAP **-system [T01/122**]. Konfigurations gränssnittet öppnas i ett nytt webbläsarfönster. I det här exemplet använder vi företags klienten för SAP system 122.

    ![Inloggnings sidan för SAP Fiori Business client](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Ange ditt användar namn och lösen ord och välj sedan **Logga in**.

    ![SAML 2,0-konfigurationen av ABAP system T01/122-sidan i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. I rutan **Providernamn** ersätter du **T01122** med **http: \/ /T01122**och väljer sedan **Spara**.

    > [!NOTE]
    > Som standard är leverantörs namnet i formatet \<sid> \<client> . Azure AD förväntar sig namnet i formatet \<protocol> :// \<name> . Vi rekommenderar att du underhåller Providerns namn som https \: // \<sid> \<client> så att du kan konfigurera flera SAP Fiori ABAP-motorer i Azure AD.

    ![Namnet på den uppdaterade providern i SAML 2,0-konfigurationen av ABAP system T01/122-sidan i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Välj **fliken lokal Provider**  >  **metadata**.

1. I dialog rutan **SAML 2,0 metadata** laddar du ned XML-filen med genererade metadata och sparar den på din dator.

    ![Länken Hämta metadata i dialog rutan SAP SAML 2,0 metadata](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integration i **SAP Fiori** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls **ID** -och **svars-URL** -värdena automatiskt i fönstret **grundläggande SAML-konfiguration** . I rutan **inloggnings-URL** anger du en URL som har följande mönster: `https:\//\<your company instance of SAP Fiori\>` .

    > [!NOTE]
    > Några kunder rapporterar fel relaterade till felaktigt konfigurerade **svars-URL** -värden. Om du ser det här felet kan du använda följande PowerShell-skript för att ange rätt svars-URL för din instans:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Du kan ange `ServicePrincipal` objekt-ID: t själv innan du kör skriptet, eller så kan du skicka det här.

1. Fiori-programmet i SAP förväntar sig att SAML-intygen ska vara i ett särskilt format. Konfigurera följande anspråk för det här programmet. Om du vill hantera dessa attributvärden går du till fönstret **Konfigurera enkel inloggning med SAML** och väljer **Redigera**.

    ![Fönstret användarattribut](common/edit-attribute.png)

1. I fönstret **användarattribut & anspråk** konfigurerar du SAML-token som visas i föregående bild. Utför sedan följande steg:

    1. Välj **Redigera** för att öppna fönstret **hantera användar anspråk** .

    1. I listan **omvandling** väljer du **ExtractMailPrefix ()**.

    1. I listan **parameter 1** väljer du **User. UserPrincipalName**.

    1. Välj **Spara**.

       ![Fönstret hantera användar anspråk](./media/sapfiori-tutorial/nameidattribute.png)

       ![Transformering-avsnittet i fönstret hantera användar anspråk](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera SAP-Fiori** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Fiori.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SAP Fiori**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-fiori-sso"></a>Konfigurera SAP Fiori SSO

1. Logga in på SAP-systemet och gå till transaktions koden **SAML2**. Ett nytt webbläsarfönster öppnas med sidan SAML-konfiguration.

1. Om du vill konfigurera slut punkter för en betrodd identitets leverantör (Azure AD) väljer du fliken **betrodda providers** .

    ![Fliken Betrodda providers i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Välj **Lägg till**och välj sedan **Ladda upp metadatafil** från snabb menyn.

    ![Fil alternativ för att lägga till och ladda upp metadata i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Ladda upp metadatafilen som du laddade ned i Azure Portal. Välj **Nästa**.

    ![Välj den metadatafil som ska laddas upp i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Ange aliasnamnet på nästa sida i rutan **alias** . Till exempel **aadsts**. Välj **Nästa**.

    ![Rutan alias i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Kontrol lera att värdet i rutan **Digest algorithm** är **SHA-256**. Välj **Nästa**.

    ![Verifiera värdet för Digest-algoritmen i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. Under **slut punkter för enkel inloggning**väljer du **http post**och väljer sedan **Nästa**.

    ![Alternativ för enkel inloggnings slut punkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. Under **enkel utloggning-slutpunkter**väljer du **http-omdirigering**och väljer sedan **Nästa**.

    ![Alternativ för enskilda utloggnings slut punkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. Under **artefakt slut punkter**väljer du **Nästa** för att fortsätta.

    ![Alternativ för artefakt slut punkter i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. Under **autentiseringskrav**väljer du **Slutför**.

    ![Alternativ för autentiseringskrav och alternativet Slutför i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Välj **Trusted Provider**  >  **identitets Federation** för betrodd Provider (längst ned på sidan). Välj **Redigera**.

    ![Flikarna betrodda Provider och identitets Federation i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Välj **Lägg till**.

    ![Alternativet Lägg till på fliken identitets Federation](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. I dialog rutan **NameID format som stöds** väljer du **ospecificerad**. Välj **OK**.

    ![Dialog rutan NameID format som stöds och alternativ i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Värdena för läget **användar-ID källa** och **användar-ID-mappning** bestämmer länken mellan SAP-användaren och Azure AD-anspråket.  

    **Scenario 1**: användar mappning för SAP-användare till Azure AD

    1. I SAP, under **information om NameID-format "Ospecificerat"**, Observera information:

        ![Information om NameID-formatet "ospecificerad" i SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. I Azure Portal, under **användarattribut & anspråk**, noterar du nödvändiga anspråk från Azure AD.

        ![Dialog rutan användarattribut &-anspråk i Azure Portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scenario 2**: Välj det SAP-användar-ID som baseras på den konfigurerade e-postadressen i SU01. I det här fallet ska e-postadressen konfigureras i SU01 för varje användare som kräver SSO.

    1.  I SAP, under **information om NameID-format "Ospecificerat"**, Observera information:

        ![Information om NameID-formatet "ospecificerad" i SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. I Azure Portal, under **användarattribut & anspråk**, noterar du nödvändiga anspråk från Azure AD.

       ![Dialog rutan användarattribut &-anspråk i Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Välj **Spara**och välj sedan **Aktivera** för att aktivera identitets leverantören.

    ![Alternativen Spara och aktivera i SAP](./media/sapfiori-tutorial/configuration1.png)

1. Välj **OK** när du uppmanas till det.

    ![Alternativet OK i dialog rutan för SAML 2,0-konfiguration i SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Skapa SAP Fiori-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i SAP Fiori. Arbeta med din interna SAP-grupp experter eller din organisations SAP-partner för att lägga till användaren på SAP Fiori-plattformen.

## <a name="test-sso"></a>Testa SSO

1. När identitetsprovider Azure AD har Aktiver ATS i SAP Fiori försöker du komma åt någon av följande URL: er för att testa enkel inloggning (du bör inte uppmanas att ange ett användar namn och lösen ord):

    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm
    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm

    > [!NOTE]
    > Ersätt *sapurl* med det faktiska SAP-värdnamnet.

1. Test-URL: en ska gå till följande test program sida i SAP. Om sidan öppnas konfigureras enkel inloggning för Azure AD.

    ![Standard sidan för test program i SAP](./media/sapfiori-tutorial/testingsso.png)

1. Om du uppmanas att ange ett användar namn och lösen ord, kan du aktivera trace för att diagnostisera problemet. Använd följande URL för trace: https: \/ / \<sapurl\> /SAP/BC/WebDynpro/SAP/sec_diag_tool? SAP-client = 122&SAP-language = en #.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Fiori med Azure AD](https://aad.portal.azure.com/)
