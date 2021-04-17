---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med desknets NEO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och desknets NEO.
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
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580797"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Desknets NEO

I den här självstudien lär du dig att integrera Desknets NEO med Azure Active Directory (Azure AD). När du integrerar Desknets NEO med Azure AD kan du:

* I Azure AD kan du styra vem som har åtkomst till desknets NEO.
* Gör så att dina användare automatiskt loggas in på desknets NEO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* desknets NEO-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* desknets NEO har stöd för **SP-initierad** enkel inloggning.

## <a name="adding-desknets-neo-from-the-gallery"></a>Lägga till Desknets NEO från galleriet

För att konfigurera integreringen av Desknets NEO i Azure AD behöver du lägga till desknets NEO från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **desknet's NEO** i sökrutan.
1. Välj **desknets NEO från** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Konfigurera och testa Azure AD SSO för desknets NEO

Konfigurera och testa enkel inloggning för Azure AD med desknets NEO med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i desknets NEO.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med desknets NEO:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera desknets NEO SSO](#configure-desknets-neo-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa desknets NEO-testanvändare](#create-desknets-neo-test-user)** – för att ha en motsvarighet för B.Simon i desknets NEO som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på **desknets SIDA för** NEO-programintegrering, hittar du **avsnittet Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk identifierare, svars-URL och inloggnings-URL. Kontakta [supportteamet för DESKnets NEO-klient för](mailto:cloudsupport@desknets.com) att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera desknets NEO** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till desknets NEO.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **desknets NEO**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-desknets-neo-sso"></a>Konfigurera desknets NEO SSO

1. Logga in på desknets NEO-företagswebbplats som administratör.

1. I menyn klickar du på **inställningsikonen för SAML-autentiseringslänk.**

    ![Skärmbild av inställningar för SAML-autentiseringslänk.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. I Vanliga **inställningar klickar du** på Använd **från** SAML-autentisering samarbete.

    ![Skärmbild för användning av SAML-autentisering.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Utför stegen nedan i avsnittet inställningar **för SAML-autentiseringslänk.**

    ![Skärmbild av avsnittet inställningar för SAML-autentiseringslänk.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. I **textrutan Åtkomst-URL** klistrar du **in värdet** för den inloggnings-URL som du har kopierat från Azure Portal.

    b. I **textrutan SP Entity ID (SP-entitets-ID)** klistrar du in värdet för Identifierare som du har kopierat från Azure Portal. 

    c. Klicka **på Välj** fil för att ladda upp den nedladdade **certifikatfilen (Base64)** från Azure Portal till textrutan **x.509 Certificate (x.509-certifikat).**

    d. Klicka **på Ändra**.

### <a name="create-desknets-neo-test-user"></a>Skapa desknets NEO-testanvändare

1. Logga in på desknets NEO-företagswebbplats som administratör.

1. I menyn **klickar** du på **ikonen Administratörsinställningar.**

    ![Skärmbild av administratörsinställningar.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Klicka **på inställningsikonen** och välj **Användarhantering** i Anpassade **inställningar.**

    ![Skärmbild av användarhanteringsinställningar.](./media/desknets-neo-tutorial/user-management.png)

1. Klicka **på Skapa användarinformation.**

    ![Skärmbild av knappen Användarinformation.](./media/desknets-neo-tutorial/create-new-user.png)

1. Fyll i de obligatoriska fälten på sidan nedan och klicka på **Skapa**.

    ![Skärmbild av avsnittet Skapa användare.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till desknets NEO-inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till desknets NEO-inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på desknets NEO-panel i Mina appar omdirigeras detta till desknets NEO-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat desknets NEO kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


