---
title: 'Självstudie: Azure Active Directory integrering med Replicon | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Replicon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 8394191820226a0d4fdcfe1a078e85e1caafa37f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534345"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Självstudie: integrera Replicon med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Replicon med Azure Active Directory (Azure AD). När du integrerar Replicon med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Replicon.
* Gör det möjligt för användarna att logga in automatiskt till Replicon med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Replicon för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Replicon stöder **SP** -initierad SSO.

## <a name="adding-replicon-from-the-gallery"></a>Lägga till Replicon från galleriet

Om du vill konfigurera integreringen av Replicon i Azure AD måste du lägga till Replicon från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Replicon** i sökrutan.
1. Välj **Replicon** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Replicon med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Replicon.

Om du vill konfigurera och testa Azure AD SSO med Replicon, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera REPLICON SSO](#configure-replicon-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Replicon test User](#create-replicon-test-user)** -om du vill ha en motsvarighet till B. Simon i Replicon som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Replicon** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. I rutan **identifierare** anger du en URL med följande mönster: `https://global.replicon.com/!/saml2/<client name>`

    1. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Replicon client support team](https://www.replicon.com/customerzone/contact-support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på ikonen Redigera/penna för **SAML-signeringscertifikat** om du vill redigera inställningarna.

    ![Signeringsalgoritm](common/signing-algorithm.png)

    1. Välj **signera SAML Assertion** som **signerings alternativ**.

    1. Välj **SHA-256** som **Signeringsalgoritm**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurera Replicon SSO

1. Logga in på din Replicon-företags webbplats som administratör i ett annat webbläsarfönster.

2. Utför följande steg för att konfigurera SAML 2,0:

    ![Aktivera SAML-autentisering](./media/replicon-tutorial/ic777805.png "Aktivera SAML-autentisering")

    a. För att Visa dialog rutan **EnableSAML Authentication2** lägger du till följande i URL: en efter företagets nyckel: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Följande visar schemat för den fullständiga URL: en: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicka på alternativet **+** för att expandera **v20Configuration** -avsnittet.

   c. Klicka på alternativet **+** för att expandera **metaDataConfiguration** -avsnittet.

   d. Välj **SHA256** för xmlSignatureAlgorithm

   e. Klicka på **Välj fil**, för att välja din metadata-XML-fil för identitetsprovider och klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Replicon.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Replicon**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-replicon-test-user"></a>Skapa Replicon test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i Replicon.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din Replicon-företags webbplats som administratör i ett webbläsarfönster.

2. Gå till **administrations \> användare**.

    ![Användare](./media/replicon-tutorial/ic777806.png "Användare")

3. Klicka på **+ Lägg till användare**.

    ![Lägg till användare](./media/replicon-tutorial/ic777807.png "Lägg till användare")

4. I avsnittet **användar profil** utför du följande steg:

    ![Användarprofil](./media/replicon-tutorial/ic777808.png "Användarprofil")

    a. I text rutan **inloggnings namn** skriver du in Azure AD-e-postadressen för den Azure AD-användare som du vill etablera `B.Simon@contoso.com` .

    > [!NOTE]
    > Inloggnings namnet måste matcha användarens e-postadress i Azure AD

    b. Som **Autentiseringstyp**väljer du **SSO**.

    c. Ange autentiserings-ID till samma värde som inloggnings namnet (användarens Azure AD-e-postadress)

    d. Skriv användarens avdelning i text rutan **avdelning** .

    e. Som **medarbetar typ**väljer du **administratör**.

    f. Klicka på **Spara användar profil**.

> [!NOTE]
> Du kan använda andra verktyg för Replicon av användar konton eller API: er som tillhandahålls av Replicon för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Replicon på åtkomst panelen, bör du loggas in automatiskt på den Replicon som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)