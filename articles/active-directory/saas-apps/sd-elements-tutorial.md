---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SD-element | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SD-element.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med SD-element

I den här självstudien får du lära dig att integrera SD-element med Azure Active Directory (Azure AD). När du integrerar SD-element med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SD-element.
* Gör det möjligt för användarna att logga in automatiskt till SD-element med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SD-element enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SD-element stöder **IDP** -INITIERAd SSO

## <a name="adding-sd-elements-from-the-gallery"></a>Lägga till SD-element från galleriet

Om du vill konfigurera integrering av SD-element i Azure AD måste du lägga till SD-element från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SD-element** i sökrutan.
1. Välj **SD-element** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Konfigurera och testa enkel inloggning med Azure AD för SD-element

Konfigurera och testa Azure AD SSO med SD-element med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SD-element.

Om du vill konfigurera och testa Azure AD SSO med SD-element slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SD-element SSO](#configure-sd-elements-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa SD-element testa användare](#create-sd-elements-test-user)** -om du vill ha en motsvarighet till B. Simon i SD-element som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **SD-elementens** program integration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [support teamet för SD-element](mailto:support@sdelements.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I programmet för SD-element förväntas SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig program med SD-element att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | --- | --- |
    | e-post |user.mail |
    | förnamn |user.givenname |
    | efternamn |user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera SD-element** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SD-element.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SD-element**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sd-elements-sso"></a>Konfigurera SD-element SSO

1. Om du vill få enkel inloggning aktive rad kontaktar du [support teamet för SD-element](mailto:support@sdelements.com) och ger dem den hämtade certifikat filen.

1. Logga in på dina SD-element i ett annat webbläsarfönster som administratör.

1. I menyn högst upp klickar du på **system** och sedan **på enkel inloggning**.

    ![Skärm bild som visar "system" markerat och "enkel inloggning" valt i list rutan.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. I dialog rutan **Inställningar för enskild Sign-On** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Som **SSO-typ** väljer du **SAML**.

    b. I text rutan för **entitets-ID för identitetsprovider** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. Klistra in värdet för **inloggnings-URL**: en som du har kopierat från Azure Portal i text rutan **identitets leverantörens enskild Sign-On** .

    d. Klicka på **Spara**.

### <a name="create-sd-elements-test-user"></a>Skapa SD-element testa användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i SD-element. När det gäller SD-element skapar SD-element användare en manuell uppgift.

**Utför följande steg för att skapa B. Simon i SD-element:**

1. Logga in på dina SD-element på företags platsen som administratör i ett webbläsarfönster.

1. Klicka på **användar hantering** på menyn längst upp och sedan på **användare**.

    ![Skärm bild som visar "användare" som marker ATS från List rutan "användar hantering".](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klicka på **Lägg till ny användare**.

    ![Skärm bild som visar knappen "Lägg till ny användare" vald.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. I dialog rutan **Lägg till ny användare** utför du följande steg:

    ![Skapa ett SD-element test användare](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. I text rutan **e-post** anger du e-postmeddelandet för användaren **b.simon@contoso.com** .

    b. I text rutan för det **första namnet** anger du det första namnet på användaren, t **. ex. B.**..

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    d. Som **roll** väljer du **användare**.

    e. Klicka på **Skapa användare**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SD-element på åtkomst panelen, bör du loggas in automatiskt på de SD-element som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Testa SD-element med Azure AD](https://aad.portal.azure.com/)