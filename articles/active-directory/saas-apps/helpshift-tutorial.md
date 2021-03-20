---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med helpshift | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och helpshift.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: b20d47962bf5467b9a5d69327e7fc152215edaa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445077"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med helpshift

I den här självstudien får du lära dig hur du integrerar helpshift med Azure Active Directory (Azure AD). När du integrerar helpshift med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till helpshift.
* Gör det möjligt för användarna att logga in automatiskt till helpshift med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Helpshift för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Helpshift stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-helpshift-from-the-gallery"></a>Lägga till helpshift från galleriet

Om du vill konfigurera integreringen av helpshift i Azure AD måste du lägga till helpshift från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **helpshift** i sökrutan.
1. Välj **helpshift** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Konfigurera och testa enkel inloggning med Azure AD för helpshift

Konfigurera och testa Azure AD SSO med helpshift med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i helpshift.

Om du vill konfigurera och testa Azure AD SSO med helpshift, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera HELPSHIFT SSO](#configure-helpshift-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa helpshift test User](#create-helpshift-test-user)** -om du vill ha en motsvarighet till B. Simon i helpshift som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **helpshift** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<YourDOMAIN>.helpshift.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    d. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med aktuell identifierare, svars-URL, inloggnings-URL och relä status. Kontakta [helpshift client support team](mailto:support@helpshift.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera helpshift** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till helpshift.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **helpshift**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-helpshift-sso"></a>Konfigurera helpshift SSO

1. Logga in på ditt helpshift-program som administratör i en annan webbläsare.

1. Öppna helpshift- **instrumentpanelen** och klicka på **inställnings ikonen**.

    ![Skärm bild som visar ikonen helpshift-inställningar.](./media/helpshift-tutorial/configuration01.png)

1. Klicka på fliken **integrationer** och utför följande steg:

    ![Skärm bild som visar fliken integration där du kan utföra stegen som beskrivs.](./media/helpshift-tutorial/configuration02.png)

    a. Aktivera **enkel inloggning (SAML – SSO)**.

    b. Välj **identitets leverantör (IdP)** som **Azure Active Directory**.

    c. I text rutan **slut punkts-URL för SAML 2,0** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    d. Öppna filen med hämtade **certifikat (base64)** i anteckningar och kopiera innehållet i filen (utan att använda raderna "– – BEGIN Certificate —-" och "—-"-") och klistra in den i **X. 509-certifikatets** text ruta.

    e. I text rutan **utfärdar-URL** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    f. Klicka på **Verkställ ändringar**.

### <a name="create-helpshift-test-user"></a>Skapa helpshift test användare

I det här avsnittet skapar du en användare som heter B. Simon i helpshift. Arbeta med [helpshift-klientens support team](mailto:support@helpshift.com) för att lägga till användare i helpshift-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen helpshift på åtkomst panelen, bör du loggas in automatiskt på den helpshift som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova helpshift med Azure AD](https://aad.portal.azure.com/)