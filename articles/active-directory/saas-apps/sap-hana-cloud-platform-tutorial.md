---
title: 'Självstudie: Azure Active Directory integration med SAP Cloud Platform | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97964056"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Självstudie: Azure Active Directory integration med SAP Cloud Platform

I den här självstudien får du lära dig hur du integrerar SAP Cloud Platform med Azure Active Directory (Azure AD). När du integrerar SAP Cloud Platform med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Cloud Platform.
* Gör det möjligt för användarna att logga in automatiskt till SAP Cloud Platform med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP Cloud Platform så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP Cloud Platform-prenumeration med enkel inloggning aktiverad

När du har slutfört den här självstudien så kommer de Azure AD-användare som du har tilldelat till SAP Cloud Platform att kunna använda enkel inloggning i programmet med [Introduktion till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

>[!IMPORTANT]
>Du måste distribuera ditt eget program eller prenumerera på ett program på ditt SAP Cloud Platform-konto för att testa enkel inloggning. I den här självstudien distribueras ett program i kontot.
> 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud Platform stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Lägga till SAP Cloud Platform från galleriet

Om du vill konfigurera integreringen av SAP Cloud Platform i Azure AD så måste du lägga till SAP Cloud Platform från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SAP Cloud Platform** i sökrutan.
1. Välj **SAP Cloud Platform** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Konfigurera och testa Azure AD SSO för SAP Cloud Platform

Konfigurera och testa Azure AD SSO med SAP Cloud Platform med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud Platform.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAP Cloud Platform:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera SAP Cloud Platform SSO](#configure-sap-cloud-platform-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa en SAP Cloud Platform-testanvändare](#create-sap-cloud-platform-test-user)** – för att få en motpart Britta Simon i SAP Cloud Platform som är länkad till Azure AD-representationen för användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **SAP Cloud Platform** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    ![Information om SAP Cloud Platform och URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **inloggning på URL:en**, skriver du in den URL som används av dina användare för att logga in på ditt **SAP Cloud Platform**-program. Det är den kontospecifika URL:en för en skyddad resurs i ditt SAP Cloud Platform-program. URL:en är baserad på följande mönster: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Det här är URL:en i ditt SAP Cloud Platform-program som kräver att användaren autentiseras.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. I **identifierare**-textrutan så anger du din SAP Cloud Platforms typ, en URL med en av följande mönster: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. I textrutan **Svars-URL** skriver du en URL med följande mönster: 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en, identifierare och svars-URL. Kontakta [supportteamet för SAP Cloud Platform-klienten](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) för att få inloggnings-URL och identifierare. Svars-URL:en kan du hämta från förtroendehanteringsavsnittet som beskrivs senare i självstudien.
    > 
4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Cloud Platform.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAP Cloud Platform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-cloud-platform-sso"></a>Konfigurera SAP Cloud Platform SSO

1. I ett annat webbläsarfönster, loggar du in på SAP Cloud Platform Cockpit på `https://account.<landscape host>.ondemand.com/cockpit`(till exempel: https://account.hanatrial.ondemand.com/cockpit).

2. Klicka på **Förtroende**-fliken.
   
    ![Förtroende](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Förtroende")

3. I avsnittet förtroendehantering under **Lokal tjänstleverantör**, utför du följande steg:

    ![Skärm bild som visar avsnittet "Trust Management" med fliken "lokal tjänst leverantör" markerad och alla text rutor markerade.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Förtroende hantering")
   
    a. Klicka på **Redigera**.

    b. Som **Konfigurationstyp**, väljer du **Anpassad**.

    c. Som **Namn på lokal leverantör**, lämna standardvärdet. Kopiera det här värdet och klistra in det i fältet **Identifierare** i Azure AD-konfigurationen för SAP Cloud Platform.

    d. Generera en **Signeringsnyckel** och ett **Signeringscertifikat**-nyckelpar genom att klicka på **Generera nyckelpar**.

    e. Som **Huvudsaklig spridning** så väljer du **Inaktiverad**.

    f. Som **Tvinga autentisering** så väljer du **Inaktiverad**.

    ex. Klicka på **Spara**.

4. När du har sparat inställningarna för **Lokal tjänstleverantör** så utför du följande för att hämta svars-URL:en:
   
    ![Hämta metadata](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Hämta metadata")

    a. Hämta SAP Cloud Platform-metadatafilen genom att klicka på **Hämta metadata**.

    b. Öppna den hämtade XML-filen för SAP Cloud Platform-metadata och hitta sedan taggen **ns3:AssertionConsumerService**.
 
    c. Kopiera värdet för attributet **Plats** och klistra in det i fältet **Svars-URL** i Azure AD-konfigurationen för SAP Cloud Platform.

5. Klicka på fliken **Betrodd identitetsprovider** och sedan på **Lägg till betrodd identitetsprovider**.
   
    ![Skärm bild som visar sidan "förtroende hantering" med fliken "betrodd identitetsprovider" vald.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Förtroende hantering")
   
    >[!NOTE]
    >Om du vill hantera listan med betrodda identitetsprovidrar så måste du ha valt typ av anpassad konfiguration i avsnittet lokal tjänstleverantör. Som standardkonfigurationstyp har du ett icke-redigerbart och implicit förtroende till SAP-ID-tjänsten. För Ingen så har du inga förtroendeinställningar.
    > 
    > 

6. Klicka på **Allmänt**-fliken och sedan på **Bläddra** för att överföra den hämtade metadatafilen.
    
    ![Förtroende hantering](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Förtroende hantering")
    
    >[!NOTE]
    >När du har överfört metadatafilen så fylls värdena för **URL för enkel inloggning**, **URL för enkel utloggning** och **Signeringscertifikat** i automatiskt.
    > 
     
7. Klicka på fliken **Attribut**.

8. På **Attribut**-fliken, utför du följande steg:
    
    ![Attribut](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attribut") 

    a. Klicka på **Lägg till försäkransbaserat attribut** och lägg sedan till följande försäkransbaserade attribut:
       
    | Försäkransattribut | Huvudnamnsattribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |förnamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |efternamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-post |
   
     >[!NOTE]
     >Konfigurationen av attributen beror på hur programmen på SCP utvecklas, det vill säga vilka attribut som de förväntar sig i SAML-svaret och under vilket namn (huvudnamnsattribut) de har åtkomst till det här attributet i koden.
     > 
    
    b. **Standardattributet** i skärmbilden är bara i illustrativt syfte. Det krävs inte för att få scenariot att fungera.  
 
    c. Namnen och värdena för **huvudnamnsattributet** som visas i skärmbilden beror på hur programmet utvecklas. Det är möjligt att ditt program kräver olika mappningar.

### <a name="assertion-based-groups"></a>Försäkransbaserade grupper

Som ett valfritt steg så kan du konfigurera försäkransbaserade grupper för din Azure Active Directory-identitetsprovider.

Med grupper på SAP Cloud Platform så kan du dynamiskt tilldela en eller flera användare till en eller flera roller i dina SAP Cloud Platform-program, baserat på värden för attribut i SAML 2.0-försäkran. 

Om försäkran till exempel innehåller attributet ”*contract=temporary*” så kan du vilja att alla berörda användare läggs till i gruppen ”*TEMPORARY*”. Gruppen ”*TEMPORARY*” kan innehålla en eller flera roller från en eller flera program som distribueras i ditt SAP Cloud Platform-konto.
 
Använd försäkransbaserade grupper när du vill tilldela många användare samtidigt till en eller flera roller av program i ditt SAP Cloud Platform-konto. Om du bara vill tilldela en enskild eller ett litet antal användare till specifika roller så rekommenderar vi att du tilldelar dem direkt i fliken **Auktoriseringar** i SAP Cloud Platform-cockpiten.

### <a name="create-sap-cloud-platform-test-user"></a>Skapa SAP Cloud Platform-testanvändare

För att låta Azure AD-användare logga in till SAP Cloud Platform så måste du tilldela roller i SAP Cloud Platform till dem.

**Om du vill tilldela en roll till en användare så utför du följande steg:**

1. Logga in på din **SAP Cloud Platform**-cockpit.

2. Utför följande:
   
    ![Auktoriseringar](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Auktoriseringar")
   
    a. Klicka på **Auktorisering**.

    b. Klicka på fliken **användare** .

    c. I textrutan **Användare** så skriver du in användarens e-postadress.

    d. Klicka på **Tilldela** för att tilldela användaren till en roll.

    e. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till SAP Cloud Platform-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till SAP Cloud Platform inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SAP Cloud Platform i Mina appar, bör du loggas in automatiskt på den SAP Cloud-plattform som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAP Cloud Platform kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).