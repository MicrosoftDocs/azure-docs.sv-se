---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med yuhu Property Management Platform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och yuhu-plattform för egenskaps hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: b2427d474f4a36dc518680518a10cace290c0136
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546137"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med yuhu Property Management Platform

I den här självstudien får du lära dig att integrera yuhu-plattform för egenskaps hantering med Azure Active Directory (Azure AD). När du integrerar yuhu-plattform för egenskaps hantering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till yuhu-plattform för egenskaps hantering.
* Gör det möjligt för användarna att logga in automatiskt till yuhu-plattform för egenskaps hantering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Yuhu för egenskaps hanterings plattform med enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Yuhu Property Management Platform stöder **SP** -INITIERAd SSO

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Lägga till yuhu-plattform för egenskaps hantering från galleriet

Om du vill konfigurera integreringen av yuhu-plattformen för egenskaps hantering i Azure AD måste du lägga till yuhu-plattform för egenskaps hantering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **yuhu Property Management Platform** i sökrutan.
1. Välj **yuhu-plattform för egenskaps hantering** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Konfigurera och testa enkel inloggning med Azure AD för yuhu Property Management Platform

Konfigurera och testa Azure AD SSO med yuhu egenskaps hanterings plattform med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i yuhu Property Management Platform.

Om du vill konfigurera och testa Azure AD SSO med yuhu Property Management Platform slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera yuhu Property Management Platform SSO](#configure-yuhu-property-management-platform-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa yuhu Property Management Platform test User](#create-yuhu-property-management-platform-test-user)** – om du vill ha en motsvarighet till B. Simon i yuhu för egenskaps hantering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan för **hantering av plattforms** program för yuhu-plattform, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.yuhu.io/companies`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `yuhu-<ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [yuhu Property Management Platform client support team](mailto:hello@yuhu.io) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Yuhu för egenskaps hanterings plattformen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig yuhu Property Management Platform-appen fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname ||
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera yuhu för egenskaps hantering** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till yuhu egenskaps hanterings plattform.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **yuhu Property Management Platform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-yuhu-property-management-platform-sso"></a>Konfigurera yuhu Property Management Platform SSO

Om du vill konfigurera enkel inloggning på **yuhu för egenskaps hanterings plattform** måste du skicka det nedladdade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [yuhu Property Management Platform support team](mailto:hello@yuhu.io). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-yuhu-property-management-platform-test-user"></a>Skapa yuhu Property Management Platform test User

I det här avsnittet skapar du en användare som heter B. Simon i yuhu Property Management Platform. Arbeta med [yuhu Property Management Platform support team](mailto:hello@yuhu.io) för att lägga till användare i plattforms plattformen för yuhu-. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för yuhu för egenskaps hantering i åtkomst panelen, bör du loggas in automatiskt på den yuhu för egenskaps hantering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova yuhu Property Management Platform with Azure AD](https://aad.portal.azure.com/)