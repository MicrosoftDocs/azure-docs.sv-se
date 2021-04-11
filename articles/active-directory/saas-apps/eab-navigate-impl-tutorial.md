---
title: 'Självstudie: Azure Active Directory-integration med enkel inloggning (SSO) med EAB-implementering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och EAB-implementering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: a1f288aaa51585b825cd9a9970c4d179dbfd34da
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223157"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eab-implementation"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med EAB-implementering

I den här självstudien får du lära dig hur du integrerar EAB-implementering med Azure Active Directory (Azure AD). När du integrerar EAB-implementering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EAB-implementering.
* Gör det möjligt för användarna att logga in automatiskt till EAB-implementering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EAB-prenumeration för enkel inloggning (SSO) med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EAB-implementering stöder **SP** -initierad SSO.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-eab-implementation-from-the-gallery"></a>Lägga till EAB-implementering från galleriet

Om du vill konfigurera integrering av EAB-implementering i Azure AD måste du lägga till EAB-implementering från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **EAB-implementering** i sökrutan.
1. Välj **EAB-implementering** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-eab-implementation"></a>Konfigurera och testa Azure AD SSO för EAB-implementering

Konfigurera och testa Azure AD SSO med EAB-implementering med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EAB-implementeringen.

Utför följande steg för att konfigurera och testa Azure AD SSO med EAB-implementering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EAB-implementering SSO](#configure-eab-implementation-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa EAB implementation test User](#create-eab-implementation-test-user)** – för att få en motsvarighet till B. Simon i EAB-implementering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **EAB implementation** Application Integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **identifierare (enhets-ID)** anger du exakt följande värde: `https://impl.bouncer.eab.com`
    
    b. I text rutan **svars-URL: en (intyg om mottagar tjänst)** anger du båda följande värden som separata rader:
    
    | Svars-URL|
    | -------------- |
    | `https://impl.bouncer.eab.com/sso/saml2/acs` |
    | `https://impl.bouncer.eab.com/sso/saml2/acs/` |
    |
    
    c. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.navigate.impl.eab.com/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [EAB implementation support team](mailto:EABTechSupport@eab.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EAB-implementering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **EAB-implementering**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-eab-implementation-sso"></a>Konfigurera EAB-implementering SSO

Om du vill konfigurera enkel inloggning på **EAB-implementerings** sidan måste du skicka **URL: en för appens Federations-metadata** till [support teamet för EAB implementation](mailto:EABTechSupport@eab.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-eab-implementation-test-user"></a>Skapa EAB implementation test User

I det här avsnittet skapar du en användare som heter B. Simon i EAB-implementeringen. Arbeta med [support teamet för EAB-implementering](mailto:EABTechSupport@eab.com) för att lägga till användare på EAB-implementerings plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till EAB implementerings inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till EAB implementation inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen för EAB-implementering i Mina appar omdirigeras det till EAB implementations inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EAB-implementeringen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
