---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med hubb Planner | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hub Planner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: 2d9dc483a9d60dc395c0aff52b721690d4fc701d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442447"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med hubb planerare

I den här självstudien får du lära dig att integrera hubb planeraren med Azure Active Directory (Azure AD). När du integrerar hubb planeraren med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Hub Planner.
* Gör det möjligt för användarna att logga in automatiskt till hubb planeraren med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktive rad prenumeration för enkel inloggning (SSO) i hubb Planner.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Hub Planner stöder **SP** -initierad SSO.
* När du har konfigurerat hubben för hubb kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-hub-planner-from-the-gallery"></a>Lägga till Hub Planner från galleriet

Om du vill konfigurera en integrering av Hub Planner i Azure AD måste du lägga till Hub Planner från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Hub Planner** i sökrutan.
1. Välj **Hub Planner** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Konfigurera och testa enkel inloggning med Azure AD för hubb planerare

Konfigurera och testa Azure AD SSO med hubb Planner med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i hubb planeraren.

Om du vill konfigurera och testa Azure AD SSO med Hub Planner slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Hub Planner SSO](#configure-hub-planner-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en test användare av Hub Planner](#create-hub-planner-test-user)** – om du vill ha en motsvarighet till B. Simon i hubb planeraren som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Hub Planner** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.hubplanner.com`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://app.hubplanner.com/sso/metadata`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.hubplanner.com/sso/callback`

    > [!NOTE]
    > De här värdena är de som du kommer att använda. Den enda ändringen du behöver göra är att ersätta \<SUBDOMAIN\> i **inloggnings-URL: en** med den under domän som du fick när du registrerade dig för Hub Planner. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera hubben för hubb** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Hub Planner.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **hubb planerare**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-hub-planner-sso"></a>Konfigurera Hub Planner SSO

Om du vill konfigurera enkel inloggning på **Hub Planner** -sidan måste du logga in på ditt Hub Planner-konto och utföra följande uppgifter. 

### <a name="install-the-extension-in-hub-planner"></a>Installera tillägget i hubb planeraren

Om du vill aktivera SSO-funktioner måste du först aktivera tillägget. Slutför följande steg som konto ägare eller med motsvarande behörigheter:

1. Gå till **Inställningar**.
1. På menyn på sidan väljer du **Hantera tillägg**  >  **Lägg till/ta bort tillägg**.
1. Hitta tillägget för enkel inloggning och Lägg till eller prova kostnads fritt.
1. När du uppmanas till det godkänner du villkoren och väljer sedan **Lägg till nu**.

### <a name="enable-sso"></a>Aktivera enkel inloggning

När tillägget har Aktiver ATS måste du aktivera SSO för ditt konto. 

1. Gå till **Inställningar**.
1. På menyn på sidan väljer du **autentisering**.
1. Välj **SSO (enkel inloggning)**.
1. Ange ytterligare autentiseringsinformation som visas i följande bild och välj sedan **Spara**.

![Skärm bild av SSO-inställningar](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Skapa en test användare för Hub Planner

Om du vill lägga till andra användare går du till **Inställningar**  >  **Hantera resurser** och lägger till användare härifrån. Se till att lägga till deras e-postadress och Bjud in dem. När de bjudits in får de ett e-postmeddelande och kan ange via SSO. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för NAV planering i åtkomst panelen, bör du loggas in automatiskt på den hubb-Planner som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Testa Hub Planner med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du hubb planeraren med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)