---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med EasySSO för BitBucket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 21e5da3884cce6e3a7beff297e40fdc48a3ac761
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724365"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EasySSO för BitBucket

I den här självstudien får du lära dig hur du integrerar EasySSO för BitBucket med Azure Active Directory (Azure AD). När du integrerar EasySSO för BitBucket med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EasySSO för BitBucket.
* Gör det möjligt för användarna att logga in automatiskt till EasySSO för BitBucket med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration på EasySSO för BitBucket som är aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EasySSO för BitBucket stöder SP-initierad och IdP-initierad SSO.
* EasySSO för BitBucket har stöd för "just-in-Time"-användar etablering.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Lägg till EasySSO för BitBucket från galleriet

Om du vill konfigurera integreringen av EasySSO för BitBucket i Azure AD måste du lägga till EasySSO för BitBucket från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **EasySSO för BitBucket** i sökrutan.
1. Välj **EasySSO för BitBucket** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Konfigurera och testa Azure AD SSO för EasySSO för BitBucket

Konfigurera och testa Azure AD SSO med EasySSO för BitBucket med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i EasySSO för BitBucket.

Utför följande steg för att konfigurera och testa Azure AD SSO med EasySSO för BitBucket:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera EasySSO för BITBUCKET SSO](#configure-easysso-for-bitbucket-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. [Skapa en EasySSO för BitBucket-test användare](#create-an-easysso-for-bitbucket-test-user) för att få en motsvarighet till B. Simon i EasySSO för BitBucket, som är länkad till Azure AD-representation av användare.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **EasySSO för BitBucket** program integration letar du upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkla Sign-On med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL som använder följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. I text rutan **svars-URL** anger du en URL som använder följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Välj **Ange ytterligare URL: er** och gör följande om du vill konfigurera programmet i läget **SP** -initierat:

    - I text rutan **inloggnings-URL** anger du en URL som använder följande mönster: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [EasySSO support-teamet](mailto:support@techtime.co.nz) för att få dessa värden om du är tveksam. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för BitBucket-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av standardattribut](common/default-attributes.png)

1. EasySSO för BitBucket-programmet förväntar sig också några fler attribut som ska skickas tillbaka i SAML-svaret. I följande tabell visas dessa. Dessa attribut har också fyllts i automatiskt, men du kan granska dem enligt dina krav.
    
    | Namn | Källattribut|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Om dina Azure AD-användare har konfigurerat **sAMAccountName** måste du mappa **urn: OID: 0.9.2342.19200300.100.1.1** till attributet **sAMAccountName** .
    
1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du Hämta Länkar för **certifikatets (base64)** eller **federationsmetadata XML-** alternativ. Spara antingen eller båda på din dator. Du behöver den senare för att kunna konfigurera BitBucket-EasySSO.

    ![Skärm bild av avsnittet SAML-signeringscertifikat med markerade hämtnings länkar](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Om du planerar att konfigurera EasySSO för BitBucket manuellt med ett certifikat, måste du också kopiera **inloggnings-URL** och **Azure AD-identifierare** och spara dem på din dator.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare, B. Simon, i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. Som **namn** anger du `B.Simon` .
   1. För **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EasySSO för BitBucket.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **EasySSO för BitBucket**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** och väljer sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurera EasySSO för BitBucket SSO

1. Om du vill automatisera konfigurationen i zoom måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kommer du direkt till zoomnings programmet genom att klicka på **Konfigurera zoomning** . Därifrån anger du administratörsautentiseringsuppgifter för att logga in på zoom. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-10.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera zoom manuellt i ett annat webbläsarfönster loggar du in på den sammanställda företags platsen som administratör.

1. Gå till avsnittet **Administration** .

    ![Skärm bild av BitBucket-instansen med kugg hjuls ikon markerad](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Leta upp och välj **EasySSO**.

    ![Skärm bild av enkel SSO-alternativ](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Välj **SAML**. Då går du till avsnittet SAML-konfiguration.

    ![Skärm bild av sidan EasySSO-administratör med SAML markerat](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Välj fliken **certifikat** så visas följande skärm bild:

    ![Skärm bild av fliken certifikat med olika alternativ markerade](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Leta upp **certifikatet (base64)** eller **metadatafilen** som du sparade i föregående avsnitt i den här självstudien. Du kan fortsätta på något av följande sätt:

    - Använd den app Federation- **metadatafil** som du laddade ned till en lokal fil på din dator. Välj knappen **överför** alternativ och följ den sökväg som är speciell för ditt operativ system.

    - Öppna appens Federations **ETA data** för att se innehållet i filen i valfri text redigerare. Kopiera den till Urklipp. Välj **inmatade** filer och klistra in innehållet i Urklipp i textfältet.
 
    - Gör en helt manuell konfiguration. Öppna appens Federations **certifikat (base64)** om du vill se innehållet i filen i en vanlig text redigerare. Kopiera den till Urklipp och klistra in den i text fältet **IDP token Signature certificates** . Gå sedan till fliken **Allmänt** och fyll i fälten **post kopplings-URL** och **entitets-ID** med respektive värde för **inloggnings-URL** och **Azure AD-identifierare** som du sparade tidigare.
 
1. Välj **Spara** längst ned på sidan. Du ser att innehållet i metadata eller certifikatfiler parsas i konfigurations fälten. EasySSO för BitBucket-konfigurationen har slutförts.

1. Testa konfigurationen genom att gå till fliken **titta & känsla** och välj **knappen SAML-inloggning**. Detta aktiverar en separat knapp på BitBucket-inloggnings skärmen, speciellt för att testa Azure AD SAML-integrationen från slut punkt till slut punkt. Du kan lämna den här knappen på och konfigurera dess placering, färg och översättning för produktions läget.

    ![Skärm bild av SAML-sidan Titta & Känn på fliken med knappen SAML-inloggning markerad](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Om du har problem kan du kontakta [EasySSO support-teamet](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Skapa en EasySSO för BitBucket-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i BitBucket. EasySSO för BitBucket har stöd för just-in-Time User-etablering, som är inaktive rad som standard. Om du vill aktivera det måste du uttryckligen kontrol lera att **skapa användare vid lyckad inloggning** i avsnittet **Allmänt** i EasySSO-plugin-programmet. Om en användare inte redan finns i BitBucket skapas en ny efter autentiseringen.

Men om du inte vill aktivera automatisk användar etablering när användaren loggar in måste användare finnas i användar kataloger som instansen av BitBucket använder. Katalogen kan till exempel vara LDAP eller Atlassian-överfullhet.

![Skärm bild av avsnittet Allmänt i EasySSO-plugin-konfigurationen med skapa användare för lyckad inloggning markerad](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till EasySSO för BitBucket inloggnings-URL där du kan starta inloggnings flödet.

* Gå till EasySSO för BitBucket inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till EasySSO för BitBucket som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen EasySSO för BitBucket i Mina appar, om det kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på EasySSO för BitBucket som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EasySSO för BitBucket kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).