---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Check Point Identity Awareness | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Check Point Identity Awareness.
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
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520947"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Check Point Identity Awareness

I den här självstudien lär du dig att integrera Check Point Identity Awareness med Azure Active Directory (Azure AD). När du integrerar Check Point Identity Awareness med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Check Point Identity Awareness.
* Gör så att dina användare automatiskt loggas in på Check Point Identity Awareness med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Check Point-prenumeration med enkel inloggning (SSO) för Identity Awareness.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Check Point Identity Awareness har stöd för **SP-initierad** enkel inloggning.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Lägga Check Point Identity Awareness från galleriet

För att konfigurera integreringen av Check Point Identity Awareness i Azure AD måste du lägga till Check Point Identity Awareness från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du Check Point Identity **Awareness** i sökrutan.
1. Välj **Check Point Identity Awareness** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Konfigurera och testa enkel inloggning med Azure AD för Check Point Identity Awareness

Konfigurera och testa enkel inloggning med Azure AD Check Point Identity Awareness med hjälp av en testanvändare med namnet **B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Check Point Identity Awareness.

Utför följande steg för att konfigurera och testa enkel inloggning Check Point Azure AD Check Point Identity Awareness:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Check Point Identity Awareness SSO](#configure-check-point-identity-awareness-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Check Point Identity Awareness-testanvändare](#create-check-point-identity-awareness-test-user)** – för att ha en motsvarighet för B.Simon i Check Point Identity Awareness som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal du på **sidan Check Point Identity Awareness-programintegrering,** hittar du avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk identifierare, svars-URL och inloggnings-URL. Kontakta [Check Point Identity Awareness Client och be](mailto:support@checkpoint.com) om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera Check Point Identity Awareness** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Check Point Identity Awareness.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du Check Point **Identity Awareness**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-check-point-identity-awareness-sso"></a>Konfigurera Check Point Identity Awareness SSO

1. Logga in på Check Point Identity Awareness-företagswebbplatsen som administratör.

1. I vyn SmartConsole > **Gateways & Servers** (Nya > Mer > för **användare/> identitetsprovider).**

    ![skärmbild för ny identitetsprovider.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Utför följande steg i fönstret **Ny identitetsprovider.**

    ![skärmbild för avsnittet Identitetsprovider.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. I fältet **Gateway** väljer du Den säkerhetsgateway som måste utföra SAML-autentiseringen.

    b. I fältet **Tjänst** väljer du Identity Awareness (Identitetsmedvetenhet) i listrutan. 

    c. Kopiera **värdet Identifier(Entity ID) (Identifierare(Entitets-ID)** och klistra in det i **textrutan** Identifierare i avsnittet Grundläggande **SAML-konfiguration** i Azure Portal.

    d. Kopiera **värdet för Svars-URL** och klistra in det här värdet i textrutan Svars-URL i avsnittet Grundläggande **SAML-konfiguration** i Azure Portal. 

    e. Välj **Importera metadatafilen för** att ladda upp det **nedladdade certifikatet (Base64)** från Azure Portal.

    > [!NOTE]
    > Du kan också **välja Infoga** manuellt om du vill klistra in värdena för Entitets-ID och Inloggnings-URL manuellt i motsvarande fält och ladda upp certifikatfilen från Azure Portal.   

    f. Klicka på **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Skapa Check Point Identity Awareness-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Check Point Identity Awareness. Ta hjälp [Check Point Identity Awareness-supportteamet](mailto:support@checkpoint.com) för att lägga till användare Check Point Identity Awareness-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Check Point identity awareness inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Check Point inloggnings-URL för Identity Awareness direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar Check Point Identity Awareness-panelen i Mina appar omdirigeras detta till Check Point inloggnings-URL för Identity Awareness. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har Check Point Identity Awareness kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


