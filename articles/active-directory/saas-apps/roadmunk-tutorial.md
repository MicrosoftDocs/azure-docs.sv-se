---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Roadmunk'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181733"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Roadmunk

I den här självstudien får du lära dig hur du integrerar Roadmunk med Azure Active Directory (Azure AD). När du integrerar Roadmunk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Roadmunk.
* Gör det möjligt för användarna att logga in automatiskt på Roadmunk med hjälp av deras Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Roadmunk-prenumeration som är aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

Roadmunk stöder SSO som startas av *tjänst leverantören* (SP) och av *identitets leverantören* (IdP).

## <a name="add-roadmunk-from-the-gallery"></a>Lägg till Roadmunk från galleriet

Om du vill integrera Roadmunk i Azure AD går du till galleriet och lägger till Roadmunk i listan över hanterade SaaS-appar:

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I rutan **Lägg till från galleriet** i rutan Sök skriver du **Roadmunk**.
1. Välj **Roadmunk** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Konfigurera och testa Azure AD SSO för Roadmunk

Konfigurera och testa Azure AD SSO med Roadmunk med hjälp av en test användare som heter *B. Simon*. Om du vill göra SSO-arbete måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Roadmunk.

Här är en översikt över hur du konfigurerar och testar Azure AD SSO med Roadmunk:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med hjälp av B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B. Simon att använda Azure AD SSO.
1. [Konfigurera ROADMUNK SSO](#configure-roadmunk-sso) för att konfigurera SSO-inställningar på program sidan.
    1. [Skapa en Roadmunk-test användare](#create-roadmunk-test-user) så att du kan länka motsvarigheten till B. Simon i Roadmunk till Azure AD-åter givningen av användaren.
1. [Testa SSO](#test-sso) för att kontrol lera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I Azure Portal går du till sidan för program integrering i **Roadmunk** , letar reda på avsnittet **Hantera** och väljer sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar redigerings ikonen för grundläggande SAML-konfiguration.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en SP-metadatafil och du vill konfigurera i IDP läge, följer du dessa steg:

    a. Välj **Ladda upp metadatafil**.

    ![Skärm bild som visar länken för uppladdning av metadatafil.](common/upload-metadata.png)

    b. Välj mappikonen för att välja den metadatafil som du laddade ned i steg 4 i proceduren "Konfigurera Roadmunk SSO". Välj sedan **Ladda upp**.

    ![Skärm bild som visar hur du väljer metadatafilen.](common/browse-upload-metadata.png)

    När metadatafilen har laddats upp fylls **ID** och **svars-URL** -värden automatiskt i avsnittet **grundläggande SAML-konfiguration** .

    ![Skärm bild som visar avsnittet grundläggande SAML-konfiguration. Fältet identifierare och fältet svars-URL är markerat.](common/idp-intiated.png)

    > [!Note]
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt fyller du i värdena manuellt.

1. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er**. I fältet **inloggnings-URL** skriver du `https://login.roadmunk.com`

    ![Skärm bild som visar var du anger en inloggnings-URL för SP-initierat läge.](common/metadata-upload-additional-signon.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj sedan **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Skärm bild som visar nedladdnings länken för SAML-signerings certifikatet.](common/metadataxml.png)

1. I avsnittet **Konfigurera Roadmunk** kopierar du den URL eller de URL: er som du behöver.

    ![Skärm bild som visar var du kan kopiera konfigurations webb adresser.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal. Du namnger användaren *B. Simon*.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** längst upp i fönstret.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ange till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda Azure SSO genom att bevilja åtkomst till Roadmunk.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **Roadmunk**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer sedan **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** . Välj sedan **Välj** i slutet av dialog rutan.
1. Om du förväntar dig att en roll ska tilldelas användarna väljer du den på list menyn **Välj en roll** . Om ingen roll har ställts in för den här appen väljs standard rollen för **åtkomst** .
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-roadmunk-sso"></a>Konfigurera Roadmunk SSO

1. Logga in på Roadmunk-webbplatsen som administratör.

1. Längst ned på sidan väljer du användar ikonen och väljer sedan **konto inställningar**.

    ![Skärm bild som visar var du kan välja inställningar för användar konton.](./media/roadmunk-tutorial/account.png)

1. Gå till inställningar för **företags**  >  **autentisering**.

1. Följ dessa steg på sidan **autentiseringsinställningar** :

    ![Skärm bild som visar sidan autentiseringsinställningar.](./media/roadmunk-tutorial/saml-sso.png)

    a. Aktivera **SAML enkel inloggning (SSO)**.

    b. I avsnittet **steg 1** kan du antingen ladda upp metadata-XML-filen eller ange URL: en för metadata.

    c. I avsnittet **steg 2** laddar du ned Roadmunk-metadatafilen och sparar den sedan på din dator.

    d. Om du vill logga in med hjälp av SSO går du till avsnittet **steg 3** och väljer **genomdriva SAML-Sign-In**.

    e. Välj **Spara**.


### <a name="create-roadmunk-test-user"></a>Skapa Roadmunk test användare

1. Logga in på Roadmunk-webbplatsen som administratör.

1. Välj användar ikonen längst ned på sidan och välj sedan **konto inställningar**.

    ![Skärm bild som visar hur du öppnar konto inställningar för test användaren.](./media/roadmunk-tutorial/account.png)

1. Öppna fliken **användare** och välj sedan **Bjud in användare**.

    ![Skärm bild som visar fliken användare. Knappen Bjud in användare är markerad. I fönstret öppna markeras fälten e-post och roll.](./media/roadmunk-tutorial/create-user.png)

1. Fyll i den information som krävs i formuläret som visas och välj sedan **Bjud in**.


## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen **Roadmunk** i portalen Mina appar, bör du loggas in automatiskt på det Roadmunk-konto som du ställer in SSO för. Mer information finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Roadmunk kan du framtvinga kontroll av sessionen. Session Control skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. 

Lär dig hur du [tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).