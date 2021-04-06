---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AwareGo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AwareGo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735351"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Självstudie: Azure Active Directory integration med enkel inloggning med AwareGo

I den här självstudien får du lära dig hur du integrerar AwareGo med Azure Active Directory (Azure AD). När du integrerar AwareGo med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AwareGo.
* Gör det möjligt för användarna att logga in automatiskt på AwareGo med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En AwareGo-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. AwareGo stöder en Service Provider (SP)-initierad SSO.


## <a name="adding-awarego-from-the-gallery"></a>Lägga till AwareGo från galleriet

Om du vill konfigurera integreringen av AwareGo i Azure AD måste du lägga till AwareGo från galleriet till din lista över hanterade program som en tjänst (SaaS).

1. Logga in på Azure Portal med ett arbets konto, ett skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Välj **företags program**  >  **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **AwareGo** i sökrutan.
1. I resultat fönstret väljer du **AwareGo** och lägger sedan till appen. Om några sekunder läggs appen till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Konfigurera och testa Azure AD SSO för AwareGo

Konfigurera och testa Azure AD SSO med AwareGo med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AwareGo.

Om du vill konfigurera och testa Azure AD SSO med AwareGo gör du följande:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.  

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med User B. Simon.  
    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera användare B. Simon för att använda enkel inloggning i Azure AD.  

1. **[Konfigurera AWAREGO SSO](#configure-awarego-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.

    a. **[Skapa en AwareGo-testanvändare](#create-an-awarego-test-user)** som ska ha en motsvarighet till B. Simon i AwareGo som är länkad till Azure AD-representation av användaren.  
    b. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Gör så här om du vill aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal går du till sidan för program integrering i **AwareGo** under **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. Om du vill redigera inställningarna väljer du knappen **Redigera** i fönstret **Konfigurera enskilda Sign-On med SAML** .

   ![Skärm bild av knappen Redigera för grundläggande SAML-konfiguration.](common/edit-urls.png)

1. I fönstret Redigera, under **grundläggande SAML-konfiguration**, gör du följande:

    a. I rutan **inloggnings-URL** anger du någon av följande URL: er:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. I rutan **identifierare (enhets-ID)** anger du en URL i följande format: `https://<SUBDOMAIN>.awarego.com`

    c. I rutan **svars-URL** anger du en URL i följande format: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dem med den faktiska identifieraren och svars-URL: erna. Kontakta [AwareGo-klientens support team](mailto:support@awarego.com)för att få till gång till värdena. Du kan också se exemplen i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , bredvid **certifikat (base64)** väljer du **Hämta** för att ladda ned certifikatet och spara det på datorn.

    ![Skärm bild av länken för certifikatet "Ladda ned" i certifikat fönstret för SAML-signering.](common/certificatebase64.png)

1. I avsnittet **Konfigurera AwareGo** kopierar du en eller flera webb adresser, beroende på dina behov.

    ![Skärm bild av fönstret "Ställ in AwareGo" för att kopiera konfigurations-URL: er.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory** och väljer sedan **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I fönstret **användar** egenskaper gör du följande:

   a. I rutan **namn** anger du **B. Simon**.  
   b. I rutan **användar namn** anger du användar namnet i följande format: (till `<username>@<companydomain>.<extension>` exempel B.Simon@contoso.com ).  
   c. Markera kryss rutan **Visa lösen ord** och anteckna värdet som visas i rutan **lösen ord** för senare användning.  
   d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du användare B. Simon för att använda Azure SSO genom att bevilja åtkomst till AwareGo.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan **program** väljer du **AwareGo**.
1. På sidan Översikt för appen i avsnittet **Hantera** väljer du **användare och grupper**.
1. Välj **Lägg till användare** och sedan i fönstret **Lägg till tilldelning** väljer **du användare och grupper**.
1. I fönstret **användare och grupper** väljer du **B. Simon** i listan **användare** och väljer sedan knappen **Välj** .
1. Om du förväntar dig att tilldela en roll till användarna kan du välja den i list rutan **Välj en roll** . Om ingen roll har ställts in för den här appen väljs standard rollen för *åtkomst* .
1. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-awarego-sso"></a>Konfigurera AwareGo SSO

Om du vill konfigurera enkel inloggning på **AwareGo** -sidan skickar du certifikatet **(base64)** som du laddade ned tidigare och de URL: er som du kopierade tidigare från Azure Portal till [support teamet för AwareGo](mailto:support@awarego.com). Support teamet skapar den här inställningen för att upprätta en SAML SSO-anslutning korrekt på båda sidor.

### <a name="create-an-awarego-test-user"></a>Skapa en AwareGo-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i AwareGo. Arbeta med [AwareGo support team](mailto:support@awarego.com) för att lägga till användare i AwareGo-plattformen. Du måste skapa och aktivera användarna innan du kan använda enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet kan du testa konfigurationen för enkel inloggning med Azure AD genom att göra något av följande: 

* I Azure Portal väljer du **testa det här programmet**. Detta omdirigerar dig till inloggnings sidan för AwareGo där du kan initiera inloggnings flödet. 

* Gå till AwareGo-inloggnings sidan direkt och initiera inloggnings flödet därifrån.

* Gå till Microsoft Mina appar. När du väljer panelen **AwareGo** i Mina appar omdirigeras du till AwareGo-inloggnings sidan. Mer information finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AwareGo kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från Appkontroll för villkorsstyrd åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).