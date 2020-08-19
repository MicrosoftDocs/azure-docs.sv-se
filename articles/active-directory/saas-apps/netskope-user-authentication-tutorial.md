---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med autentisering med Netskope-användare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Netskope-användarautentisering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a2bd9b7240033df47f57dc3432ce50819d8754b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544034"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med autentisering med Netskope-användare

I den här självstudien får du lära dig att integrera Netskope-användarautentisering med Azure Active Directory (Azure AD). När du integrerar Netskope-användarautentisering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netskope-användarautentisering.
* Gör det möjligt för användarna att logga in automatiskt för att Netskope med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Netskope-prenumeration med enkel inloggning (SSO) för användar autentisering.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Netskope-användarautentisering stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Lägga till Netskope användarautentisering från galleriet

Om du vill konfigurera integrationen av Netskope-användarautentisering i Azure AD måste du lägga till Netskope användarautentisering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Netskope User Authentication** i sökrutan.
1. Välj **Netskope användarautentisering** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Konfigurera och testa enkel inloggning med Azure AD för Netskope-användarautentisering

Konfigurera och testa Azure AD SSO med Netskope-autentisering med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Netskope användarautentisering.

Slutför följande Bygg stenar om du vill konfigurera och testa Azure AD SSO med Netskope-användarautentisering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Netskope User Authentication SSO](#configure-netskope-user-authentication-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Netskope User Authentication test User](#create-netskope-user-authentication-test-user)** -för att få en motsvarighet till B. Simon i Netskope användarautentisering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Netskope User Authentication** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Du får de här värdena förklaras senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Inloggnings-URL-värdena är inte riktiga. Uppdatera inloggnings-URL-värdet med den faktiska inloggnings-URL: en. Kontakta [Netskope User Authentication support team](mailto:support@netskope.com) för att få inloggnings-URL-värde. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Netskope för användarautentisering** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Netskope-användarautentisering.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Netskope User Authentication**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurera Netskope User Authentication SSO

1. Öppna en ny flik i webbläsaren och logga in på företags webbplatsen för Netskope-användarautentisering som administratör.

1. Klicka på fliken **Active Platform** .

    ![Netskope konfiguration av användarautentisering](./media/netskope-user-authentication-tutorial/user1.png)

1. Rulla ned till **vidarebefordra proxy** och välj **SAML**.

    ![Netskope konfiguration av användarautentisering](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Utför följande steg på sidan **SAML-inställningar** :

    ![Netskope konfiguration av användarautentisering](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Kopiera **SAML-entitetens ID-** värde och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Kopiera URL-värde för **SAML ACS** och klistra in det i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. Klicka på **Lägg till konto**.

    ![Netskope konfiguration av användarautentisering](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. På sidan **Lägg till SAML-konto** utför du följande steg:

    ![Netskope konfiguration av användarautentisering](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. I text rutan **namn** anger du namnet som Azure AD.

    b. I text rutan **IDP URL** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    c. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    d. Öppna den hämtade metadatafilen i anteckningar, kopiera innehållet i den till Urklipp och klistra sedan in den i text rutan IDP- **certifikat** .

    e. Klicka på **Spara**.

### <a name="create-netskope-user-authentication-test-user"></a>Skapa Netskope-test för användarautentisering

1. Öppna en ny flik i webbläsaren och logga in på företags webbplatsen för Netskope-användarautentisering som administratör.

1. Klicka på fliken **Inställningar** i det vänstra navigerings fönstret.

    ![Användare som skapar användarautentisering med Netskope](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klicka på fliken **Active Platform** .

    ![Användare som skapar användarautentisering med Netskope](./media/netskope-user-authentication-tutorial/user1.png)

1. Klicka på fliken **användare** .

    ![Användare som skapar användarautentisering med Netskope](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klicka på **Lägg till användare**.

    ![Användare som skapar användarautentisering med Netskope](./media/netskope-user-authentication-tutorial/user-add.png)

1. Ange e-postadressen för den användare som du vill lägga till och klicka på **Lägg till**.

    ![Användare som skapar användarautentisering med Netskope](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Netskope User Authentication på åtkomst panelen, bör du loggas in automatiskt till den Netskope-användarautentisering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Netskope User Authentication with Azure AD](https://aad.portal.azure.com/)