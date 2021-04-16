---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med MongoDB Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MongoDB Cloud.
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
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520096"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med MongoDB Cloud

I den här självstudien lär du dig att integrera MongoDB Cloud med Azure Active Directory (Azure AD). När du integrerar MongoDB Cloud med Azure AD kan du:

* Kontrollera vem som har åtkomst till MongoDB Cloud, MongoDB Atlas, MongoDB-communityn, MongoDB University och MongoDB-support i Azure AD.
* Gör så att dina användare automatiskt loggas in på MongoDB Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* MongoDB Cloud-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MongoDB Cloud stöder **SP- och** **IDP-initierad** enkel inloggning.
* MongoDB Cloud stöder **just-in-time-användareablering.**

## <a name="add-mongodb-cloud-from-the-gallery"></a>Lägga till MongoDB Cloud från galleriet

För att konfigurera integreringen av MongoDB Cloud i Azure AD måste du lägga till MongoDB Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **MongoDB Cloud** i sökrutan.
1. Välj **MongoDB Cloud** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Konfigurera och testa enkel inloggning för Azure AD för MongoDB Cloud

Konfigurera och testa enkel inloggning i Azure AD med MongoDB Cloud med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i MongoDB Cloud.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med MongoDB Cloud:

1. [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att B.Simon kan använda enkel inloggning med Azure AD.
1. [Konfigurera enkel inloggning för MongoDB Cloud](#configure-mongodb-cloud-sso) för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. [Skapa en MongoDB Cloud-testanvändare](#create-a-mongodb-cloud-test-user) för att ha en motsvarighet för B.Simon i MongoDB Cloud som är länkad till en Azure AD-representation av användaren.
1. [Testa enkel inloggning](#test-sso) för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/), på sidan **för MongoDB** Cloud-programintegrering, hittar **du avsnittet** Hantera. Välj **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel Sign-On med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Skärmbild av Konfigurera enkel Sign-On med SAML-sida, med pennikonen markerad](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I **textrutan** Identifierare skriver du en URL som använder följande mönster: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. I **textrutan Svars-URL** skriver du en URL som använder följande mönster: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Välj **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP-initierat** läge:

    I **textrutan Inloggnings-URL** anger du en URL som använder följande mönster:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk identifierare, svars-URL och inloggnings-URL. Om du vill hämta dessa värden kontaktar du [supportteamet för MongoDB Cloud.](https://support.mongodb.com/) Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. MongoDB Cloud-programmet förväntar sig att SAML-försäkran har ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![Skärmbild av standardattribut](common/default-attributes.png)

1. Förutom de föregående attributen förväntar sig MongoDB Cloud-programmet att några fler attribut skickas tillbaka i SAML-svaret. Dessa attribut är också förifyllda, men du kan granska dem enligt dina behov.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | e-post | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat** och hittar **XML för federationsmetadata.** Välj **Ladda** ned för att ladda ned certifikatet och spara det på datorn.

    ![Skärmbild av avsnittet SAML-signeringscertifikat med nedladdningslänken markerad](common/metadataxml.png)

1. I avsnittet **Konfigurera MongoDB Cloud** kopierar du lämpliga URL:er baserat på dina behov.

    ![Skärmbild av avsnittet Konfigurera Mongo DB Cloud med URL:er markerade](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **Användare**  >  **Alla användare**.
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera **kryssrutan Visa** lösenord och skriv sedan ned lösenordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till MongoDB Cloud.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **MongoDB Cloud**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst" vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurera enkel inloggning för MongoDB Cloud

För att konfigurera enkel inloggning på MongoDB Cloud-sidan behöver du lämpliga URL:er kopierade från Azure Portal. Du måste också konfigurera federationsprogrammet för mongoDB-molnorganisationen. Följ anvisningarna i [MongoDB Cloud-dokumentationen.](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/) Om du har problem kontaktar du [Supportteamet för MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Skapa en Testanvändare för MongoDB Cloud

MongoDB Cloud stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns ingen ytterligare åtgärd att vidta. Om det inte redan finns någon användare i MongoDB Cloud skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

#### <a name="sp-initiated"></a>SP-initierad:

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till mongoDB-molninloggnings-URL:en där du kan initiera inloggningsflödet.  

* Gå till Inloggnings-URL för MongoDB Cloud direkt och initiera inloggningsflödet därifrån.

#### <a name="idp-initiated"></a>IDP-initierad:

* Klicka på **Testa det här** programmet Azure Portal så bör du automatiskt loggas in på MongoDB Cloud som du har ställt in enkel inloggning för. 

Du kan också använda Microsoft Mina appar för att testa programmet i val annat läge. När du klickar på MongoDB Cloud-panelen i Mina appar omdirigeras du, om den konfigureras i SP-läge, till programinloggningssidan för att initiera inloggningsflödet. Om den konfigureras i IDP-läge bör du automatiskt loggas in på MongoDB Cloud som du har konfigurerat enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat MongoDB Cloud kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
