---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med FCM HUB | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FCM HUB.
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
ms.openlocfilehash: 23177363cada5d8fde5a3fc93a14da2144b250c3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516764"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fcm-hub"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med FCM HUB

I den här självstudien lär du dig att integrera FCM HUB med Azure Active Directory (Azure AD). När du integrerar FCM HUB med Azure AD kan du:

* Kontrollera vem som har åtkomst till FCM HUB från Azure AD.
* Gör så att dina användare automatiskt loggas in på FCM HUB med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* FCM HUB-prenumeration med enkel inloggning (SSO).

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FCM HUB stöder **SP- och IDP-initierad** enkel inloggning.

## <a name="add-fcm-hub-from-the-gallery"></a>Lägga till FCM HUB från galleriet

För att konfigurera integreringen av FCM HUB i Azure AD måste du lägga till FCM HUB från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från galleriet** skriver du **FCM HUB** i sökrutan.
1. Välj **FCM HUB** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-fcm-hub"></a>Konfigurera och testa enkel inloggning för Azure AD för FCM HUB

Konfigurera och testa enkel inloggning i Azure AD med FCM HUB med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i FCM HUB.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med FCM HUB:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för FCM HUB](#configure-fcm-hub-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa FCM HUB-testanvändare](#create-fcm-hub-test-user)** – för att ha en motsvarighet för B.Simon i FCM HUB som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på sidan **för FCM** HUB-programintegrering går du **till avsnittet Hantera** och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I **textrutan Inloggnings-URL** skriver du en URL med följande mönster:  `https://hub.fcm.travel/SsoSp/SpInit?clientid=<CUSTOMID>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta kontohanteraren som är tilldelad till dig eller kontakta supportteamet för [FCM HUB-klienten](mailto:fcmssoadmin@us.fcm.travel) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Välj **Spara**.

1. Lägg till **följande anpassade** attribut i avsnittet **Användarattribut & Anspråk på** sidan Hantera anspråk:
   - **Namn:** PortalID
   - **Källa:** Attribut
   - **Källattribut:** PortalID, värde som tillhandahålls av FCM

1. I avsnittet **SAML-signeringscertifikat** använder du redigeringsalternativet för att välja eller ange följande inställningar och väljer sedan **Spara:**
   - **Signeringsalternativ:** Signera SAML-svar
   - **Signeringsalgoritm:** SHA-256

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera FCM HUB** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till FCM HUB.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **FCM HUB**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-fcm-hub-sso"></a>Konfigurera enkel inloggning för FCM HUB

För att konfigurera enkel inloggning på **FCM HUB-sidan** behöver du skicka det nedladdade certifikatet **(Base64)** och lämpliga kopierade URL:er från Azure Portal till din kontohanterare som är tilldelad till dig för supporten eller kontakta supportteamet för [FCM HUB-klienten.](mailto:fcmssoadmin@us.fcm.travel) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-fcm-hub-test-user"></a>Skapa FCM HUB-testanvändare

I det här avsnittet skapar du en användare med namnet B.Simon i FCM HUB. Kontakta din kontohanterare eller kontakta supportteamet för [FCM HUB-klienten för](mailto:fcmssoadmin@us.fcm.travel) att lägga till användare i FCM HUB-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL:en för FCM HUB där du kan initiera inloggningsflödet.  

* Gå till inloggnings-URL:en för FCM HUB direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** programmet Azure Portal så bör du automatiskt loggas in på FCM HUB som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på FCM HUB-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på FCM HUB som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat FCM HUB kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
