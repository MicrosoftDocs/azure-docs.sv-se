---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Grovo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Grovo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 84bbec783630aadc68a9632c90ee90f4a8cc98d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92446709"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Grovo

I den här självstudien får du lära dig hur du integrerar Grovo med Azure Active Directory (Azure AD). När du integrerar Grovo med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Grovo.
* Gör det möjligt för användarna att logga in automatiskt till Grovo med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Grovo för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Grovo stöder **SP-och IDP** -INITIERAd SSO
* Grovo stöder **just-in-Time** User-etablering

## <a name="adding-grovo-from-the-gallery"></a>Lägga till Grovo från galleriet

Om du vill konfigurera integreringen av Grovo i Azure AD måste du lägga till Grovo från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Grovo** i sökrutan.
1. Välj **Grovo** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Konfigurera och testa enkel inloggning med Azure AD för Grovo

Konfigurera och testa Azure AD SSO med Grovo med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Grovo.

Om du vill konfigurera och testa Azure AD SSO med Grovo, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GROVO SSO](#configure-grovo-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Grovo test User](#create-grovo-test-user)** -om du vill ha en motsvarighet till B. Simon i Grovo som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Grovo** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<subdomain>.grovo.com`

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med aktuell identifierare, svars-URL, inloggnings-URL och relä status. Kontakta [Grovo client support team](https://www.grovo.com/contact-us) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Grovo** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Grovo.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Grovo**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-grovo-sso"></a>Konfigurera Grovo SSO

1. Logga in på Grovo som administratör i ett annat webbläsarfönster.

2. Gå till **Administratörs**  >  **integreringar**.
 
    ![Skärm bild som visar "administratör"-menyn med "integrations" valda.](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Klicka på **Konfigurera** under **SP initiera SAML 2,0** -avsnittet.

    ![Skärm bild som visar avsnittet "S P inlett S A M L 2,0" med knappen "Ställ in" valt.](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. Utför följande steg i popup-fönstret **SP-initierad SAML 2,0** :

    ![Grovo-konfiguration](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    b. I text rutan för **enkel inloggnings tjänstens slut punkt** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Välj **tjänst slut punkts bindning för enkel inloggning** som `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect` .
    
    d. Öppna det nedladdade **base64-kodade certifikatet** från Azure Portal i anteckningar, klistra in det i text rutan för den **offentliga nyckeln** .

    e. Klicka på **Nästa**.

### <a name="create-grovo-test-user"></a>Skapa Grovo test användare

I det här avsnittet skapas en användare som heter B. Simon i Grovo. Grovo stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Grovo skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [Grovo support team](https://www.grovo.com/contact-us)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Grovo på åtkomst panelen, bör du loggas in automatiskt på den Grovo som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Grovo med Azure AD](https://aad.portal.azure.com/)