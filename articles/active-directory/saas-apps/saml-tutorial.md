---
title: 'Självstudie: Azure Active Directory integrering med SAML 1,1 token Enabled LOB-app | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML 1.1-tokenaktiverad LOB App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 477180a6576d52e3386e18b6e2ba12dd33e4d794
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654423"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Självstudie: Azure Active Directory integrering med SAML 1,1 token Enabled LOB-app

I den här självstudien får du lära dig hur du integrerar en LOB-app med SAML 1,1-token med Azure Active Directory (Azure AD). När du integrerar en SAML 1,1-token aktive rad LOB-app med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LOB 1,1-token-aktiverad LOB-app.
* Gör det möjligt för användarna att logga in automatiskt till SAML 1,1-token-aktiverad LOB-app med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAML 1,1-token aktiverade en prenumeration med enkel inloggning (SSO) för LOB-app.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML 1.1-tokenaktiverad LOB App stöder **SP**-initierad SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-saml-11-token-enabled-lob-app-from-the-gallery"></a>Lägg till SAML 1,1 token Enabled LOB-app från galleriet

Om du vill konfigurera integreringen av SAML 1.1-tokenaktiverad LOB App till Azure AD måste du lägga till SAML 1.1-tokenaktiverad LOB App från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAML 1,1 token Enabled LOB-app** i sökrutan.
1. Välj **SAML 1,1-token aktive rad LOB-app** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-saml-11-token-enabled-lob-app"></a>Konfigurera och testa Azure AD SSO för SAML 1,1 token-aktiverad LOB-app

Konfigurera och testa Azure AD SSO med SAML 1,1 token-aktiverad LOB-app med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAML 1,1-token-aktiverad LOB-app.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAML 1,1 token Enabled LOB-app:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SAML 1,1-token Enabled LOB-app SSO](#configure-saml-11-token-enabled-lob-app-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en saml 1,1-token-aktiverad LOB-app-test användare](#create-saml-11-token-enabled-lob-app-test-user)** – för att få en motsvarighet till B. Simon i SAML 1,1 token-aktiverad LOB-app som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **SAML 1,1-token aktiverade LOB-app** -programintegration i Azure Portal, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://your-app-url`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://your-app-url`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta supportteamet för SAML 1.1-tokenaktiverad LOB App-klienten för hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Set up SAML 1.1 Token enabled LOB App** (Konfigurera SAML 1.1-tokenaktiverad LOB App) kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till en SAML 1,1-token-aktiverad LOB-app.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SAML 1,1 token-aktiverad LOB-app**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-saml-11-token-enabled-lob-app-sso"></a>Konfigurera SAML 1,1-token aktiverat LOB-app SSO

För att kunna konfigurera enkel inloggning på **SAML 1.1-tokenaktiverad LOB App**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till SAML 1.1-tokenaktiverad LOB App-supporten. De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>Skapa SAML 1.1-tokenaktiverad LOB App-aktiverad testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i en SAML 1.1-token-aktiverad LOB App. Arbeta med SAML 1.1-tokenaktiverad LOB App-supportteamet för att lägga till användare i SAML 1.1-tokenaktiverad LOB App-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAML-appens inloggnings-URL för SAML 1,1 där du kan starta inloggnings flödet. 

* Gå till SAML 1,1 token Enabled LOB-appens inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen SAML 1,1-token aktiverat LOB-app i Mina appar omdirigeras den till SAML 1,1 token-inloggnings-URL för LOB-appen. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat en SAML 1,1-token aktive rad LOB-app kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).