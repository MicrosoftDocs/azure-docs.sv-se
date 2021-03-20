---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SAML-integrering med Klaxoon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Klaxoon SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 86a54e2450f9790bad590249f4dbad89fc3e2492
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-klaxoon-saml"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Klaxoon SAML

I den här självstudien får du lära dig hur du integrerar Klaxoon SAML med Azure Active Directory (Azure AD). När du integrerar Klaxoon SAML med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Klaxoon SAML.
* Gör det möjligt för användarna att logga in automatiskt för att Klaxoon SAML med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Klaxoon SAML-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Klaxoon SAML stöder **SP** -INITIERAd SSO

## <a name="adding-klaxoon-saml-from-the-gallery"></a>Lägga till Klaxoon SAML från galleriet

Om du vill konfigurera integrationen av Klaxoon SAML i Azure AD måste du lägga till Klaxoon SAML från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Klaxoon SAML** i sökrutan.
1. Välj **KLAXOON SAML** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-klaxoon-saml"></a>Konfigurera och testa Azure AD SSO för Klaxoon SAML

Konfigurera och testa Azure AD SSO med Klaxoon SAML med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Klaxoon SAML.

Utför följande steg för att konfigurera och testa Azure AD SSO med Klaxoon SAML:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera KLAXOON SAML SSO](#configure-klaxoon-saml-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa KLAXOON SAML test User](#create-klaxoon-saml-test-user)** – om du vill ha en motsvarighet till B. Simon i Klaxoon SAML som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **KLAXOON SAML** application integration i Azure Portal letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.klaxoon.com`
    
    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://enterprise-access.klaxoon.com/aaa/login/sso/<SUBDOMAIN>/callback`

    > [!Note]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och svars-URL: en. Kontakta [KLAXOON SAML client support team](mailto:help@klaxoon.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera KLAXOON SAML** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Klaxoon SAML.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **KLAXOON SAML**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-klaxoon-saml-sso"></a>Konfigurera Klaxoon SAML SSO

Om du vill konfigurera enkel inloggning på **KLAXOON SAML** -sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [Klaxoon SAML support team](mailto:help@klaxoon.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-klaxoon-saml-test-user"></a>Skapa Klaxoon SAML test User

I det här avsnittet skapar du en användare som heter Britta Simon i Klaxoon SAML. Arbeta med [KLAXOON SAML support team](mailto:help@klaxoon.com) för att lägga till användare i Klaxoon SAML-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till Klaxoon SAML-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Klaxoon SAML-inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Klaxoon SAML-panelen i Mina appar omdirigeras den till Klaxoon SAML-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Klaxoon SAML kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).