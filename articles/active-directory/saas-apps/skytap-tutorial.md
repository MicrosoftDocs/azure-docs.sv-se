---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för Skytap | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och enkel inloggning för Skytap.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.openlocfilehash: 8d34ca8ed01144ee282f6411640894807a09ef08
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527884"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för Skytap

I den här självstudien får du lära dig hur du integrerar enkel inloggning för Skytap med Azure Active Directory (Azure AD). När du integrerar enkel inloggning för Skytap med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till enkel inloggning för Skytap.
* Gör det möjligt för användarna att logga in automatiskt till enkel inloggning för Skytap med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning för Skytap-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Enkel inloggning för Skytap stöder SP-och IDP-initierad SSO.
* När du har konfigurerat enkel inloggning för Skytap kan du framtvinga kontroll av sessionen. Detta skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Lägg till enkel inloggning för Skytap från galleriet

Om du vill konfigurera integrering av enkel inloggning för Skytap i Azure AD måste du lägga till enkel inloggning för Skytap från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **enkel inloggning för Skytap** i sökrutan.
1. Välj **enkel inloggning för Skytap** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurera och testa enkel inloggning med Azure AD för enkel inloggning för Skytap

Konfigurera och testa Azure AD SSO med enkel inloggning för Skytap med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i enkel inloggning för Skytap.

Här följer allmänna steg för att konfigurera och testa Azure AD SSO med enkel inloggning för Skytap:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.

    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. Konfigurera **[enkel inloggning för SKYTAP SSO](#configure-single-sign-on-for-skytap-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.

    a. **[Skapa en enkel inloggning för Skytap test användare](#create-single-sign-on-for-skytap-test-user)** för att få en motsvarighet till B. Simon i enkel inloggning för Skytap. Den här motsvarigheten är länkad till användarens Azure AD-representation.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/), på sidan **för enkel inloggning för Skytap** program integration, hittar du avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML Page med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL som använder följande mönster: `http://pingone.com/<custom EntityID>`

    b. I text rutan **svars-URL** anger du en URL som använder följande mönster: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Välj **Ange ytterligare URL: er**och utför följande steg om du vill konfigurera programmet i **SP** -initierat läge:

    a. I text rutan **inloggnings-URL** anger du en URL som använder följande mönster: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. I text rutan **vidarebefordra tillstånd** anger du en URL som använder följande mönster: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med aktuell identifierare, svars-URL, inloggnings-URL och relä status. Kontakta [enkel inloggning för Skytap-klientens support team](mailto:support@skytap.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned metadatafilen och spara den på din dator.

    ![Skärm bild av länken för hämtning av certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera enkel inloggning för Skytap** kopierar du lämplig URL eller URL, baserat på ditt krav.

    ![Skärm bild av webb adresser för kopierings konfiguration](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i fältet **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till enkel inloggning för Skytap.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **enkel inloggning för Skytap**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurera enkel inloggning för Skytap SSO

Om du vill konfigurera enkel inloggning på enkel inloggning för Skytap-sidan måste du skicka den hämtade **XML-koden för federationsmetadata**och lämpliga kopierade URL: er från Azure Portal till [enkel inloggning för Skytap-klientens support team](mailto:support@skytap.com). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Skapa enkel inloggning för Skytap test användare

I det här avsnittet skapar du en användare som heter B. Simon i enkel inloggning för Skytap. Arbeta med [enkel inloggning för Skytap-klientens support team](mailto:support@skytap.com) för att lägga till användare i enkel inloggning för Skytap-plattformen. Du kan inte använda enkel inloggning förrän du har skapat och aktiverat användare.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer den enkla inloggningen för Skytap-panelen i åtkomst panelen, bör du loggas in automatiskt på den enkla inloggningen för Skytap som du ställer in SSO för. Mer information finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova slack med Azure AD](https://aad.portal.azure.com/)

