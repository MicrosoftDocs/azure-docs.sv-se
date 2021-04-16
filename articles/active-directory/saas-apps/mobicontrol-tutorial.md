---
title: 'Självstudie: Azure Active Directory integrering med Mcontrol | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mcontrol.
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
ms.openlocfilehash: 87cbbd0caf735afaaadfa47bebf13feeaefcc832
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520081"
---
# <a name="tutorial-azure-active-directory-integration-with-mobicontrol"></a>Självstudie: Azure Active Directory integrering med McontrolControl

I den här självstudien lär du dig att integrera Mcontrol med Azure Active Directory (Ad Azure). När du integrerar Mcontrol med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till Mcontrol.
* Gör så att dina användare automatiskt loggas in på Mcontrol med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* McontrolControl-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mcontrol har stöd för **SP-initierad** enkel inloggning.

## <a name="add-mobicontrol-from-the-gallery"></a>Lägga till Mcontrol från galleriet

För att konfigurera integreringen av Mcontrol i Azure AD måste du lägga till Mcontrol från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Mcontrol** i sökrutan.
1. Välj **McontrolControl** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-mobicontrol"></a>Konfigurera och testa Enkel inloggning för Azure AD för Mcontrol

Konfigurera och testa enkel inloggning med Azure AD med Mcontrol med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Mcontrol.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Mcontrol:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Mcontrol](#configure-mobicontrol-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Mcontrol-testanvändare](#create-mobicontrol-test-user)** – för att ha en motsvarighet för B.Simon i Mcontrol som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för Mcontrol letar** du upp avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.corp.soti.net/mobicontrol`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.mobicontrolcloud.com/mobicontrol`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Mcontrol och](https://www.soti.net/about/contact-us/) be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Mcontrol.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **McontrolControl**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-mobicontrol-sso"></a>Konfigurera enkel inloggning för Mcontrol

För att konfigurera enkel inloggning på **Mcontrol-sidan** behöver du skicka **URL:en med appfederationsmetadata** till [supportteamet för Mcontrol.](https://www.soti.net/about/contact-us/) De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-mobicontrol-test-user"></a>Skapa Mcontrol-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Mcontrol. Ta hjälp [av Mcontrol-supportteamet](https://www.soti.net/about/contact-us/) för att lägga till användare i Mcontrol-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL:en för Mcontrol där du kan initiera inloggningsflödet. 

* Gå till inloggnings-URL:en för Mcontrol direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Mcontrol-panelen i Mina appar omdirigeras den till inloggnings-URL:en för Mcontrol. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Mcontrol kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).