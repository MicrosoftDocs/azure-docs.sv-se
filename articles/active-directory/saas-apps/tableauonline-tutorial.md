---
title: 'Självstudie: Azure Active Directory integrering med Tableau online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5318570ed77e3352f37c2306ecd003195992d010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Tableau online

I den här självstudien får du lära dig hur du integrerar Tableau online med Azure Active Directory (Azure AD). När du integrerar Tableau online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Tableau online.
* Gör det möjligt för användarna att logga in automatiskt till Tableau online med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Tableau online, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Tableau online stöder **SP** -INITIERAd SSO

## <a name="adding-tableau-online-from-the-gallery"></a>Lägga till Tableau online från galleriet

Om du vill konfigurera integreringen av Tableau online i Azure AD måste du lägga till Tableau online från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Tableau online** i sökrutan.
1. Välj **Tableau online** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Konfigurera och testa Azure AD SSO för Tableau online

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Tableau online baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Tableau online.

Utför följande steg för att konfigurera och testa Azure AD SSO med Tableau online:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Tableau online SSO](#configure-tableau-online-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Tableau online-test](#create-tableau-online-test-user)** för att få en motsvarighet till B. Simon i Tableau online som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Tableau online** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Du får `<entityid>` värdet från avsnittet **Konfigurera Tableau online** i den här självstudien. Värdet för entitets-ID är ett värde för **Azure AD-identifieraren** i avsnittet **Konfigurera Tableau online** .

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Tableau online** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Tableau online.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Tableau online**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-tableau-online-sso"></a>Konfigurera Tableau online SSO

1. Om du vill automatisera konfigurationen i Tableau online måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **Konfigurera Tableau online** för att dirigera dig till Tableau online-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Tableau online. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Tableau online manuellt i ett annat webbläsarfönster loggar du in på din Tableau online-företags webbplats som administratör.

1. Gå till **Inställningar** och sedan **autentisering**.

    ![Skärm bild som visar den autentisering som valts från menyn Inställningar.](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. För att aktivera SAML under avsnittet **autentiseringsmetoder** . Markera **aktivera ytterligare en autentiseringsmetod** och markera sedan **SAML** -kryss rutan.

    ![Skärm bild som visar avsnittet autentiseringsmetoder där du kan välja värden.](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Rulla nedåt uppåt för att **Importera metadatafilen till avsnittet Tableau online** .  Klicka på Bläddra och importera metadatafilen som du har laddat ned från Azure AD. Klicka sedan på **Använd**.

   ![Skärm bild som visar avsnittet där du kan importera metadatafilen.](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. I avsnittet **matchnings intyg** infogar du motsvarande namn på identitets leverantören för **e-postadress**, **förnamn** och **efter namn**. För att hämta den här informationen från Azure AD: 
  
    a. Gå till sidan **Tableau online** application integration i Azure Portal.

    b. I avsnittet **användarattribut &-anspråk** klickar du på redigerings ikonen.

   ![Skärm bild som visar avsnittet användarattribut &-anspråk där du kan välja redigerings ikonen.](./media/tableauonline-tutorial/attributesection.png)

    c. Kopiera namn rymds värdet för dessa attribut: givenName, e-post och efter namn med hjälp av följande steg:

   ![Skärm bild som visar attributen givenName, efter namn och EmailAddress.](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klicka på **User. givenName** -värde

    e. Kopiera värdet från text rutan **namnrymd** .

    ![Skärm bild som visar avsnittet hantera användar anspråk där du kan ange namn området.](./media/tableauonline-tutorial/attributesection2.png)

    f. Om du vill kopiera namn rymds värden för e-postmeddelandet och förnamnet upprepar du ovanstående steg.

    ex. Växla till Tableau online-programmet och ange sedan avsnittet **användarattribut &-anspråk** enligt följande:

    * E-post: **mail** eller **userPrincipalName**

    * Förnamn: **givenName**

    * Efter namn: efter **namn**

    ![Skärm bild som visar avsnittet matcha attribut där du kan ange värden.](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Skapa Tableau online-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Tableau online.

1. På **Tableau online** klickar du på **Inställningar** och sedan på **autentisering** . Rulla ned till avsnittet **Hantera användare** . Klicka på **Lägg till användare** och sedan på **Ange e-postadresser**.
  
    ![Skärm bild som visar avsnittet hantera användare där du kan välja Lägg till användare.](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Välj **Lägg till användare för (SAML) autentisering**. I text rutan **Ange e-postadresser** lägger du till Britta. Simon \@ contoso.com
  
    ![Skärm bild som visar sidan Lägg till användare där du kan ange en e-postadress.](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Tableau online-inloggnings-URL där du kan starta inloggnings flödet.

* Gå till Tableau online-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Tableau online i Mina appar omdirigeras detta till Tableau online-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Tableau online kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)