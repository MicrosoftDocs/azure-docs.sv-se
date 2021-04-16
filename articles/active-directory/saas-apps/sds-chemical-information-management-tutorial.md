---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SDS & Chemical Information Management | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SDS & Kemiinformationshantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509337"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med SDS & Kemiinformationshantering

I den här självstudien lär du dig att integrera SDS & Kemiinformationshantering med Azure Active Directory (Azure AD). När du integrerar SDS & Kemiinformationshantering med Azure AD kan du:

* I Azure AD kan du styra vem som har åtkomst till SDS & Kemiinformationshantering.
* Gör det möjligt för dina användare att automatiskt loggas in på SDS & Kemiinformationshantering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* SDS & En SSO-aktiverad prenumeration (Chemical Information Management).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SDS & Chemical Information Management stöder **SP-initierad** enkel inloggning.

* SDS & Kemisk informationshantering stöder **just-in-time-användareablering.**


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>Lägga till SDS & Kemiinformationshantering från galleriet

För att konfigurera integreringen av SDS & Kemisk informationshantering i Azure AD måste du lägga till SDS & Kemisk informationshantering från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från galleriet** skriver du **SDS & Kemiinformationshantering** i sökrutan.
1. Välj **SDS & Kemiinformationshantering** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>Konfigurera och testa enkel inloggning i Azure AD för SDS & Kemisk informationshantering

Konfigurera och testa enkel inloggning i Azure AD med SDS & Chemical Information Management med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SDS & Kemiinformationshantering.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD & SDS & kemiinformationshantering:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera SDS & För enkel](#configure-sds--chemical-information-management-sso)** inloggning med Kemisk informationshantering – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa SDS & Chemical Information Management-testanvändare](#create-sds--chemical-information-management-test-user)** – för att ha en motsvarighet till B.Simon i SDS & Chemical Information Management som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal du avsnittet Hantera på **sidan för programintegrering & SDS**  & Kemiinformationshantering och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I rutan **Identifierare** skriver du en URL med följande mönster: `https://cs.cloudsds.com/saml/<ID>`

    b. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk identifierare, svars-URL och Sign-On-URL. Kontakta [kundsupporten & SDS](mailto:info@cloudsds.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan Konfigurera enkel inloggning med SAML går du till avsnittet SAML-signeringscertifikat och klickar på kopieringsknappen för att kopiera **URL:en** för  **appfederationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till SDS & Chemical Information Management.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **SDS & Kemiinformationshantering.**
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-sds--chemical-information-management-sso"></a>Konfigurera enkel inloggning & SDS för kemisk informationshantering

För att konfigurera enkel inloggning på **SDS & Chemical Information Management-sidan** behöver du skicka URL:en för appfederationens **metadata** till [SDS & supportteamet för Kemiinformationshantering.](mailto:info@cloudsds.com) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-sds--chemical-information-management-test-user"></a>Skapa SDS& testanvändare för hantering av kemisk information

I det här avsnittet skapas en användare som heter Britta Simon i SDS & Chemical Information Management. SDS & Chemical Information Management stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i SDS & Kemisk informationshantering skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till SDS & inloggnings-URL för kemiinformationshantering där du kan initiera inloggningsflödet. 

* Gå till SDS & inloggnings-URL för hantering av kemisk information direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SDS & Kemiinformationshantering i Mina appar omdirigeras detta till inloggnings-URL:en för SDS & Kemiinformationshantering. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SDS & Kemisk informationshantering kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


