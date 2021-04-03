---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Lenses.io | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Lenses.io DataOps-portalen

I den här självstudien får du lära dig att integrera [lenses.io](https://lenses.io/) DataOps-portalen med Azure Active Directory (Azure AD). När du har integrerat Lenses.io med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Lenses.io-portalen.
* Gör det möjligt för användarna att logga in automatiskt till linser med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure AD](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En instans av en linser-Portal. Du kan välja mellan ett antal [distributions alternativ](https://lenses.io/product/deployment/).
* En Lenses.io- [licens](https://lenses.io/product/pricing/) som stöder enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Lenses.io stöder Service Provider (SP) initierad SSO.

* Du kan framtvinga kontroll av sessionen när du har konfigurerat Lenses.io. Session Control skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Lägg till Lenses.io från galleriet

Om du vill konfigurera integreringen av Lenses.io i Azure AD lägger du till Lenses.io i listan över hanterade SaaS-appar:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **Nytt program**.
1. I avsnittet **Lägg till från galleriet** , ange **lenses.io** i sökrutan.
1. Från panelen resultat väljer du **lenses.io** och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Konfigurera och testa Azure AD SSO för Lenses.io

Du skapar en test användare som heter *B. Simon* för att konfigurera och testa Azure AD SSO med lenses.io-portalen. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Lenses.io.

Slutför följande steg:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD test-användare och-grupp](#create-an-azure-ad-test-user-and-group) för att testa Azure AD SSO med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B. Simon att använda Azure AD SSO.
1. [Konfigurera LENSES.io SSO](#configure-lensesio-sso) för att konfigurera SSO-inställningar på program sidan.
    1. [Skapa lenses.io test Group-behörigheter](#create-lensesio-test-group-permissions) för att kontrol lera vad B. Simon kan komma åt i lenses.IO (auktorisering).
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **lenses.io** , letar reda på avsnittet **Hantera** och väljer sedan **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar ikonen för redigering av grundläggande SAML-konfiguration.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden i följande text inmatnings rutor:

    a. **Inloggnings-URL**: Ange en URL som har följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>` . Ett exempel är `https://lenses.my.company.com`.

    b. **Identifierare (enhets-ID)**: Ange en URL som har följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>` . Ett exempel är `https://lenses.my.company.com`.

    c. **Svars-URL**: Ange en URL som har följande mönster: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` . Ett exempel är `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dem med den faktiska inloggnings-URL: en, svars-URL: en och ID för bas-URL: en för din linser-Portal instans. Mer information finns i [dokumentationen om LENSES.io SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) .

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat** . Hitta **XML för federationsmetadata** och välj sedan **Ladda ned** för att ladda ned och spara certifikatet på datorn.

    ![Skärm bild som visar länken Hämta certifikat.](common/metadataxml.png)

1. I avsnittet **konfigurera lenses.io** använder du XML-filen som du laddade ned för att konfigurera linser mot din Azure SSO.

### <a name="create-an-azure-ad-test-user-and-group"></a>Skapa en Azure AD-test användare och-grupp

I Azure Portal skapar du en test användare som heter B. Simon. Sedan ska du skapa en test grupp som styr åtkomsten B. Simon har i linser.

Du kan ta reda på hur linser använder mappning av grupp medlemskap för auktorisering i [SSO-dokumentationen för linser](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Så här skapar du test användaren:**

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.  
   1. I rutan **användar namn** anger du username@companydomain.extension . Till exempel B.Simon@contoso.com.
   1. Markera kryss rutan **Visa lösen ord** . Skriv ned lösen ordet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

**Så här skapar du gruppen:**

1. Gå till **Azure Active Directory** och välj sedan **grupper**.
1. Överst på skärmen väljer du **ny grupp**.
1. I **grupp egenskaperna** följer du dessa steg:
   1. I rutan **typ av grupp** väljer du **säkerhet**.
   1. I rutan **grupp namn** anger du **LensesUsers**.
   1. Välj **Skapa**.
1. Välj gruppen **LensesUsers** och kopiera **objekt-ID:** t (till exempel f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Du använder detta ID i linser för att mappa användare av gruppen till [rätt behörigheter](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Så här tilldelar du gruppen till test användaren:**

1. Gå till **Azure Active Directory** och välj sedan **användare**.
1. Välj test användare **B. Simon**.
1. Välj **grupper**.
1. Överst på skärmen väljer du **Lägg till medlemskap**.
1. Sök efter och välj **LensesUsers**.
1. Klicka på **Välj**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Lenses.io.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **lenses.io**.
1. På sidan Översikt för appen i avsnittet **Hantera** väljer du **användare och grupper**.

   ![Skärm bild som visar länken "användare och grupper".](common/users-groups-blade.png)

1. Välj **Lägg till användare**.

   ![Skärm bild som visar länken Lägg till användare.](common/add-assign-user.png)

1. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Klicka sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Klicka sedan på knappen **Välj** längst ned på skärmen.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-lensesio-sso"></a>Konfigurera Lenses.io SSO

Om du vill konfigurera SSO på **lenses.io** -portalen installerar du den hämtade **XML-koden för federationsmetadata** på objektiv-instansen och [konfigurerar linser för att aktivera SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Skapa Lenses.io test Group-behörigheter

1. Om du vill skapa en grupp i linser använder du **objekt-ID: t** för **LensesUsers** -gruppen. Detta är det ID som du kopierade i avsnittet om att [skapa](#create-an-azure-ad-test-user-and-group)användare.
1. Tilldela önskade behörigheter för B. Simon.

Mer information finns i [grupp mappning för Azure-linser](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen Lenses.io på åtkomst panelen, bör du loggas in automatiskt på Lenses.io-portalen. Mer information finns i [Introduktion till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Konfigurera SSO i din Lenses.io-instans](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure AD](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure AD?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure AD?](../conditional-access/overview.md)

- [Prova Lenses.io med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Lenses.io med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)