---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med blomstra LXP | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och blomstra LXP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2021
ms.author: jeedes
ms.openlocfilehash: d1c1fba8da908133d1e4e4836832de3bed68476d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103202689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thrive-lxp"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med blomstra LXP

I den här självstudien får du lära dig att integrera blomstra-LXP med Azure Active Directory (Azure AD). När du integrerar blomstra-LXP med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till blomstra-LXP.
* Gör det möjligt för användarna att logga in automatiskt till blomstra LXP med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Blomstra LXP-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Blomstra LXP stöder **SP** -initierad SSO.

## <a name="adding-thrive-lxp-from-the-gallery"></a>Lägga till blomstra LXP från galleriet

Om du vill konfigurera integreringen av blomstra-LXP i Azure AD måste du lägga till blomstra LXP från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **blomstra LXP** i sökrutan.
1. Välj **blomstra LXP** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-thrive-lxp"></a>Konfigurera och testa Azure AD SSO för blomstra-LXP

Konfigurera och testa Azure AD SSO med blomstra LXP med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i blomstra LXP.

Utför följande steg för att konfigurera och testa Azure AD SSO med blomstra LXP:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera blomstra LXP SSO](#configure-thrive-lxp-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa blomstra LXP test User](#create-thrive-lxp-test-user)** – om du vill ha en motsvarighet till B. Simon i blomstra LXP som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **blomstra LXP** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `urn:amazon:cognito:sp:<THRIVE_LXP_IDENTIFIER>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>-lxp.auth.eu-west-2.amazoncognito.com/saml2/idpresponse`

    c. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.learn.link`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL. Kontakta [blomstra LXP client support team](mailto:support@thrivelearning.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera blomstra LXP** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till blomstra LXP.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **blomstra LXP**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-thrive-lxp-sso"></a>Konfigurera blomstra LXP SSO

Om du vill konfigurera enkel inloggning på **blomstra LXP** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [blomstra LXP support team](mailto:support@thrivelearning.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-thrive-lxp-test-user"></a>Skapa blomstra LXP-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i blomstra LXP. Arbeta med [blomstra LXP support team](mailto:support@thrivelearning.com) för att lägga till användarna på blomstra LXP-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till blomstra LXP-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till blomstra LXP-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen blomstra LXP i Mina appar omdirigeras det till blomstra LXP-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat blomstra LXP kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


