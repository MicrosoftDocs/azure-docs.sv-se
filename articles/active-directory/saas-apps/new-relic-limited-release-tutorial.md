---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och New Relic.
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
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517614"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med New Relic

I den här självstudien lär du dig att integrera New Relic med Azure Active Directory (Azure AD). När du integrerar New Relic med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till New Relic.
* Gör så att dina användare automatiskt loggas in på New Relic med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* En New Relic-prenumeration som är aktiverad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* New Relic har stöd för enkel inloggning som initieras av antingen tjänstleverantören eller identitetsprovidern.

## <a name="add-new-relic-from-the-gallery"></a>Lägga New Relic från galleriet

För att konfigurera integrering New Relic i Azure AD måste du lägga **till New Relic (efter organisation) från** galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory** tjänst.
1. Välj **Företagsprogram**  >  **Nytt program**.
1. På sidan **Bläddra i Azure AD-galleriet** skriver **New Relic (efter organisation)** i sökrutan.
1. Välj **New Relic (efter organisation)** i resultatet och välj sedan **Skapa**. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurera och testa enkel inloggning med Azure AD för New Relic

Konfigurera och testa enkel inloggning i Azure AD New Relic med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i New Relic.

Så här konfigurerar och testar du enkel inloggning med Azure AD New Relic:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
   1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD med B.Simon.
   1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att B.Simon kan använda enkel inloggning med Azure AD.
1. [Konfigurera New Relic enkel inloggning](#configure-new-relic-sso) för att konfigurera inställningarna för enkel inloggning på New Relic sidan.
   1. [Skapa en New Relic testanvändare för](#create-a-new-relic-test-user) att ha en motsvarighet för B.Simon i New Relic som är länkad till Azure AD-användaren.
1. [Testa enkel inloggning](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på sidan **New Relic programintegrering** per organisation hittar du **avsnittet** Hantera. Välj sedan **Enkel inloggning.**

1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.

1. På sidan **Konfigurera enkel Sign-On med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel Sign-On med SAML, med pennikonen markerad.](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** fyller du i värden för **Identifierare och** **Svars-URL.**

   * Hämta dessa värden med hjälp av New Relic **Min** organisation. Så här använder du det här programmet:
      1. [Logga in](https://login.newrelic.com/) på New Relic.
      1. Välj Appar på den översta **menyn.**
      1. I avsnittet **Your apps (Dina** appar) väljer du My Organization Authentication domains **(Min organisationsautentiseringsdomäner).**  >  
      1. Välj den autentiseringsdomän som du vill att enkel inloggning med Azure AD ska ansluta till (om du har fler än en autentiseringsdomän). De flesta företag har bara en autentiseringsdomän som heter **Standard.** Om det bara finns en autentiseringsdomän behöver du inte välja något.
      1. I avsnittet **Autentisering innehåller** URL för **försäkran konsument** det värde som ska användas för **svars-URL.**
      1. I avsnittet **Autentisering** innehåller vår **entitets-ID** det värde som ska användas för **identifierare**.

1. I avsnittet **Användarattribut &** anspråk kontrollerar du att Unik användaridentifierare mappas till ett fält som innehåller den e-postadress som används New Relic. 

   * Standardfältet **user.userprincipalname** fungerar åt dig om dess värden är samma som de New Relic e-postadresserna.
   * Fältet  **user.mail kan** fungera bättre för dig om **user.userprincipalname** inte är den New Relic e-postadressen.

1. I avsnittet **SAML-signeringscertifikat** kopierar du **URL:en för appfederationsmetadata** och sparar värdet för senare användning.

1. I avsnittet **Konfigurera New Relic efter organisation kopierar** du **inloggnings-URL** och sparar värdet för senare användning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure Portal namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till New Relic.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **New Relic**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-new-relic-sso"></a>Konfigurera New Relic enkel inloggning

Följ de här stegen för att konfigurera enkel inloggning New Relic.

1. [Logga in](https://login.newrelic.com/) på New Relic.

1. Välj Appar på den översta **menyn.**

1. I avsnittet **Your apps (Dina** appar) väljer du My Organization Authentication domains **(Min organisationsautentiseringsdomäner).**  >  

1. Välj den autentiseringsdomän som du vill att enkel inloggning med Azure AD ska ansluta till (om du har fler än en autentiseringsdomän). De flesta företag har bara en autentiseringsdomän som heter **Standard.** Om det bara finns en autentiseringsdomän behöver du inte välja något.

1. I avsnittet **Autentisering** väljer du **Konfigurera**.

   1. För **Källa för SAML-metadata** anger du det värde som du tidigare sparade från fältet Url för azure **AD-appfederationens metadata.**

   1. För **SSO-mål-URL** anger du det värde som du sparade tidigare från fältet **Inloggnings-URL för** Azure AD.

   1. När du har verifierat att inställningarna ser bra ut på båda sidorna för Azure AD New Relic väljer du **Spara**. Om båda sidorna inte är korrekt konfigurerade kan användarna inte logga in på New Relic.

### <a name="create-a-new-relic-test-user"></a>Skapa en New Relic-testanvändare

I det här avsnittet skapar du en användare med namnet B.Simon i New Relic.

1. [Logga in](https://login.newrelic.com/) på New Relic.

1. Välj Appar på den översta **menyn.**

1. I avsnittet **Dina appar** väljer du **Användarhantering.**

1. Välj **Lägg till användare**.

   1. För **Namn** anger du **B.Simon**.
   
   1. För **E-post** anger du det värde som ska skickas av Enkel inloggning med Azure AD.
   
   1. Välj en **användartyp** och en **användargrupp** för användaren. För en testanvändare är **Grundläggande användare** för Typ **och Användare** för grupp rimliga val.
   
   1. Spara användaren genom att välja Lägg **till användare.**

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till New Relic inloggnings-URL där du kan initiera inloggningsflödet.  

* Gå till New Relic inloggnings-URL direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** Azure Portal så bör du automatiskt loggas in på New Relic som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på New Relic-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till sidan för programinloggning för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på New Relic som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du New Relic kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
