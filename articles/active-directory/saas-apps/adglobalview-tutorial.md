---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ADP-Globalview (inaktuell) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ADP-Globalview (inaktuell).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 08b1294436ead372234104008a48ca23e56de389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589318"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp-globalview-deprecated"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ADP-Globalview (inaktuell)

I den här självstudien får du lära dig hur du integrerar ADP-Globalview (inaktuellt) med Azure Active Directory (Azure AD). När du integrerar ADP-Globalview (inaktuellt) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ADP-Globalview (inaktuell).
* Gör det möjligt för användarna att logga in automatiskt till ADP-Globalview (inaktuell) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ADP-Globalview (inaktuell) enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ADP-Globalview (inaktuell) stöder **IDP** initierad SSO.

## <a name="adding-adp-globalview-deprecated-from-the-gallery"></a>Lägga till ADP-Globalview (inaktuellt) från galleriet

Om du vill konfigurera Globalview för ADP (inaktuell) i Azure AD måste du lägga till ADP-Globalview (inaktuell) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ADP Globalview (inaktuellt)** i sökrutan.
1. Välj **ADP-Globalview (inaktuellt)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adp-globalview-deprecated"></a>Konfigurera och testa Azure AD SSO för ADP-Globalview (inaktuellt)

Konfigurera och testa Azure AD SSO med ADP-Globalview (inaktuellt) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ADP-Globalview (inaktuell).

Utför följande steg för att konfigurera och testa Azure AD SSO med ADP-Globalview (inaktuell):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ADP-Globalview (inaktuellt) SSO](#configure-adp-globalview-deprecated-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ADP-Globalview (inaktuell) testa användaren](#create-adp-globalview-deprecated-test-user)** – om du vill ha en motsvarighet till B. Simon i ADP-Globalview (inaktuell) som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal, på sidan **ADP-Globalview (föråldrad)** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    
    I text rutan **identifierare** anger du en URL med något av följande mönster:

    | Identifierare |
    | ----------- |
    | `https://<subdomain>.globalview.adp.com/federate` |
    | `https://<subdomain>.globalview.adp.com/federate2` |
    |

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [ADP-Globalview (inaktuellt) klient support teamet](https://www.adp.com/contact-us/overview.aspx) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera ADP-Globalview (inaktuell)** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ADP-Globalview (inaktuell).

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **ADP-Globalview (inaktuellt)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-adp-globalview-deprecated-sso"></a>Konfigurera ADP-Globalview (inaktuellt) SSO

Om du vill konfigurera enkel inloggning på **ADP-Globalview (inaktuell)** , måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [ADP Globalview (inaktuell) support team](https://www.adp.com/contact-us/overview.aspx). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-adp-globalview-deprecated-test-user"></a>Skapa ADP-Globalview (inaktuell) test användare

I det här avsnittet skapar du en användare som heter B. Simon i ADP-Globalview (inaktuell). Arbeta med [ADP-Globalview (inaktuellt) support team](https://www.adp.com/contact-us/overview.aspx) för att lägga till användare i ADP-Globalview (inaktuell). Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till ADP-Globalview (inaktuell) som du konfigurerar SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen ADP-Globalview (inaktuell) i Mina appar, bör du loggas in automatiskt till ADP-Globalview (inaktuell) som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ADP-Globalview (inaktuell) kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).