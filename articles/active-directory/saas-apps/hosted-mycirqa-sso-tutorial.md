---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med värdbaserade MyCirqa SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och värdbaserade MyCirqa SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.openlocfilehash: 4a45954a0993994b80ac4301bc2abc08cecbaac8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521626"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med värdbaserade MyCirqa SSO

I den här självstudien får du lära dig att integrera värdbaserade MyCirqa SSO med Azure Active Directory (Azure AD). När du integrerar värdbaserade MyCirqa SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till värdbaserade MyCirqa SSO.
* Gör det möjligt för användarna att logga in automatiskt till värdbaserat MyCirqa SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Värdbaserad MyCirqa SSO-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Värdbaserad MyCirqa SSO stöder **SP** -INITIERAd SSO

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Lägga till värdbaserade MyCirqa SSO från galleriet

Om du vill konfigurera integreringen av värdbaserade MyCirqa SSO i Azure AD måste du lägga till värdbaserade MyCirqa SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **värdbaserade MyCirqa SSO** i sökrutan.
1. Välj **värdbaserade MYCIRQA SSO** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för enkel inloggning för värdbaserade MyCirqa

Konfigurera och testa Azure AD SSO med värdbaserade MyCirqa SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i den värdbaserade MyCirqa SSO.

Om du vill konfigurera och testa Azure AD SSO med den värdbaserade MyCirqa SSO, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera värdbaserade MYCIRQA SSO](#configure-hosted-mycirqa-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa värdbaserade MYCIRQA SSO-test](#create-hosted-mycirqa-sso-test-user)** för att få en motsvarighet till B. Simon i den värdbaserade MyCirqa SSO som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **värdbaserade MyCirqa SSO** -programintegration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta det [värdbaserade MYCIRQA SSO-klientprogrammet](not sure) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till värdbaserade MyCirqa SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **värdbaserad MYCIRQA SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-hosted-mycirqa-sso"></a>Konfigurera värdbaserade MyCirqa SSO

Om du vill konfigurera enkel inloggning på den **värdbaserade MYCIRQA SSO** -sidan måste du skicka **URL: en för appens Federations-metadata** till den [värdbaserade MyCirqa SSO support team](mailto:support@isoxford.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Skapa en värdbaserad MyCirqa SSO-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i den värdbaserade MyCirqa SSO. Arbeta med [MYCIRQA SSO support team](mailto:support@isoxford.com) för att lägga till användare i den värdbaserade MyCirqa SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den värdbaserade MyCirqa SSO-panelen på åtkomst panelen, bör du loggas in automatiskt till den värdbaserade MyCirqa SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova värdbaserade MyCirqa SSO med Azure AD](https://aad.portal.azure.com/)