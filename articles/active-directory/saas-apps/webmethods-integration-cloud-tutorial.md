---
title: 'Självstudie: Azure Active Directory integrering med webMethods Integration Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och webMethods Integration Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: c3eaac19ffbf3fd93311073ff69e28532b1c15e6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520062"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Självstudie: Azure Active Directory integrering med webMethods Integration Suite

I den här självstudien lär du dig att integrera webMethods Integration Suite med Azure Active Directory (Azure AD). När du integrerar webMethods Integration Suite med Azure AD kan du:

* I Azure AD kan du styra vem som har åtkomst till webMethods Integration Suite.
* Gör så att dina användare automatiskt loggas in på webMethods Integration Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* webMethods Integration Suite-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* webMethods Integration Suite **SP-** och **IDP-initierad** enkel inloggning.

* webMethods Integration Suite stöder **just-in-time-användareablering.**

## <a name="add-webmethods-integration-suite-from-the-gallery"></a>Lägga till webMethods Integration Suite från galleriet

För att konfigurera integreringen av webMethods Integration Suite i Azure AD måste du lägga till webMethods Integration Suite från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **webMethods Integration Suite** i sökrutan.
1. Välj **webMethods Integration Suite** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-webmethods-integration-suite"></a>Konfigurera och testa enkel inloggning i Azure AD för webMethods Integration Suite

Konfigurera och testa enkel inloggning i Azure AD med webMethods Integration Suite en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i webMethods Integration Suite.

Utför följande steg för att konfigurera Integration Suite testa enkel inloggning med Azure AD Integration Suite webMethods:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera webMethods Integration Suite enkel](#configure-webmethods-integration-suite-sso)** inloggning – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa webMethods Integration Suite-testanvändare](#create-webmethods-integration-suite-test-user)** – för att ha en motsvarighet för B.Simon i webMethods Integration Suite som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal du avsnittet Hantera **på sidan webMethods Integration Suite** för  programintegrering och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill **konfigurera webMethods Integration Cloud** i avsnittet Grundläggande **SAML-konfiguration** utför du följande steg om du vill konfigurera programmet **i IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL med något av följande mönster:

    | Identifierar-URL |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |
    |

    b. I **textrutan Svars-URL** skriver du en URL med något av följande mönster:

    | Svars-URL |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`|
    |

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    d. I **textrutan Inloggnings-URL** skriver du en URL med något av följande mönster:

    | Inloggnings-URL |
    |--------------------------------|
    |`https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`|
    |`https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`|
    |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [webMethods Integration Suite kundsupporten för att](https://empower.softwareag.com/) hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Om du vill **konfigurera webMethods API Cloud** i avsnittet Grundläggande **SAML-konfiguration** utför du följande steg om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL med något av följande mönster:

    | Identifierar-URL |
    |----------------------------------------------|
    | `<SUBDOMAIN>.webmethodscloud.com`|
    |`<SUBDOMAIN>.webmethodscloud.eu`|
    | `<SUBDOMAIN>.webmethodscloud.de`|
    |

    b. I **textrutan Svars-URL** skriver du en URL med något av följande mönster:

    | Svars-URL |
    |----------------------------------------------|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`|
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`|
    |

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    d. I **textrutan Inloggnings-URL** skriver du en URL med något av följande mönster:
    
    | Inloggnings-URL |
    |--------------------------------|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`|
    |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [webMethods Integration Suite kundsupporten för att](https://empower.softwareag.com/) hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I **avsnittet Konfigurera webMethods Integration Suite** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till webMethods Integration Suite.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **webMethods Integration Suite**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-webmethods-integration-suite-sso"></a>Konfigurera webMethods Integration Suite SSO

För att konfigurera enkel inloggning på **webMethods Integration Suite-sidan** behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure Portal till [webMethods Integration Suite-supportteamet](https://empower.softwareag.com/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-webmethods-integration-suite-test-user"></a>Skapa webMethods Integration Suite testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i webMethods Integration Suite. webMethods Integration Suite stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i webMethods Integration Suite skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till webMethods Integration Suite inloggnings-URL där du kan initiera inloggningsflödet.  

* Gå till webMethods Integration Suite inloggnings-URL direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det** här Azure Portal så bör du automatiskt loggas in på webMethods-Integration Suite som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på webMethods Integration Suite-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till sidan för programinloggning för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på webMethods Integration Suite som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat webMethods Integration Suite du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
