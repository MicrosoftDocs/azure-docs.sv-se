---
title: 'Självstudie: Azure Active Directory integrering med OutSystems Azure AD | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OutSystems Azure AD.
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
ms.openlocfilehash: 6a4a2f4edb3fcc56bafb3847280958a7803ba3bd
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519909"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Självstudie: Integrera OutSystems Azure AD med Azure Active Directory

I den här självstudien lär du dig att integrera OutSystems Azure AD med Azure Active Directory (Azure AD). När du integrerar OutSystems Azure AD med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till OutSystems Azure AD.
* Gör så att dina användare automatiskt loggas in på OutSystems Azure AD med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* OutSystems Azure AD-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OutSystems Azure AD stöder **SP- och IDP-initierad** enkel inloggning och stöder **just-in-time-användareablering.**

## <a name="add-outsystems-azure-ad-from-the-gallery"></a>Lägga till OutSystems Azure AD från galleriet

För att konfigurera integreringen av OutSystems Azure AD i Azure AD måste du lägga till OutSystems Azure AD från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **OutSystems Azure AD** i sökrutan.
1. Välj **OutSystems Azure AD i** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-outsystems-azure-ad"></a>Konfigurera och testa Enkel inloggning för Azure AD för OutSystems Azure AD

Konfigurera och testa enkel inloggning för Azure AD med OutSystems Azure AD med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i OutSystems Azure AD.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med OutSystems Azure AD:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för OutSystems Azure AD](#configure-outsystems-azure-ad-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa OutSystems Azure AD-testanvändare](#create-outsystems-azure-ad-test-user)** – för att ha en motsvarighet för B.Simon i OutSystems Azure AD som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal går du till **sidan för OutSystems Azure AD-programintegrering,** hittar avsnittet Hantera och **väljer Enkel inloggning.** 
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel Sign-On med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera programmet **i IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL med följande mönster: `http://<YOURBASEURL>/IdP`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<YOURBASEURL>/IdP/SSO.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I **textrutan Inloggnings-URL** skriver du en URL med följande mönster:  `https://<YOURBASEURL>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [supportteamet för OutSystems-klienten](mailto:support@outsystems.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning Sign-On SAML** går du till avsnittet **SAML-signeringscertifikat,** hittar XML för **federationsmetadata** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I **avsnittet Konfigurera OutSystems Azure AD** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till OutSystems Azure AD.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **OutSystems Azure AD**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-outsystems-azure-ad-sso"></a>Konfigurera OutSystems Azure AD SSO

För att konfigurera enkel inloggning på OutSystems-sidan måste du ladda ned [IdP-förfalskarkomponenten,](https://www.outsystems.com/forge/component-overview/599/idp) konfigurera den enligt [anvisningarna](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Configure_your_application_to_use_IdP_connector). När du har installerat komponenten och gjort nödvändiga kodändringar konfigurerar du Azure AD genom att ladda ned XML-federationsmetadata från Azure Portal och ladda upp den på OutSystems IdP-komponenten, enligt följande [anvisningar.](https://success.outsystems.com/Documentation/Development_FAQs/How_to_configure_OutSystems_to_use_identity_providers_using_SAML#Azure_AD_.2F_ADFS)

### <a name="create-outsystems-azure-ad-test-user"></a>Skapa OutSystems Azure AD-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i OutSystems. OutSystems stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i OutSystems skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till OutSystems Azure AD-inloggnings-URL där du kan initiera inloggningsflödet.  

* Gå till OutSystems Azure AD Inloggnings-URL direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** Azure Portal så bör du automatiskt loggas in på OutSystems Azure AD som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på OutSystems Azure AD-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på OutSystems Azure AD som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat OutSystems Azure AD kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
