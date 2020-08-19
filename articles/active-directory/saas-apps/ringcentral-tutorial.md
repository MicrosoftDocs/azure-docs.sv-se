---
title: 'Självstudie: Azure Active Directory integrering med RingCentral | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 6bdbb68e8d8748bc8851720588f8748d788e0cc0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542589"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Självstudie: integrera RingCentral med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar RingCentral med Azure Active Directory (Azure AD). När du integrerar RingCentral med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RingCentral.
* Gör det möjligt för användarna att logga in automatiskt till RingCentral med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* RingCentral för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RingCentral stöder **IDP** INITIERAd SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Lägga till RingCentral från galleriet

Om du vill konfigurera integreringen av RingCentral i Azure AD måste du lägga till RingCentral från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **RingCentral** i sökrutan.
1. Välj **RingCentral** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med RingCentral med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RingCentral.

Om du vill konfigurera och testa Azure AD SSO med RingCentral, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RINGCENTRAL SSO](#configure-ringcentral-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa RingCentral test User](#create-ringcentral-test-user)** -om du vill ha en motsvarighet till B. Simon i RingCentral som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **RingCentral** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    1. Klicka på **Ladda upp metadatafil**.
    1. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.
    1. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet **grundläggande SAML-konfiguration** .

    > [!Note]
    > Du hämtar **metadata-filen för tjänst leverantören** på konfigurations sidan för RingCentral SSO som beskrivs senare i självstudien.

1. Om du inte har en **metadata-fil för Service Provider**anger du värdena för följande fält:

    a. Skriv en URL i text rutan **identifierare** :
  
    | Identifierare |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. I textrutan **Svars-URL** anger du en URL:

    | Svars-URL |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RingCentral.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **RingCentral**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-ringcentral-sso"></a>Konfigurera RingCentral SSO

1. Om du vill automatisera konfigurationen i RingCentral måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera RingCentral** för att dirigera dig till RingCentral-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på RingCentral. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera RingCentral manuellt öppnar du ett nytt webbläsarfönster och loggar in på din RingCentral-företags webbplats som administratör och utför följande steg:

1. Klicka på **verktyg**längst upp.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigera till **enkel inloggning**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. På sidan **enkel inloggning** under **SSO-konfiguration** går du till **steg 1** och klickar på **Redigera** och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Utför följande steg på sidan **Konfigurera enkel inloggning** :

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Klicka på **Bläddra** för att ladda upp metadatafilen som du har laddat ned från Azure Portal.

    b. När du har laddat upp metadata får värdena automatiskt ifyllt i avsnittet om **allmän SSO-information** .

    c. Under **attribut mappning** väljer **du mappa e-postattribut till** som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Klicka på **Spara**.

    e. Från **steg 2** klickar du på **Ladda ned** för att ladda ned **metadata-filen för tjänste leverantören** och laddar upp den i avsnittet **grundläggande SAML-konfiguration** för att automatiskt fylla i **identifieraren** och **svara på URL** -värden i Azure Portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. På samma sida navigerar du till avsnittet **Aktivera SSO** och utför följande steg:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Välj **Aktivera SSO-tjänst**.

    * Välj **Tillåt att användare loggar in med SSO eller RingCentral-autentiseringsuppgifter**.

    * Klicka på **Spara**.

### <a name="create-ringcentral-test-user"></a>Skapa RingCentral test användare

I det här avsnittet skapar du en användare som heter Britta Simon i RingCentral. Arbeta med [RingCentral-klientens support team](https://success.ringcentral.com/RCContactSupp) för att lägga till användare i RingCentral-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen RingCentral på åtkomst panelen, bör du loggas in automatiskt på den RingCentral som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova RingCentral med Azure AD](https://aad.portal.azure.com/)
