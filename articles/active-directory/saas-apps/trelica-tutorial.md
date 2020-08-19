---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Trelica | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: dcc304febe6fcebe3aba3047d1773e695b80f9e2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551949"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Trelica

I den här självstudien får du lära dig hur du integrerar Trelica med Azure Active Directory (Azure AD).

Med den här integreringen kan du:

* Kontroll i Azure AD som har åtkomst till Trelica.
* Gör det möjligt för användarna att logga in automatiskt på Trelica med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Trelica-prenumeration med enkel inloggning aktive rad (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Trelica stöder IDP-initierad SSO.
* Trelica stöder just-in-Time-etablering av användare.
* När du har konfigurerat Trelica kan du framtvinga kontroll av sessionen. Den här kontrollen skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Lägga till Trelica från galleriet

Om du vill konfigurera integreringen av Trelica i Azure AD måste du lägga till Trelica från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I navigerings fönstret längst till vänster väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , ange **Trelica** i sökrutan.
1. Välj **Trelica** från Sök resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Konfigurera och testa enkel inloggning med Azure AD för Trelica

Konfigurera och testa Azure AD SSO med Trelica med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Trelica.

Om du vill konfigurera och testa Azure AD SSO med Trelica, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera TRELICA SSO](#configure-trelica-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en Trelica-test](#create-a-trelica-test-user)** för att få en motsvarighet till B. Simon i Trelica. Den här motsvarigheten är länkad till användarens Azure AD-representation.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)går du till avsnittet **Hantera** på sidan för program integrering i **Trelica** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Sidan konfigurera enkel inloggning med SAML, med Penn ikonen för grundläggande SAML-konfiguration markerad](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du följande värden:

    1. I rutan **identifierare** anger du URL: en **https://app.trelica.com** .

    1. I rutan **svars-URL** anger du en URL som har mönstret `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL: en (även kallat ACS).
    > Du kan hitta detta genom att logga in på Trelica och gå till [konfigurations sidan för SAML Identity providers](https://app.trelica.com/Admin/Profile/SAML) (admin > konto > SAML). Klicka på kopierings knappen bredvid den **(ACS) URL: en** för att placera den i Urklipp, som du kan klistra in i text rutan **svars-URL** i Azure AD.
    > Läs [dokumentationen för Trelica-hjälpen](https://docs.trelica.com/admin/saml/azure-ad) eller kontakta [Trelica-klientens support team](mailto:support@trelica.com) om du har frågor.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på knappen Kopiera för att kopiera URL för **metadata för app Federation** och sparar den på datorn.

    ![Avsnittet SAML-signeringscertifikat med kopierings knappen bredvid URL för app Federation-Metadata](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare som heter B. Simon i Azure Portal.

1. I rutan längst till vänster i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. I fältet **namn** anger du **B. Simon**.
   1. I fältet **användar namn** anger du **B. Simon@**_companydomain_**.** _tillägg_. Till exempel B.Simon@contoso.com.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Trelica.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Trelica**.
1. På sidan Översikt för appen går du till avsnittet **Hantera** och väljer **användare och grupper**.

   ![Avsnittet hantera, där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

   ![Fönstret användare och grupper, där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-trelica-sso"></a>Konfigurera Trelica SSO

Om du vill konfigurera enkel inloggning på **Trelica** -sidan går du till [konfigurations sidan för SAML Identity providers](https://app.trelica.com/Admin/Profile/SAML) (admin > konto > SAML). Klicka på knappen **nytt** . Ange **Azure AD** som namn och välj **metadata från URL: en** för typen metadata. Klistra in **URL: en för app Federation-Metadata** som du tog från Azure AD till URL-fältet för **metadata** i Trelica.

Läs [dokumentationen för Trelica-hjälpen](https://docs.trelica.com/admin/saml/azure-ad) eller kontakta [Trelica-klientens support team](mailto:support@trelica.com) om du har frågor.

### <a name="create-a-trelica-test-user"></a>Skapa en Trelica-test användare

Trelica stöder just-in-Time-etablering, som är aktiverat som standard. Det finns ingen åtgärd att ta med i det här avsnittet. Om en användare inte redan finns i Trelica skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer panelen Trelica i portalen Mina appar loggas du automatiskt in på den Trelica som du ställer in SSO för. Mer information om mina apps-portalen finns i [Introduktion till portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Trelica med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Trelica med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
