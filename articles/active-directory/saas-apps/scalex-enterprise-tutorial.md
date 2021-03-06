---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ScaleX Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScaleX Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: fecb7381786c36b077d329f68ca48ab10f65a984
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med ScaleX Enterprise

I den här självstudien får du lära dig hur du integrerar ScaleX Enterprise med Azure Active Directory (Azure AD). När du integrerar ScaleX Enterprise med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ScaleX Enterprise.
* Gör det möjligt för användarna att logga in automatiskt till ScaleX Enterprise med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ScaleX Enterprise enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ScaleX Enterprise stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Lägga till ScaleX Enterprise från galleriet

Om du vill konfigurera integreringen av ScaleX Enterprise i Azure AD måste du lägga till ScaleX Enterprise från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri** skriver du **scaleX Enterprise** i sökrutan.
1. Välj **scaleX Enterprise** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Konfigurera och testa enkel inloggning med Azure AD för ScaleX Enterprise

Konfigurera och testa Azure AD SSO med ScaleX Enterprise med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ScaleX Enterprise.

Om du vill konfigurera och testa Azure AD SSO med ScaleX Enterprise, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera scaleX Enterprise SSO](#configure-scalex-enterprise-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa scaleX Enterprise test User](#create-scalex-enterprise-test-user)** -för att få en motsvarighet till B. Simon i scaleX Enterprise som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **scaleX Enterprise** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://platform.rescale.com/saml2/<company id>/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [scaleX Enterprise client support team](https://about.rescale.com/contactus.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ditt ScaleX Enterprise-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärm bild visar en lista över standardattribut, där as **EmailAddress** mappas med **User. mail**. ScaleX Enterprise-appen förväntar sig att **EmailAddress** mappas med **User. UserPrincipalName**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera scaleX Enterprise** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ScaleX Enterprise.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **scaleX Enterprise**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-scalex-enterprise-sso"></a>Konfigurera ScaleX Enterprise SSO

1. Om du vill automatisera konfigurationen i ScaleX Enterprise måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera scaleX Enterprise** för att dirigera dig till scaleX Enterprise-programmet. Därifrån anger du de autentiseringsuppgifter som krävs för att logga in på ScaleX Enterprise. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera ScaleX Enterprise manuellt öppnar du ett nytt webbläsarfönster och loggar in på ditt ScaleX Enterprise-företags webbplats som administratör och utför följande steg:

1. Klicka på menyn längst upp till höger och välj **contoso administration**.

    > [!NOTE]
    > Contoso är bara ett exempel. Detta bör vara ditt faktiska företags namn.

    ![Skärm bild som visar ett exempel företags namn som valts på menyn längst upp till höger.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Välj **integrationer** på den översta menyn och välj **enkel inloggning**.

    ![Skärm bild som visar "integreringar" och "enkel inloggning" valt på den nedrullningsbara menyn.](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Fyll i formuläret enligt följande:

    ![Konfigurera enkel inloggning](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Välj **skapa en användare som kan autentisera med SSO**

    b. **Service Provider SAML**: klistra in värdet **_urn: Oasis: Names: TC: SAML: 2.0: NameID-format: persistent_**

    c. **Namn på e-postfältet med identitets leverantör i ACS-svar**: klistra in värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID för EntityDescriptor entitet för identitetsprovider:** Klistra in värdet för **Azure AD-identifieraren** som kopierats från Azure Portal.

    e. **SingleSignOnService-URL för identitetsprovider:** Klistra in **inloggnings-URL:** en från Azure Portal.

    f. **Offentligt X509-certifikat för identitetsprovider:** Öppna det X509-certifikat som du laddade ned från Azure i anteckningar och klistra in innehållet i den här rutan. Se till att det inte finns några rad brytningar mitt i certifikat innehållet.

    ex. Markera följande kryss rutor: **aktiverade, kryptera NameID och signera AuthnRequests.**

    h. Klicka på **Uppdatera SSO-inställningar** för att spara inställningarna.

### <a name="create-scalex-enterprise-test-user"></a>Skapa ScaleX Enterprise test User

Om du vill att Azure AD-användare ska kunna logga in på ScaleX Enterprise måste de tillhandahållas i ScaleX Enterprise. Om det gäller ScaleX Enterprise är etableringen en automatisk uppgift och inga manuella steg krävs. Alla användare som kan autentiseras med SSO-autentiseringsuppgifter kommer automatiskt att tillhandahållas på ScaleX-sidan.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ScaleX Enterprise på åtkomst panelen, bör du loggas in automatiskt på det ScaleX-företag som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova ScaleX Enterprise med Azure AD](https://aad.portal.azure.com/)
