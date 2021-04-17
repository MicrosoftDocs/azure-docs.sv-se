---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med CylancePROTECT | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splunk Enterprise och CylancePROTECT.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: a5daf83346db901288d5bc614f341c354d0c008f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481352"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med CylancePROTECT

I den här självstudien lär du dig att integrera CylancePROTECT med Azure Active Directory (Azure AD). När du integrerar CylancePROTECT med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till CylancePROTECT.
* Gör så att dina användare automatiskt loggas in på CylancePROTECT med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* CylancePROTECT-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* CylancePROTECT stöder **IDP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-cylanceprotect-from-the-gallery"></a>Lägga till CylancePROTECT från galleriet

För att konfigurera integreringen av CylancePROTECT till Azure AD behöver du lägga till CylancePROTECT från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **CylancePROTECT** i sökrutan.
1. Välj **CylancePROTECT** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>Konfigurera och testa Azure AD SSO för CylancePROTECT

Konfigurera och testa Azure AD SSO med CylancePROTECT med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i CylancePROTECT.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med CylancePROTECT:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för CylancePROTECT](#configure-cylanceprotect-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa CylancePROTECT-testanvändare](#create-cylanceprotect-test-user)** – för att ha en motsvarighet för B.Simon i CylancePROTECT som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På sidan Azure Portal **CylancePROTECT-programintegrering** går du till avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I **textrutan** Identifierare skriver du en av följande URL:er:
    
    | Region | URL-värde |
    |----------|---------|
    | Asien och stillahavsområdet, nordöstra (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien och stillahavsområdet, sydöstra (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa, centrala (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. I **textrutan Svars-URL** skriver du någon av följande URL:er:
    
    | Region | URL-värde |
    |----------|---------|
    | Asien och stillahavsområdet, nordöstra (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asien och stillahavsområdet, sydöstra (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa, centrala (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Nordamerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sydamerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. CylancePROTECT-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. CylancePROTECT-programmet förväntar sig att **nameidentifier** mappas med **user.mail** och tar bort alla andra  återstående anspråk, så du måste redigera attributmappningen genom att klicka på redigera-ikonen och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar Certifikat **(Base64)** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn. 

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I **avsnittet Konfigurera CylancePROTECT** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

> [!NOTE]
> Öppna det nedladdade Base64-kodade certifikatet i en textredigerare och kopiera ENDAST texten mellan **START-** och **END-taggarna** för att klistra in den i Cylance Admin-portalen.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till CylancePROTECT.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **CylancePROTECT**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst" vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-cylanceprotect-sso"></a>Konfigurera enkel inloggning med CylancePROTECT

För att konfigurera enkel inloggning på **CylancePROTECT-sidan** behöver du skicka det nedladdade certifikatet **(Base64)** och lämpliga kopierade URL:er från Azure Portal till [CylancePROTECT-supportteamet.](https://www.cylance.com/en-us/resources/support/support-overview.html) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna. Mer information finns i Cylance-dokumentationen: [https://support.cylance.com/s/](https://support.cylance.com/s/) .

### <a name="create-cylanceprotect-test-user"></a>Skapa CylancePROTECT-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i CylancePROTECT. Arbeta med konsoladministratören för att lägga till användarna i CylancePROTECT-plattformen. Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ.

* Klicka på Testa det här programmet Azure Portal så bör du automatiskt loggas in på CylancePROTECT som du har ställt in enkel inloggning för.

* Du kan använda Microsoft Mina appar. När du klickar på CylancePROTECT-panelen i Mina appar bör du automatiskt loggas in på CylancePROTECT som du har ställt in enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat CylancePROTECT kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
