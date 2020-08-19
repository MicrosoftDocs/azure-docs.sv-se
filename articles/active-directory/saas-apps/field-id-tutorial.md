---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med fält-iD | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och fält-iD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: a9ace754a75d63bc24bea91dd6c88a3d004fd0eb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555104"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-field-id"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med fält-iD

I den här självstudien får du lära dig hur du integrerar fält-iD med Azure Active Directory (Azure AD). När du integrerar fält-iD med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till fält-iD.
* Gör det möjligt för användarna att logga in automatiskt till fält-iD med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration med enkel inloggning (SSO) för fält-iD.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Fält-iD stöder IDP initierad SSO.
* När du har konfigurerat fält-iD kan du framtvinga kontroll av sessioner. Detta skyddar mot exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-field-id-from-the-gallery"></a>Lägg till fält-iD från galleriet

Om du vill konfigurera integrering av fält-iD i Azure AD måste du lägga till fält-iD från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till det nya programmet väljer du **nytt program**.
1. I avsnittet **Lägg till från galleriet** skriver du **fält-ID** i sökrutan.
1. Välj **fält-ID** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-field-id"></a>Konfigurera och testa enkel inloggning med Azure AD för fält-iD

Konfigurera och testa Azure AD SSO med fält-iD med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i fält-iD.

Utför följande steg för att konfigurera och testa Azure AD SSO med fält-iD:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera fält-ID SSO](#configure-field-id-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. [Skapa en test användare för fält-ID](#create-a-field-id-test-user) för att ha en motsvarighet till B. Simon i fält-iD: t som är länkat till Azure AD-representationen av användaren.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan integration av program på **fält-ID** hittar du avsnittet **Hantera** . Välj sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

   a. I text rutan **identifierare** anger du en URL som använder följande mönster: `https://<tenantname>.fieldid.com/fieldid`

   b. I text rutan **svars-URL** anger du en URL som använder följande mönster: `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [support teamet för fält-ID](mailto:support@ecompliance.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du kopierings ikonen för att kopiera URL för **metadata för app Federation**. Spara den på din dator.

    ![Skärm bild av SAML-signerings certifikat med kopiera-ikonen markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. Som **namn**anger du `B.Simon` .  
   1. För **användar namn**anger du username@companydomain.extension (till exempel `B.Simon@contoso.com` ).
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till fält-iD.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **fält-ID**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Skärm bild av Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan **användare** och väljer sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-field-id-sso"></a>Konfigurera SSO för fält-iD

Om du vill konfigurera enkel inloggning på fält-iD-sidan skickar du **URL: en för appens Federations-metadata** till [support teamet för fält-ID](mailto:support@ecompliance.com). De säkerställer att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-a-field-id-test-user"></a>Skapa en test användare för fält-iD

I det här avsnittet skapar du en användare med namnet Britta Simon i fält-iD. Arbeta med [support teamet för fält-ID](mailto:support@ecompliance.com) för att lägga till användarna i fält-ID-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen fält-iD i åtkomst panelen, bör du loggas in automatiskt till det fält-iD som du ställer in SSO för. Mer information finns i [Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova fält-iD med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du fält-iD med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

