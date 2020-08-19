---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nät | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och-nät.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.openlocfilehash: cfa81b52e8479851fc22b12cbc96a9507f0b2dc7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551397"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nät

I den här självstudien får du lära dig hur du integrerar nät med Azure Active Directory (Azure AD). När du integrerar nät med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till-nät.
* Gör det möjligt för användarna att logga in automatiskt för att kunna använda sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Stöd för stöd för **SP-och IDP** -INITIERAd SSO

## <a name="adding-harness-from-the-gallery"></a>Lägga till ett nät från galleriet

Om du vill konfigurera integrering av nät i Azure AD måste du lägga till ett nät från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du in **nät** i sökrutan.
1. Välj **nät** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Konfigurera och testa enkel inloggning med Azure AD för nät

Konfigurera och testa Azure AD SSO med ett nät med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i-nätet.

Om du vill konfigurera och testa Azure AD SSO med-nätet slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera](#configure-harness-sso)** enkel inloggning – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa en test användare](#create-harness-test-user)** för att få en motsvarighet till B. Simon i ett nät som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **för integrations program integration** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.harness.io/`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Du får den faktiska svars-URL: en från avsnittet **Konfigurera nät-SSO** , som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera ett nät** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ett nät.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **nät**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-harness-sso"></a>Konfigurera enkel inloggning för nät

1. Om du vill automatisera konfigurationen i ett nät måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, kan du klicka på **installations programmet** för att dirigera dig till programmet för nät. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på nätet. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera nät manuellt öppnar du ett nytt webbläsarfönster och loggar in på din företags webbplats som administratör och utför följande steg:

4. Klicka på Inställningar för **kontinuerlig säkerhets**  >  **åtkomst hantering**på sidan längst upp till höger på sidan  >  **Authentication Settings**.

    ![Konfiguration av nät](./media/harness-tutorial/configure01.png)

5. I avsnittet **SSO-providers** klickar du på **+ Lägg till SSO-providers**  >  **SAML**.

    ![Konfiguration av nät](./media/harness-tutorial/configure03.png)

6. Utför följande steg på popup-sidan för **SAML-providern** :

    ![Konfiguration av nät](./media/harness-tutorial/configure02.png)

    a. Kopiera **i SSO-providern, aktivera SAML-baserad inloggning och ange sedan följande URL-** instans och klistra in den i text rutan svars-URL i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    b. Skriv ditt visnings namn i text rutan **visnings namn** .

    c. Klicka på **Välj fil** för att ladda upp XML-filen för federationsmetadata som du har laddat ned från Azure AD.

    d. Klicka på **Skicka**.

### <a name="create-harness-test-user"></a>Skapa en test användare på nätet

Om du vill att Azure AD-användare ska kunna logga in på ett nät, måste de tillhandahållas i ett nät. I-nätet är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in för att ansluta som administratör.

1. Längst upp till höger på sidan klickar du på principer för **kontinuerlig säkerhets**  >  **åtkomst hantering**  >  **Users**.

    ![Konfiguration av nät](./media/harness-tutorial/configure04.png)

1. Klicka på **+ Lägg till användare**till höger på sidan.

    ![Konfiguration av nät](./media/harness-tutorial/configure05.png)

1. Utför följande steg i popup-fönstret **Lägg till användare** :

    ![Konfiguration av nät](./media/harness-tutorial/configure06.png)

    a. I text rutan **e-postadress (er)** anger du e-postadressen till användaren `B.simon@contoso.com` .

    b. Välj **användar grupper**.

    c. Klicka på **Skicka**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen nät på åtkomst panelen, bör du loggas in automatiskt på det nät som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova att använda Azure AD](https://aad.portal.azure.com/)

