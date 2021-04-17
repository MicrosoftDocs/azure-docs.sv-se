---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Cisco Intersight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Intersight.
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
ms.openlocfilehash: 6efec61ec5866547ab6b432cb9c566642f3191ff
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580791"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-intersight"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med Cisco Intersight

I den här självstudien lär du dig att integrera Cisco Intersight med Azure Active Directory (Azure AD). När du integrerar Cisco Intersight med Azure AD kan du:

* Kontrollera vem som har åtkomst till Cisco Intersight i Azure AD.
* Gör så att dina användare automatiskt loggas in på Cisco Intersight med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Cisco Intersight-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cisco Intersight har stöd för **SP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="adding-cisco-intersight-from-the-gallery"></a>Lägga till Cisco Intersight från galleriet

För att konfigurera integreringen av Cisco Intersight i Azure AD behöver du lägga till Cisco Intersight från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du Cisco **Intersight** i sökrutan.
1. Välj **Cisco Intersight** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-cisco-intersight"></a>Konfigurera och testa Azure AD SSO för Cisco Intersight

Konfigurera och testa enkel inloggning i Azure AD med Cisco Intersight med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cisco Intersight.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Cisco Intersight:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Cisco Intersight](#configure-cisco-intersight-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Cisco Intersight-testanvändare](#create-cisco-intersight-test-user)** – för att ha en motsvarighet för B.Simon i Cisco Intersight som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal du avsnittet Hantera på sidan för Cisco  **Intersight-programintegrering** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://intersight.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `www.intersight.com`

1. Cisco Intersight-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar ett exempel på detta. Standardvärdet för **Unik** användaridentifierare är **user.userprincipalname,** men Cisco Intersight förväntar sig att detta mappas med användarens e-postadress. För det kan du använda **user.mail-attributet** från listan eller använda lämpligt attributvärde baserat på din organisationskonfiguration..

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Cisco Intersight-programmet att några fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina behov.
    
    | Name |  Källattribut|
    | -------------- | --------- |
    | Förnamn | user.givenname |
    | Efternamn | user.surname |
    | memberOf | user.groups |

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet  **SAML-signeringscertifikat,** hittar **XML** för **federationsmetadata** och väljer Ladda ned för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I **avsnittet Konfigurera Cisco Intersight** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Cisco Intersight.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Cisco Intersight**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst" vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-cisco-intersight-sso"></a>Konfigurera enkel inloggning för Cisco Intersight

För att konfigurera enkel inloggning på **Cisco Intersight-sidan** behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure Portal till [Cisco Intersight-supportteamet](mailto:intersight-feedback@cisco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cisco-intersight-test-user"></a>Skapa Cisco Intersight-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Cisco Intersight. Arbeta med [Cisco Intersight-supportteamet](mailto:intersight-feedback@cisco.com) för att lägga till användare i Cisco Intersight-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Cisco Intersights inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Cisco Intersights inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Cisco Intersight-panelen i Mina appar omdirigeras den till Inloggnings-URL för Cisco Intersight. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cisco Intersight kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


