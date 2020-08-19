---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SiteIntel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SiteIntel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: f681dd2931300ec00fd6388b4636015c87f38170
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525105"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SiteIntel

I den här självstudien får du lära dig hur du integrerar SiteIntel med Azure Active Directory (Azure AD). När du integrerar SiteIntel med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SiteIntel.
* Gör det möjligt för användarna att logga in automatiskt på SiteIntel med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Mer information om SaaS-appar (Software as a Service) med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SiteIntel för enkel inloggning (SSO) – aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SiteIntel stöder SP-initierad och IdP-initierad SSO.
* När du har konfigurerat SiteIntel kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-siteintel-from-the-gallery"></a>Lägg till SiteIntel från galleriet

Om du vill konfigurera integreringen av SiteIntel i Azure AD måste du lägga till SiteIntel från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I rutan **Lägg till från galleriet** , ange **SiteIntel**.
1. I listan resultat väljer du **SiteIntel**och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Konfigurera och testa enkel inloggning med Azure AD för SiteIntel

Konfigurera och testa Azure AD SSO med SiteIntel med hjälp av en test användare som heter *B. Simon*. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SiteIntel.

Om du vill konfigurera och testa Azure AD SSO med SiteIntel, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.  

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med User B. Simon.  

    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera användare B. Simon för att använda enkel inloggning i Azure AD.

1. **[Konfigurera SITEINTEL SSO](#configure-siteintel-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.

    * **[Skapa en SiteIntel](#create-a-siteintel-test-user)** för att få en motsvarighet till användare B. Simon i SiteIntel som är länkat till användarens Azure AD-representation.

1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Gör så här om du vill aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)går du till avsnittet **Hantera** på sidan för **SiteIntel** program integrering och väljer sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** , bredvid **grundläggande SAML-konfiguration**, väljer du **Redigera** (Penn ikon).

   ![Skärm bild av fönstret "Konfigurera enkel inloggning med SAML"](common/edit-urls.png)

1. Om du vill konfigurera programmet i IdP läge, i avsnittet **grundläggande SAML-konfiguration** , gör du följande:

    a. I rutan **identifierare** anger du en URL i följande format: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`

    b. I rutan **svars-URL** anger du en URL i följande format: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`

    c. I rutan **relä tillstånd** anger du en URL i följande format: `https://<CLIENT>.siteintel.com`

1. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er**och gör sedan följande:

   * I rutan **inloggnings-URL** skriver du en URL i följande format: `https://<CLIENT>.siteintel.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dem med aktuell identifierare, svars-URL, inloggnings-URL och relä status. Kontakta [SiteIntel-klientens support team](mailto:support@intalytics.com)för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **kopierings** knappen för att kopiera URL: en i rutan URL för **app Federation Metadata** .

    ![Skärm bild av kopierings knappen "app Federation Metadata URL"](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** längst upp i fönstret.
1. I **användar** egenskaper gör du följande:

   a. I rutan **namn** anger du **B. Simon**.  

   b. I rutan **användar namn** anger du användar namnet i följande format: (till `username@companydomain.extension` exempel `B.Simon@contoso.com` ).

   c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

   d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du användare B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SiteIntel.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan **program** väljer du **SiteIntel**.
1. På sidan Översikt för appen i avsnittet **Hantera** väljer du **användare och grupper**.

   ![Skärm bild av länken "användare och grupper"](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan i fönstret **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av knappen "Lägg till användare"](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **B. Simon**och väljer sedan knappen **Välj** i den nedre delen av skärmen.
1. Om du förväntar dig ett roll värde i SAML-försäkran väljer du lämplig roll för användaren i listan i fönstret **Välj roll** och väljer sedan knappen **Välj** .
1. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-siteintel-sso"></a>Konfigurera SiteIntel SSO

Om du vill konfigurera enkel inloggning på SiteIntel-sidan skickar du den URL som du kopierade från rutan **URL för app Federation-Metadata** till [support teamet för SiteIntel](mailto:support@intalytics.com). De anger det här värdet för att upprätta en SAML SSO-anslutning korrekt på båda sidor.

### <a name="create-a-siteintel-test-user"></a>Skapa en SiteIntel-test användare

I det här avsnittet skapar du en användare som heter *Britta Simon* i SiteIntel. Arbeta med [SiteIntel support team](mailto:support@intalytics.com) för att lägga till användare i SiteIntel-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen **SiteIntel** på åtkomst panelen, bör du loggas in automatiskt på den SiteIntel som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Prova SiteIntel med Azure AD](https://aad.portal.azure.com/)
- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Så här skyddar du SiteIntel med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
