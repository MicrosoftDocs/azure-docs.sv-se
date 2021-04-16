---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Würu App | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wñru App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 8b4cb988329f599e098b0e392938ae050b55577d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520943"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wru-app"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Wñru App

I den här självstudien lär du dig att integrera W tutorial med Azure Active Directory (Azure AD). När du integrerar Würu App med Azure AD kan du:

* Kontrollera vem som har åtkomst till Wñru-appen i Azure AD.
* Gör så att dina användare automatiskt loggas in på Wru-appen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Würu App-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Würu App har stöd för **SP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="adding-wru-app-from-the-gallery"></a>Lägga till Wñru-appen från galleriet

För att konfigurera integreringen av Würu-appen i Azure AD måste du lägga till Wñru-appen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Wñru App** i sökrutan.
1. Välj **Wñru-app** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-wru-app"></a>Konfigurera och testa Azure AD SSO för Wñru-appen

Konfigurera och testa enkel inloggning i Azure AD med Wñru App med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Wru-appen.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Wñru App:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Wpool App](#configure-wuru-app-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Wü App-testanvändare](#create-wuru-app-test-user)** – för att ha en motsvarighet för B.Simon i Würu-appen som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för Würu-appen** hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://app.wuru.site/api/auth/azure`

    b. I **textrutan Identifierare (entitets-ID)** skriver du värdet: `urn:amazon:cognito:sp:us-east-2_142Y3PTBg`

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar **XML** för **federationsmetadata** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I **avsnittet Konfigurera Wñru-app** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Wru-appen.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Wñru App**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-wuru-app-sso"></a>Konfigurera enkel inloggning för W wmapp

För att konfigurera enkel inloggning på **Wñru App-sidan** behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure Portal till supportteamet för [W certifikatappen](mailto:contacto@wuru.site). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-wuru-app-test-user"></a>Skapa en testanvändare för W wmappen

I det här avsnittet skapar du en användare med namnet Britta Simon i Wñru App. Ta hjälp [av Wru App-supportteamet](mailto:contacto@wuru.site) för att lägga till användarna på Wru App-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL:en för Würu-appen där du kan initiera inloggningsflödet. 

* Gå till inloggnings-URL:en för Wru-appen direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Wru-apppanelen i Mina appar omdirigeras den till inloggnings-URL:en för Wru-appen. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Wru-appen kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


