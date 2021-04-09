---
title: 'Självstudie: Azure Active Directory integration med TOPdesk-Secure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk – Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654314"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Självstudie: Azure Active Directory integrering med TOPdesk-Secure

I den här självstudien får du lära dig hur du integrerar TOPdesk-Secure med Azure Active Directory (Azure AD). När du integrerar TOPdesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TOPdesk-Secure.
* Gör det möjligt för användarna att logga in automatiskt för TOPdesk (enkel inloggning) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
 * En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En TOPdesk-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TOPdesk – Secure stöder **IDP**-initierad enkel inloggning

## <a name="add-topdesk---secure-from-the-gallery"></a>Lägg till TOPdesk – säkert från galleriet

För att konfigurera integreringen av TOPdesk – Secure till Azure AD behöver du lägga till TOPdesk – Secure från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **TOPdesk-Secure** i sökrutan.
1. Välj **TOPdesk – skydda** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Konfigurera och testa Azure AD SSO för TOPdesk – Secure

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med TOPdesk – Secure baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TOPdesk – Secure upprättas.

Om du vill konfigurera och testa enkel inloggning med TOPdesk i Azure AD måste du utföra följande steg:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    2. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera TOPdesk – säker SSO](#configure-topdesk---secure-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa TOPdesk – Secure-testanvändare](#create-topdesk---secure-test-user)** – för att ha en motsvarighet för Britta Simon i TOPdesk – Secure som är länkad till en Azure AD-representation av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med TOPdesk – Secure:

1. I Azure Portal på sidan **TOPdesk-säker** program integrering väljer du **enkel inloggning**.

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

3. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för att öppna dialog rutan **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net`

    b. I rutan **identifiera URL** fyller du i TOPdesk metadata-URL som du kan hämta från TOPdesk-konfigurationen. Den bör använda följande mönster: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Hämta dessa värden genom att kontakta [supportteamet för TOPdesk – Secure-klienten](https://www.topdesk.com/us/support/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera TOPdesk – Secure** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TOPdesk-Secure.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **TOPdesk-Secure**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen &quot;standard åtkomst&quot; vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name=&quot;configure-topdesk---secure-sso&quot;></a>Konfigurera TOPdesk – säker SSO

1. Logga in på din **TOPdesk – Secure**-företagsplats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.

    ![Inställningar](./media/topdesk-secure-tutorial/ic790598.png &quot;Inställningar")

3. Klicka på **Inloggningsinställningar**.

    ![Inloggnings inställningar](./media/topdesk-secure-tutorial/ic790599.png "Inloggnings inställningar")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.

    ![Allmänt](./media/topdesk-secure-tutorial/ic790600.png "Allmänt")

5. I avsnittet **Skydda** i avsnittet för konfiguration av **SAML-inloggning** utför du följande steg:

    ![Tekniska inställningar](./media/topdesk-secure-tutorial/ic790855.png "Tekniska inställningar")

    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.

    b. Öppna metadatafilen och leta upp noden **AssertionConsumerService**.

    ![Intygs konsument tjänst](./media/topdesk-secure-tutorial/ic790856.png "Intygs konsument tjänst")

    c. Kopiera värdet **AssertionConsumerService**, klistra in det i textrutan flr svars-URL i avsnittet om **TOPdesk – Secure-domän och URL:er**.

6. Skapa en certifikatfil genom att utföra följande steg:

    ![Certifikat](./media/topdesk-secure-tutorial/ic790606.png "Certifikat")

    a. Öppna den nedladdade metadatafilen från Azure-portalen.

    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.

    c. Kopiera värdet för noden **X509Certificate**.

    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.

    ![Lägg till](./media/topdesk-secure-tutorial/ic790607.png "Lägg till")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:

    ![Konfigurations assistent för SAML](./media/topdesk-secure-tutorial/ic790608.png "Konfigurations assistent för SAML")

    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. För **Private key(RSA, PKCS8, DER)** kan du ladda upp din egen privata nyckel eller kontakta [supportteamet för TOPdesk – Secure-klienten](https://www.topdesk.com/us/support) för att få den privata nyckeln.

    d. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    e. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    ex. Klicka på **Spara**.

### <a name="create-topdesk---secure-test-user"></a>Skapa TOPdesk – Secure-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på TOPdesk – Secure måste de etableras i TOPdesk – Secure.  
När det gäller TOPdesk – Secure är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din **TOPdesk – Secure**-företagsplats som administratör.

2. På menyn längst upp klickar du på **TOPdesk \> New (Ny) \> Support Files (Supportfiler) \> Operator (Operatör)**.

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. I dialogrutan **New Operator** (Ny operatör) utför du följande steg:

    ![Ny operator](./media/topdesk-secure-tutorial/ic790611.png "Ny operator")

    a. Klicka på fliken **Allmänt**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. Välj en **plats** för kontot i avsnittet **Plats**.

    d. I textrutan **Inloggningsnamn** i avsnittet **TOPdesk Login** (TOPdesk-inloggning) skriver du ett inloggningsnamn för användaren.

    e. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra TOPdesk-verktyg eller API: er för skapande av användar konton som tillhandahålls av TOPdesk-Secure för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till TOPdesk-säker inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till TOPdesk-säker inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på ikonen TOPdesk-Secure i Mina appar, bör du loggas in automatiskt till den TOPdesk-säkra som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat TOPdesk kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).