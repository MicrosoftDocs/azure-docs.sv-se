---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Codility | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Codility.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: a27d06774b1795f736497057b129945b617a6258
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495138"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-codility"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Codility

I den här självstudien lär du dig att integrera Codility med Azure Active Directory (Azure AD). När du integrerar Codility med Azure AD kan du:

* Kontrollera vem som har åtkomst till Codility i Azure AD.
* Gör så att dina användare automatiskt loggas in på Codility med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Codility-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Codility stöder **SP- och IDP-initierad** enkel inloggning
* Codility stöder **just-in-time-användareablering**

## <a name="adding-codility-from-the-gallery"></a>Lägga till Codility från galleriet

För att konfigurera integreringen av Codility i Azure AD måste du lägga till Codility från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Codility** i sökrutan.
1. Välj **Codility** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-codility"></a>Konfigurera och testa Enkel inloggning med Azure AD för Codility

Konfigurera och testa enkel inloggning i Azure AD med Codility med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Codility.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Codility:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Codility](#configure-codility-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Codility-testanvändare](#create-codility-test-user)** – för att ha en motsvarighet för B.Simon i Codility som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal går du till **programintegreringssidan för Codility,** hittar **avsnittet Hantera** och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.codility.net/social/complete/saml/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.codility.net`


1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    a. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.codility.net`

    b. I **textrutan Relay State** (Vidarebefordranstillstånd) skriver du ett värde med följande mönster: `<UNIQUE_IDENTIFIER>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en, identifieraren, inloggnings-URL:en och relätillståndet. Kontakta [codility-klientens supportteam](mailto:support@codility.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar **XML** för **federationsmetadata** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I **avsnittet Konfigurera Codility** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Codility.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Codility**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-codility-sso"></a>Konfigurera enkel inloggning för Codility

För att konfigurera enkel inloggning på **Codility-sidan** behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure Portal till [Codility-supportteamet](mailto:support@codility.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-codility-test-user"></a>Skapa Codility-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Codility. Codility stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Codility skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Codilitys inloggnings-URL där du kan initiera inloggningsflödet.  

* Gå till Inloggnings-URL för Codility direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** programmet Azure Portal så bör du automatiskt loggas in på Codility som du har ställt in enkel inloggning för 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på Codility-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på Codility som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Codility kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


