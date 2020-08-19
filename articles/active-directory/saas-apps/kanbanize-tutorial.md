---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Kanbanize | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kanbanize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 686a186650bfe4066533b0f086c080b5ffb14eb6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546843"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Kanbanize

I den här självstudien får du lära dig hur du integrerar Kanbanize med Azure Active Directory (Azure AD). När du integrerar Kanbanize med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Kanbanize.
* Gör det möjligt för användarna att logga in automatiskt till Kanbanize med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Kanbanize för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Kanbanize stöder **SP-och IDP** -INITIERAd SSO
* Kanbanize stöder **just-in-Time** User-etablering

## <a name="adding-kanbanize-from-the-gallery"></a>Lägga till Kanbanize från galleriet

Om du vill konfigurera integreringen av Kanbanize i Azure AD måste du lägga till Kanbanize från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Kanbanize** i sökrutan.
1. Välj **Kanbanize** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Konfigurera och testa enkel inloggning med Azure AD för Kanbanize

Konfigurera och testa Azure AD SSO med Kanbanize med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Kanbanize.

Om du vill konfigurera och testa Azure AD SSO med Kanbanize, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera KANBANIZE SSO](#configure-kanbanize-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Kanbanize test User](#create-kanbanize-test-user)** -om du vill ha en motsvarighet till B. Simon i Kanbanize som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Kanbanize** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

     a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.kanbanize.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Klicka på **Ange ytterligare URL:er**.

    d. I text rutan för **relä tillstånd** anger du en URL: `/ctrl_login/saml_login`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Kanbanize client support team](mailto:support@ms.kanbanize.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Kanbanize-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där nameidentifier mappas med **user.userprincipalname**. Kanbanize-programmet förväntar sig att NameIdentifier mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Kanbanize** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kanbanize.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Kanbanize**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-kanbanize-sso"></a>Konfigurera Kanbanize SSO

1. Om du vill automatisera konfigurationen i Kanbanize måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Kanbanize** för att dirigera dig till Kanbanize-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Kanbanize. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Kanbanize manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Kanbanize-företags webbplats som administratör och utför följande steg:

4. Gå till överst till höger på sidan, klicka på **Inställningar** logo typ.

    ![Kanbanize-inställningar](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. På sidan administrations panel på den vänstra sidan av menyn klickar du på **integration** och aktiverar sedan **enkel inloggning**.

    ![Kanbanize-integreringar](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Under avsnittet integrations klickar du på **Konfigurera** för att öppna **integrations sidan för enkel inloggning** .

    ![Kanbanize-konfiguration](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Utför följande steg på **integrations sidan för enkel inloggning** under **konfigurationer**:

    ![Kanbanize-integreringar](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. I text rutan för **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    b. I text rutan **IDP-inloggnings slut punkt** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. I text rutan **IDP-utloggnings slut punkt** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    d. Ange det här värdet i rutan **attributnamn för e-post** . `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Ange det här värdet i text rutan **attribut namn för förnamn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Ange det här värdet i text rutan **attribut namn för efter namn**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Du kan hämta dessa värden genom att kombinera namn rymder och namn värden för respektive attribut från avsnittet användarattribut i Azure Portal.

    ex. I anteckningar öppnar du det bas-64-kodade certifikatet som du laddade ned från Azure Portal, kopierar dess innehåll (utan start-och slut markeringar) och klistrar in det i rutan **IDP X. 509** .

    h. Markera **Aktivera inloggning med både SSO och Kanbanize**.

    i. Klicka på **Spara inställningar**.

### <a name="create-kanbanize-test-user"></a>Skapa Kanbanize test användare

I det här avsnittet skapas en användare som heter B. Simon i Kanbanize. Kanbanize stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Kanbanize skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kontaktar du [Kanbanize client support team](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kanbanize på åtkomst panelen, bör du loggas in automatiskt på den Kanbanize som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Kanbanize med Azure AD](https://aad.portal.azure.com/)

