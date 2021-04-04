---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med asynkrona servicechannel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och asynkrona servicechannel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 413ffa54a7413ad9b2482a3a8b6c698b34116301
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729864"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicechannel"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med asynkrona servicechannel

I den här självstudien får du lära dig hur du integrerar asynkrona servicechannel med Azure Active Directory (Azure AD). När du integrerar asynkrona servicechannel med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till asynkrona servicechannel.
* Gör det möjligt för användarna att logga in automatiskt till asynkrona servicechannel med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Asynkrona servicechannel för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Asynkrona servicechannel stöder **IDP** INITIERAd SSO
* Asynkrona servicechannel stöder **just-in-Time** User-etablering

## <a name="adding-servicechannel-from-the-gallery"></a>Lägga till asynkrona servicechannel från galleriet

Om du vill konfigurera integreringen av asynkrona servicechannel i Azure AD måste du lägga till asynkrona servicechannel från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **asynkrona servicechannel** i sökrutan.
1. Välj **asynkrona servicechannel** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-servicechannel"></a>Konfigurera och testa Azure AD SSO för asynkrona servicechannel

Konfigurera och testa Azure AD SSO med asynkrona servicechannel med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i asynkrona servicechannel.

Utför följande steg för att konfigurera och testa Azure AD SSO med asynkrona servicechannel:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ASYNKRONA SERVICECHANNEL SSO](#configure-servicechannel-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa asynkrona servicechannel test User](#create-servicechannel-test-user)** -om du vill ha en motsvarighet till B. Simon i asynkrona servicechannel som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **asynkrona servicechannel** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** skriver du in värdet som: `http://adfs.<domain>.com/adfs/service/trust`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [asynkrona servicechannel client support team](https://servicechannel.zendesk.com/hc/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Roll anspråk är förkonfigurerat så att du inte behöver konfigurera det, men du måste fortfarande skapa dem i Azure AD med hjälp av den här [artikeln](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview). Du kan se asynkrona servicechannel- [Guide för mer information](https://servicechannel.zendesk.com/hc/articles/217514326-Azure-AD-Configuration-Example) om anspråk.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera asynkrona servicechannel** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till asynkrona servicechannel.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **asynkrona servicechannel**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-servicechannel-sso"></a>Konfigurera asynkrona servicechannel SSO

Om du vill konfigurera enkel inloggning på **asynkrona servicechannel** sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för asynkrona servicechannel](https://servicechannel.zendesk.com/hc/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-servicechannel-test-user"></a>Skapa asynkrona servicechannel test användare

Programmet stöder just-in-time-användaretablering, och efter autentiseringen skapas användare automatiskt i programmet. Kontakta [asynkrona servicechannel support-teamet](https://servicechannel.zendesk.com/hc/)om du vill ha fullständig användar etablering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den asynkrona servicechannel som du har konfigurerat SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen asynkrona servicechannel i Mina appar, bör du loggas in automatiskt på den asynkrona servicechannel som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat asynkrona servicechannel kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
