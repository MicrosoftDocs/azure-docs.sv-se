---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Cerby | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cerby.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 53813bd692f1e621c89e234748f2f9a2d343684b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520958"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cerby"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Cerby

I den här självstudien lär du dig att integrera Cerby med Azure Active Directory (Azure AD). När du integrerar Cerby med Azure AD kan du:

* I Azure AD kan du styra vem som har åtkomst till Cerby.
* Gör så att dina användare automatiskt loggas in på Cerby med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Cerby-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cerby har stöd för **SP-initierad** enkel inloggning.

* Cerby stöder **just-in-time-användareablering.**

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="adding-cerby-from-the-gallery"></a>Lägga till Cerby från galleriet

För att konfigurera integreringen av Cerby i Azure AD måste du lägga till Cerby från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Cerby** i sökrutan.
1. Välj **Cerby** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-cerby"></a>Konfigurera och testa enkel inloggning med Azure AD för Cerby

Konfigurera och testa enkel inloggning i Azure AD med Cerby med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cerby.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Cerby:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Cerby](#configure-cerby-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Cerby-testanvändare](#create-cerby-test-user)** – för att ha en motsvarighet för B.Simon i Cerby som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal går du till **sidan för Cerby-programintegrering,** hittar avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:amazon:cognito:sp:<ID>`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<CustomerName>-cerbyauth.auth.us-east-2.amazoncognito.com/saml2/idpresponse`

    c. I **textrutan Inloggnings-URL** skriver du en URL med något av följande mönster:

    | Inloggnings-URL |
    |--------|
    | `https://app.cerby.com` |
    | `https://<CustomerName>.cerby.com` |
    |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk identifierare, svars-URL och inloggnings-URL. Kontakta [cerby-klientens supportteam](mailto:help@cerby.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera Cerby** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Cerby.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Cerby**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-cerby-sso"></a>Konfigurera enkel inloggning för Cerby

För att konfigurera enkel inloggning på **Cerby-sidan** behöver du skicka det nedladdade certifikatet **(Base64)** och lämpliga kopierade URL:er från Azure Portal [till Cerby-supportteamet](mailto:help@cerby.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cerby-test-user"></a>Skapa Cerby-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Cerby. Cerby stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Cerby skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Cerby-inloggnings-URL:en där du kan initiera inloggningsflödet. 

* Gå till Cerby-inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Cerby-panelen i Mina appar omdirigeras den till Cerby-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cerby kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


