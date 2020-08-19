---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med perforce Helix Core-Helix Authentication Service | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och perforce Helix Core-Helix Authentication Service.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 94464827f0a3cb797fa0e1b0249131e5a110dbad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553877"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perforce-helix-core---helix-authentication-service"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med perforce Helix Core-Helix Authentication Service

I den här självstudien får du lära dig att integrera perforce Helix Core-Helix Authentication Service med Azure Active Directory (Azure AD). När du integrerar perforce Helix Core-Helix Authentication Service med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till perforce Helix Core-Helix Authentication Service.
* Gör det möjligt för användarna att logga in automatiskt till perforce Helix Core-Helix Authentication Service med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Perforce Helix Core-Helix Authentication Service, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Perforce Helix Core-Helix Authentication Service stöder **SP** -INITIERAd SSO
* När du har konfigurerat perforce Helix Core-Helix Authentication Service kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-perforce-helix-core---helix-authentication-service-from-the-gallery"></a>Lägga till perforce Helix Core-Helix Authentication Service från galleriet

Om du vill konfigurera integreringen av perforce Helix Core-Helix Authentication Service i Azure AD, måste du lägga till perforce Helix Core-Helix Authentication Service från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **perforce Helix Core-Helix Authentication Service** i sökrutan.
1. Välj **perforce Helix Core-Helix Authentication Service** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-perforce-helix-core---helix-authentication-service"></a>Konfigurera och testa enkel inloggning med Azure AD för perforce Helix Core-Helix Authentication Service

Konfigurera och testa Azure AD SSO med perforce Helix Core-Helix Authentication Service med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i perforce Helix Core-Helix Authentication Service.

Om du vill konfigurera och testa Azure AD SSO med perforce Helix Core-Helix Authentication Service, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera perforce Helix Core-Helix Authentication Service SSO](#configure-perforce-helix-core---helix-authentication-service-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa perforce Helix Core-Helix Authentication Service-test](#create-perforce-helix-core---helix-authentication-service-test-user)** för att få en motsvarighet till B. Simon i perforce Helix Core-Helix Authentication Service som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **perforce Helix Core-Helix Authentication Service** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<helix-auth-service>.<customer-hostname>.com/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<helix-auth-service>.<customer-hostname>.com/saml`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<HELIX-AUTH-SERVICE>.<CUSTOMER-HOSTNAME>.com/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [perforce Helix Core-Helix Authentication Service support team](mailto:support@perforce.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till perforce Helix Core-Helix Authentication Service.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **perforce Helix Core-Helix Authentication Service**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-perforce-helix-core---helix-authentication-service-sso"></a>Konfigurera perforce Helix Core-Helix Authentication Service SSO

Om du vill konfigurera enkel inloggning på **perforce Helix Core-Helix Authentication Service** -sidan måste du skicka **URL: en för appens Federations-metadata** till [perforce Helix Core-Helix Authentication Service-teamet](mailto:support@perforce.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-perforce-helix-core---helix-authentication-service-test-user"></a>Skapa perforce Helix Core-Helix Authentication Service-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i perforce Helix Core-Helix Authentication Service. Arbeta med [perforce Helix Core-Helix Authentication Service-teamet](mailto:support@perforce.com) för att lägga till användare i perforce Helix Core-Helix Authentication Service Platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen perforce Helix Core-Helix Authentication Service på åtkomst panelen, bör du loggas in automatiskt till perforce Helix-tjänsten Core-Helix som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova perforce Helix Core-Helix Authentication Service med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda perforce Helix Core-Helix Authentication Service med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
