---
title: 'Självstudie: Azure Active Directory integrering med ArcGIS Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: ef64d857cb2215281b50617e030c634618e14dc4
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556580"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Självstudie: Azure Active Directory integrering med ArcGIS Enterprise

I den här självstudien får du lära dig att integrera ArcGIS Enterprise med Azure Active Directory (Azure AD). När du integrerar ArcGIS Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ArcGIS Enterprise.
* Gör det möjligt för användarna att logga in automatiskt till ArcGIS Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ArcGIS Enterprise Single Sign-on (SSO) aktive rad prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ArcGIS Enterprise stöder **SP-och IDP** -initierad SSO.
* ArcGIS Enterprise stöder **just-in-Time** User-etablering.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Lägg till ArcGIS Enterprise från galleriet

För att konfigurera integreringen av ArcGIS Enterprise till Azure AD behöver du lägga till ArcGIS Enterprise från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ArcGIS Enterprise** i sökrutan.
1. Välj **ArcGIS Enterprise** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Konfigurera och testa Azure AD SSO för ArcGIS Enterprise

Konfigurera och testa Azure AD SSO med ArcGIS Enterprise med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ArcGIS Enterprise.

Utför följande steg för att konfigurera och testa Azure AD SSO med ArcGIS Enterprise:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ArcGIS Enterprise SSO](#configure-arcgis-enterprise-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ArcGIS Enterprise test User](#create-arcgis-enterprise-test-user)** – för att få en motsvarighet till B. Simon i ArcGIS Enterprise som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **ArcGIS Enterprise** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg, om du vill konfigurera programmet i **IDP** initierat läge:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<EXTERNAL_DNS_NAME>.portal`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Hämta dessa värden genom att kontakta [supportteamet för ArcGIS Enterprise-klienten](mailto:support@esri.com). Du får identifierarvärdet från avsnittet **Ange identitetsprovider**, som beskrivs senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ArcGIS Enterprise.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **ArcGIS Enterprise**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-arcgis-enterprise-sso"></a>Konfigurera ArcGIS Enterprise SSO

1. Om du vill automatisera konfigurationen inom ArcGIS Enterprise måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera ArcGIS Enterprise** för att dirigera dig till ArcGIS Enterprise-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på ArcGIS Enterprise. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera ArcGIS Enterprise manuellt loggar du in på företags platsen för ArcGIS Enterprise som administratör.


1. Välj **Organization >EDIT SETTINGS** (Organisation > Redigera inställningar).

    ![Skärm bild som visar fliken ArcGIS Enterprise-organisation med redigerings inställningar som kallas för.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Välj fliken **Säkerhet**.

    ![Skärm bild som visar fliken säkerhet vald.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Rulla ned till avsnittet **Enterprise Logins via SAML** (Enterprise-inloggningar via SAML) och välj **SET ENTERPRISE LOGIN** (Ange Enterprise-inloggning).

    ![Skärm bild som visar företags inloggningar via SAML där du kan välja ange företags inloggning.](./media/arcgisenterprise-tutorial/configure-3.png)

1. I avsnittet **Set Identity Provider** (Ange identitetsprovider) utför du följande steg:

    ![Skärm bild som visar en identitets leverantör där du utför stegen som beskrivs här.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Ange ett namn såsom **Azure Active Directory-test** i textrutan **Namn**.

    b. I textrutan **URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    c. Klicka på **Visa avancerade inställningar** och kopiera värdet **Entitets-ID** och klistra in det i textrutan **Identifierare** i avsnittet **ArcGIS Enterprise Domain and URLs** (ArcGIS Enterprise-domän och URL:er) i Azure-portalen.

    ![Skärm bild som visar var du kan hämta entiteten I D och uppdatera identifiera leverantören.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Klicka på **UPDATE IDENTITY PROVIDER** (Uppdatera identitetsprovider).

### <a name="create-arcgis-enterprise-test-user"></a>Skapa ArcGIS Enterprise-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i ArcGIS Enterprise. ArcGIS Enterprise stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i ArcGIS Enterprise skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [ArcGIS Enterprise Support Team](mailto:support@esri.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till ArcGIS Enterprise-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till ArcGIS företags inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till det ArcGIS-företag som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen ArcGIS Enterprise i Mina appar, om det kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på ArcGIS-företaget som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ArcGIS Enterprise kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
