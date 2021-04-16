---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Netskope User Authentication | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netskope-användarautentisering.
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
ms.openlocfilehash: 78ef49740c3e74c4ad5a30838d9721969ca70b36
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517005"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Netskope-användarautentisering

I den här självstudien lär du dig att integrera Netskope-användarautentisering med Azure Active Directory (Azure AD). När du integrerar Netskope-användarautentisering med Azure AD kan du:

* Kontrollera vem som har åtkomst till Netskope-användarautentisering i Azure AD.
* Gör så att dina användare automatiskt loggas in på Netskope-användarautentisering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Netskope User Authentication-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Netskope User Authentication stöder **SP- och IDP-initierad** enkel inloggning.

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Lägga till Netskope-användarautentisering från galleriet

För att konfigurera integreringen av Netskope-användarautentisering i Azure AD måste du lägga till Netskope-användarautentisering från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från galleriet** skriver du **Netskope User Authentication** i sökrutan.
1. Välj **Netskope User Authentication (Netskope-användarautentisering)** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-netskope-user-authentication"></a>Konfigurera och testa Azure AD SSO för Netskope-användarautentisering

Konfigurera och testa azure AD SSO med Netskope-användarautentisering med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Netskope-användarautentisering.

Slutför följande byggstenar för att konfigurera och testa enkel inloggning i Azure AD med Netskope-användarautentisering:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Netskope-användarautentisering](#configure-netskope-user-authentication-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Netskope User Authentication-testanvändare](#create-netskope-user-authentication-test-user)** – för att ha en motsvarighet för B.Simon i Netskope-användarautentisering som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på **programintegreringssidan för Netskope User Authentication** hittar du avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL med följande mönster: `https://<tenantname>.goskope.com/<customer entered string>`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Du får dessa värden förklarade senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I **textrutan Inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Inloggnings-URL-värdena är inte verkliga. Uppdatera inloggnings-URL-värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Netskope User Authentication och](mailto:support@netskope.com) be om värdet för inloggnings-URL. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I **avsnittet Konfigurera Netskope-användarautentisering** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i den Azure Portal med namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Netskope-användarautentisering.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Netskope User Authentication**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurera enkel inloggning för Netskope-användarautentisering

1. Öppna en ny flik i webbläsaren och logga in på netskope-företagswebbplatsen för användarautentisering som administratör.

1. Klicka **på fliken Aktiv** plattform.

    ![Skärmbild som visar aktiv plattform som valts från Inställningar.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Rulla ned till **FORWARD PROXY (VIDAREBEFORDRA PROXY)** och **välj SAML**.

    ![Skärmbild som visar SAML valt från Active Platform.](./media/netskope-user-authentication-tutorial/configuration.png)

1. På sidan **SAML-inställningar** utför du följande steg:

    ![Skärmbild som visar SAML-inställningar där du kan ange de värden som beskrivs.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopiera **värdet för SAML-entitets-ID** och klistra in det i **textrutan** Identifierare i avsnittet Grundläggande **SAML-konfiguration** i Azure Portal.

    b. Kopiera **SAML ACS-URL-värdet** och klistra in det i textrutan Svars-URL i avsnittet **Grundläggande SAML-konfiguration** i Azure Portal. 

1. Klicka **på LÄGG TILL KONTO.**

    ![Skärmbild som visar LÄGG TILL KONTO valt i SAML-fönstret.](./media/netskope-user-authentication-tutorial/add-account.png)

1. På sidan **Lägg till SAML-konto** utför du följande steg:

    ![Skärmbild som visar Lägg till SAML-konto där du kan ange de värden som beskrivs.](./media/netskope-user-authentication-tutorial/configure-settings.png)

    a. I **textrutan NAMN** anger du namnet som Azure AD.

    b. I **textrutan IDP URL (IDP-URL)** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure Portal.

    c. I textrutan **IDP ENTITY ID (ENTitets-ID för IDP)** klistrar du in värdet för **Den Azure AD-identifierare** som du har kopierat från Azure Portal.

    d. Öppna den nedladdade metadatafilen i Anteckningar, kopiera innehållet till Urklipp och klistra in det i **textrutan IDP CERTIFICATE (IDP-CERTIFIKAT).**

    e. Klicka **på SPARA.**

### <a name="create-netskope-user-authentication-test-user"></a>Skapa Netskope User Authentication-testanvändare

1. Öppna en ny flik i webbläsaren och logga in på netskope-företagswebbplatsen för användarautentisering som administratör.

1. Klicka på **fliken Inställningar** i det vänstra navigeringsfönstret.

    ![Skärmbild som visar inställning vald.](./media/netskope-user-authentication-tutorial/configuration-settings.png)

1. Klicka **på fliken Aktiv** plattform.

    ![Skärmbild som visar Aktiv plattform som valts från Inställningar.](./media/netskope-user-authentication-tutorial/user-1.png)

1. Klicka **på fliken** Användare.

    ![Skärmbild som visar användare som valts från Aktiv plattform.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klicka **på LÄGG TILL ANVÄNDARE.**

    ![Skärmbild som visar dialogrutan Användare där du kan välja LÄGG TILL ANVÄNDARE.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Ange e-postadressen för den användare som du vill lägga till och klicka på **LÄGG TILL.**

    ![Skärmbild som visar Lägg till användare där du kan ange en lista över användare.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL:en för Netskope-användarautentisering där du kan initiera inloggningsflödet.  

* Gå till inloggnings-URL:en för Netskope-användarautentisering direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** programmet Azure Portal så bör du automatiskt loggas in på Netskope-användarautentisering som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på panelen för Netskope-användarautentisering i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på Netskope-användarautentisering som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Netskope-användarautentisering kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
