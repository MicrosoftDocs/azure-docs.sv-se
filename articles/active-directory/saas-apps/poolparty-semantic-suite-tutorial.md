---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med PoolParty semantiskt paket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PoolParty semantisk Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 3cd76eb3e6313b21bbc018bc14287c38517537f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-poolparty-semantic-suite"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med PoolParty semantiskt Suite

I den här självstudien får du lära dig att integrera PoolParty semantisk Suite med Azure Active Directory (Azure AD). När du integrerar PoolParty semantisk Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PoolParty semantisk Suite.
* Gör det möjligt för användarna att logga in automatiskt till PoolParty semantisk Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PoolParty-prenumeration med enkel inloggning (SSO) för semantiskt paket.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PoolParty semantisk Svit stöder **SP** -INITIERAd SSO

## <a name="adding-poolparty-semantic-suite-from-the-gallery"></a>Lägga till PoolParty semantisk Suite från galleriet

Om du vill konfigurera integrering av PoolParty-semantiskt paket i Azure AD måste du lägga till PoolParty semantisk Suite från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **PoolParty semantisk Suite** i sökrutan.
1. Välj **PoolParty semantisk Suite** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-poolparty-semantic-suite"></a>Konfigurera och testa Azure AD SSO för PoolParty semantiskt Suite

Konfigurera och testa Azure AD SSO med PoolParty semantisk Svit med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i PoolParty semantisk svit.

Utför följande steg för att konfigurera och testa Azure AD SSO med PoolParty semantiskt Suite:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PoolParty semantisk Suite SSO](#configure-poolparty-semantic-suite-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PoolParty semantisk Suite-test](#create-poolparty-semantic-suite-test-user)** för att få en motsvarighet till B. Simon i PoolParty-semantisk Suite som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **PoolParty semantik Suite** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<CustomerName>.poolparty.biz/PoolParty/`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<CustomerName>.poolparty.biz/<ID>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CustomerName>.poolparty.biz/<ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [PoolParty-avdelningen för semantisk Suite-support](mailto:support@poolparty.biz) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera PoolParty semantiskt Suite** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PoolParty semantisk Suite.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **PoolParty semantik Suite**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-poolparty-semantic-suite-sso"></a>Konfigurera PoolParty semantisk Suite SSO

Om du vill konfigurera enkel inloggning på **PoolParty semantisk Suite** -sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [PoolParty för semantisk Suite-support](mailto:support@poolparty.biz). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-poolparty-semantic-suite-test-user"></a>Skapa PoolParty semantisk Suite-test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i PoolParty-semantik. Arbeta med [PoolParty-avdelning för semantisk Suite-support](mailto:support@poolparty.biz) för att lägga till användare i PoolParty-plattformen för semantisk Suite. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till PoolParty för semantisk Suite-inloggning där du kan starta inloggnings flödet. 

* Gå till PoolParty för semantisk Suite-inloggning direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen PoolParty semantik Suite i Mina appar omdirigeras den till PoolParty för semantisk Suite-inloggning. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat PoolParty semantisk Suite kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).