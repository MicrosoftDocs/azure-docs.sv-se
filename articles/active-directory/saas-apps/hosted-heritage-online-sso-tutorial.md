---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med värd arvet online SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och värd arvet online SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: fe09da61f4d87fbef4ce6c8739cd79c7bcda6796
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442769"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-heritage-online-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med värd arvet online SSO

I den här självstudien får du lära dig att integrera värd arvet online SSO med Azure Active Directory (Azure AD). När du integrerar värd arvet online SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till värd arvet online SSO.
* Gör det möjligt för användarna att logga in automatiskt till värd arvet online SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Värd arvet online SSO, enkel inloggning (SSO), aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Värd arvet online SSO stöder **SP** -INITIERAd SSO

## <a name="adding-hosted-heritage-online-sso-from-the-gallery"></a>Lägga till värd arvet online SSO från galleriet

Om du vill konfigurera integrering av värd arvet online SSO i Azure AD måste du lägga till värd arvet online SSO från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **värd arvet online SSO** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **värd arvet online SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-heritage-online-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för värd arvet online SSO

Konfigurera och testa Azure AD SSO med värd arvet online SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i värd arvet online SSO.

Om du vill konfigurera och testa Azure AD SSO med värd arvet online SSO, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera värd arvet online SSO SSO](#configure-hosted-heritage-online-sso-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa värd arvet online SSO-testanvändare](#create-hosted-heritage-online-sso-test-user)** – om du vill ha en motsvarighet till B. Simon i värd arvet online SSO som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **värd arvet online SSO** -programintegration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.cirqahosting.com/Shibboleth.sso/Login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.cirqahosting.com/shibboleth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta det [värdbaserade arvet online SSO-klientprogrammet](mailto:support@isoxford.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till värd arvet online SSO.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **värd arvet online SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-hosted-heritage-online-sso-sso"></a>Konfigurera värd arvet online SSO SSO

Om du vill konfigurera enkel inloggning på **värd arvet online SSO** -sida måste du skicka **URL: en för appens Federations-metadata** till [värd arvet online SSO support team](mailto:support@isoxford.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-hosted-heritage-online-sso-test-user"></a>Skapa värd arvet online SSO test användare

I det här avsnittet skapar du en användare som heter B. Simon i värd arvet online SSO. Arbeta med [värd arvet online SSO support team](mailto:support@isoxford.com) för att lägga till användarna i den värdbaserade arvs plattformen online SSO. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den värd arvs panelen online SSO på åtkomst panelen, bör du loggas in automatiskt på det värd arvet online SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova värd arvet online SSO med Azure AD](https://aad.portal.azure.com/)