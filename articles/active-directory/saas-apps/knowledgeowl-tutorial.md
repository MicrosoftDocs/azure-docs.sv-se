---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med KnowledgeOwl | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowledgeOwl.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: a35fb30ca19e08c68c99c9c9524231706b066e11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549899"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med KnowledgeOwl

I den här självstudien får du lära dig hur du integrerar KnowledgeOwl med Azure Active Directory (Azure AD). När du integrerar KnowledgeOwl med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till KnowledgeOwl.
* Gör det möjligt för användarna att logga in automatiskt till KnowledgeOwl med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* KnowledgeOwl för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* KnowledgeOwl stöder **SP-och IDP** -INITIERAd SSO
* KnowledgeOwl stöder **just-in-Time** User-etablering

## <a name="adding-knowledgeowl-from-the-gallery"></a>Lägga till KnowledgeOwl från galleriet

Om du vill konfigurera integreringen av KnowledgeOwl i Azure AD måste du lägga till KnowledgeOwl från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **KnowledgeOwl** i sökrutan.
1. Välj **KnowledgeOwl** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Konfigurera och testa enkel inloggning med Azure AD för KnowledgeOwl

Konfigurera och testa Azure AD SSO med KnowledgeOwl med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i KnowledgeOwl.

Om du vill konfigurera och testa Azure AD SSO med KnowledgeOwl, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera KNOWLEDGEOWL SSO](#configure-knowledgeowl-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa KnowledgeOwl test User](#create-knowledgeowl-test-user)** -om du vill ha en motsvarighet till B. Simon i KnowledgeOwl som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **KnowledgeOwl** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Du måste uppdatera värdet från den faktiska identifieraren, svars-URL: en och inloggnings-URL: en som beskrivs senare i självstudien.

1. KnowledgeOwl-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig KnowledgeOwl-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut | Namnområde |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera KnowledgeOwl** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till KnowledgeOwl.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **KnowledgeOwl**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-knowledgeowl-sso"></a>Konfigurera KnowledgeOwl SSO

1. Logga in på din KnowledgeOwl-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **Inställningar** och välj sedan **säkerhet**.

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure1.png)

1. Bläddra till **SAML SSO-integrering** och utför följande steg:

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure2.png)

    a. Välj **Aktivera SAML SSO**.

    b. Kopiera **ID-värdet för SP-entiteten** och klistra in det i **identifieraren (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    c. Kopiera URL-värdet för **SP-inloggning** och klistra in det i text rutorna för **inloggnings-URL och svars-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    d. I text rutan **IDP entityId** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    e. I text rutan för **inloggnings-URL för IDP** klistrar du in värdet för **inloggnings-URL** , som du har kopierat från Azure Portal.

    f. I text rutan **utloggnings-URL för IDP** klistrar du in URL-värdet för **utloggning** , som du har kopierat från Azure Portal

    ex. Överför det hämtade certifikatet från Azure Portal genom att klicka på **IDP-certifikatet för uppladdning**.

    h. Klicka på **MAPPA SAML-attribut** för att mappa attribut och utför följande steg:

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure3.png)

    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` i text rutan **SSO-ID**
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i text rutan **användar namn/e-post** .
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` i text rutan för det **första namnet** .
    * Ange `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` i text rutan **efter namn** .
    * Klicka på **Spara**

    i. Klicka på **Spara** längst ned på sidan.

    ![KnowledgeOwl-konfiguration](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>Skapa KnowledgeOwl test användare

I det här avsnittet skapas en användare som heter B. Simon i KnowledgeOwl. KnowledgeOwl stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i KnowledgeOwl skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [KnowledgeOwl support team](mailto:support@knowledgeowl.com)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen KnowledgeOwl på åtkomst panelen, bör du loggas in automatiskt på den KnowledgeOwl som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova KnowledgeOwl med Azure AD](https://aad.portal.azure.com/)