---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med 8x8 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 52b45bf7457a81b605df91e63a7c6ad539309dd8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88538662"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med 8x8

I den här självstudien får du lära dig hur du integrerar 8x8 med Azure Active Directory (Azure AD). När du integrerar 8x8 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till 8x8.
* Gör det möjligt för användarna att logga in automatiskt till 8x8 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En 8x8-prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* 8x8 stöder **SP-och IDP** -INITIERAd SSO

* När du har konfigurerat 8x8 kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-8x8-from-the-gallery"></a>Lägga till 8x8 från galleriet

Om du vill konfigurera integreringen av 8x8 i Azure AD måste du lägga till 8x8 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **8x8** i sökrutan.
1. Välj **8x8** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Konfigurera och testa Azure AD SSO för 8x8

Konfigurera och testa Azure AD SSO med 8x8 med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i 8x8.

Om du vill konfigurera och testa Azure AD SSO med 8x8, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera 8X8 SSO](#configure-8x8-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa 8x8 test User](#create-8x8-test-user)** -om du vill ha en motsvarighet till B. Simon i 8x8 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **8x8** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Skriv en URL i text rutan **identifierare** : `https://sso.8x8.com/saml2`

    b. Skriv en URL i textrutan **Svars-URL**: `https://sso.8x8.com/saml2`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator. Du kommer att använda certifikatet senare i självstudien i avsnittet **Konfigurera 8X8 SSO** .

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera 8x8** kopierar du URL: erna och använder dessa URL-värden senare i självstudien.

    ![Kopiera konfigurations-URL:er](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till 8x8.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **8x8**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-8x8-sso"></a>Konfigurera 8x8 SSO

Nästa del av själv studie kursen beror på vilken typ av prenumeration du har med 8x8.

* För 8x8-utgåvor och X-seriens kunder som använder Configuration Manager för administration, se [Konfigurera 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* För virtuella Office-kunder som använder konto hanteraren för administration, se [Konfigurera 8X8 Account Manager](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Konfigurera 8x8 Configuration Manager

1. Om du vill automatisera konfigurationen i 8x8 måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera 8x8** för att dirigera dig till 8x8-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på 8x8. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Logga in på 8x8 [Configuration Manager](https://vo-cm.8x8.com/) som administratör om du vill konfigurera 8x8 manuellt.

1. Klicka på **identitets hantering**på Start sidan.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure1.png)

1. Kontrol lera **enkel inloggning (SSO)** och välj **Microsoft Azure AD**.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopiera de tre URL: erna och signerings certifikatet från sidan **Konfigurera enkel inloggning med SAML** i Azure AD till avsnittet **Microsoft Azure AD SAML-inställningar** i 8x8 Configuration Manager.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Kopiera **inloggnings-URL** till **IDP-inloggnings webb adress**.

    b. Kopiera **Azure AD-identifieraren** till **IDP Issuer URL/urn**.

    c. Kopiera URL för **utloggning** till **IDP-utloggning**.

    d. Ladda ned **certifikat (base64)** och ladda upp till **certifikat**.

    e. Klicka på **Spara**.

### <a name="configure-8x8-account-manager"></a>Konfigurera 8x8 Account Manager

1. Logga in på 8x8 Virtual Office-klienten som administratör.

1. Välj **Virtual Office Account Mgr** (Kontohanteraren i Virtual Office) i programfönstret.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Välj **Business** (Företag) som kontotyp för hantering och klicka på **Sign In** (Logga in).

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klicka på fliken **ACCOUNTS** (KONTON) i menylistan.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klicka på **Single Sign On** (Enkel inloggning) i listan över konton.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Välj autentiseringsmetoden **Single Sign On** (Enkel inloggning) och klicka på **SAML**.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Utför följande steg i avsnittet **SAML Single Sign on** (Enkel inloggning med SAML):

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. I textrutan **Sign In URL** (Inloggnings-URL) klistrar du in **inloggnings-URL-värdet** som du har kopierat från Azure-portalen.

    b. I textrutan **Sign Out URL** (Utloggnings-URL) klistrar du in **utloggnings-URL-värdet** som du har kopierat från Azure-portalen.

    c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    d. Klicka på **Browse** (Bläddra) för att ladda upp det certifikat som du har laddat ned från Azure-portalen.

    e. Klicka på knappen **Spara**.

### <a name="create-8x8-test-user"></a>Skapa 8x8 test användare

I det här avsnittet skapar du en användare som heter Britta Simon i 8x8. Arbeta med [8x8 support team](https://www.8x8.com/about-us/contact-us) för att lägga till användare i 8x8-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen 8x8 på åtkomst panelen, bör du loggas in automatiskt på den 8x8 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova 8x8 med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du 8x8 med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)