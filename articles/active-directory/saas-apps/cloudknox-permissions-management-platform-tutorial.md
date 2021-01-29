---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med CloudKnox Permissions Management Platform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och CloudKnox behörigheter för hanterings plattform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 724bb91b4295162b32822d36d82958638d976e09
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudknox-permissions-management-platform"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med CloudKnox Permissions Management Platform

I den här självstudien får du lära dig hur du integrerar CloudKnox Permissions Management Platform med Azure Active Directory (Azure AD). När du integrerar CloudKnox behörighets hanterings plattform med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CloudKnox-plattform för behörighets hantering.
* Gör det möjligt för användarna att automatiskt vara inloggade på CloudKnox-plattform för hantering av behörigheter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CloudKnox behörighets hantering plattform enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CloudKnox Permissions Management Platform stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-cloudknox-permissions-management-platform-from-the-gallery"></a>Lägga till CloudKnox-plattform för behörighets hantering från galleriet

Om du vill konfigurera integreringen av CloudKnox-plattformen för behörighets hantering i Azure AD måste du lägga till CloudKnox Permissions Management Platform från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **CloudKnox Permissions Management Platform** i sökrutan.
1. Välj **CloudKnox behörighets hanterings plattform** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-cloudknox-permissions-management-platform"></a>Konfigurera och testa Azure AD SSO för CloudKnox behörighets hanterings plattform

Konfigurera och testa Azure AD SSO med CloudKnox-behörighets hanterings plattform med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CloudKnox Permissions Management Platform.

Utför följande steg för att konfigurera och testa Azure AD SSO med CloudKnox Permissions Management Platform:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CloudKnox behörigheter Management Platform SSO](#configure-cloudknox-permissions-management-platform-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa CloudKnox Permissions Management Platform test User](#create-cloudknox-permissions-management-platform-test-user)** -för att ha en motsvarighet till B. Simon i CloudKnox Permissions Management Platform som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **CloudKnox permission Management Platform** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://app.cloudknox.io/saml/<ID>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [CloudKnox Permissions Management Platform client support team](mailto:support@cloudknox.io) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. CloudKnox Permissions Management Platform-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar för attribut för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig CloudKnox behörigheter för hanterings plattformen att fler attribut skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | --------------- | --------- |
    | First_Name | user.givenname |
    | Grupper | användare. grupper |
    | Last_Name | user.surname |
    | Email_Address | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera CloudKnox för behörighets hantering** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CloudKnox behörighets hanterings plattform.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **CloudKnox Permissions Management Platform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cloudknox-permissions-management-platform-sso"></a>Konfigurera CloudKnox behörigheter Management Platform SSO

Om du vill konfigurera enkel inloggning på **plattforms sidan för CloudKnox-behörighets hantering** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [CloudKnox permission Management Platform support team](mailto:support@cloudknox.io). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cloudknox-permissions-management-platform-test-user"></a>Skapa CloudKnox Permissions Management Platform test User

I det här avsnittet skapar du en användare som heter Britta Simon i CloudKnox permission Management Platform. Arbeta med [CloudKnox permission Management Platform support team](mailto:support@cloudknox.io) för att lägga till användare i plattforms plattformen för CloudKnox-behörighets hantering. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den CloudKnox behörighets hanterings plattform som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen CloudKnox för hantering av behörighets hantering i Mina appar, bör du loggas in automatiskt på den CloudKnox behörighets hanterings plattform som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat CloudKnox behörighets hanterings plattform kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


