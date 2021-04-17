---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Terraform Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Terraform Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.custom: devx-track-terraform
ms.openlocfilehash: 69ce53126a45e74f43514779f391c2df66d8f225
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-terraform-enterprise"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Terraform Enterprise

I den här självstudien lär du dig att integrera Terraform Enterprise med Azure Active Directory (Azure AD). När du integrerar Terraform Enterprise med Azure AD kan du:

* Kontrollera vem som har åtkomst till Terraform Enterprise från Azure AD.
* Gör så att dina användare automatiskt loggas in på Terraform Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Terraform Enterprise-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Terraform Enterprise stöder **SP-initierad** enkel inloggning.
* Terraform Enterprise stöder **just-in-time-användareablering.**

## <a name="add-terraform-enterprise-from-the-gallery"></a>Lägga till Terraform Enterprise från galleriet

För att konfigurera integreringen av Terraform Enterprise i Azure AD behöver du lägga till Terraform Enterprise från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Terraform Enterprise** i sökrutan.
1. Välj **Terraform Enterprise** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-terraform-enterprise"></a>Konfigurera och testa enkel inloggning för Azure AD för Terraform Enterprise

Konfigurera och testa enkel inloggning i Azure AD med Terraform Enterprise med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Terraform Enterprise.

Slutför följande byggstenar för att konfigurera och testa enkel inloggning i Azure AD med Terraform Enterprise:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Terraform Enterprise](#configure-terraform-enterprise-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    * **[Skapa Terraform Enterprise-testanvändare](#create-terraform-enterprise-test-user)** – för att ha en motsvarighet för B.Simon i Terraform Enterprise som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal på sidan **för Terraform** Enterprise-programintegrering går du **till avsnittet Hantera** och väljer enkel **inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<TFE HOSTNAME>/session`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<TFE HOSTNAME>/users/saml/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Terraform Enterprise-klientens supportteam](https://support.hashicorp.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera Terraform Enterprise** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Terraform Enterprise.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Terraform Enterprise**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-terraform-enterprise-sso"></a>Konfigurera enkel inloggning för Terraform Enterprise

För att konfigurera enkel inloggning på **Terraform Enterprise-sidan** behöver du skicka det nedladdade certifikatet **(Base64)** och lämpliga kopierade URL:er från Azure Portal [till Terraform Enterprise-supportteamet.](https://support.hashicorp.com) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-terraform-enterprise-test-user"></a>Skapa Terraform Enterprise-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Terraform Enterprise. Terraform Enterprise stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Terraform Enterprise skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Terraform Enterprise-inloggnings-URL:en där du kan initiera inloggningsflödet. 

* Gå till Terraform Enterprise Inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Terraform Enterprise-panelen i Mina appar omdirigeras den till Terraform Enterprise Inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Terraform Enterprise kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).