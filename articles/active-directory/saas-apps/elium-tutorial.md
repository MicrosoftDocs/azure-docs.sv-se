---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Elium | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Elium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 7f0e9d0c97b9325a30de3cb8c6ce10a3ba8489f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Elium

I den här självstudien får du lära dig hur du integrerar Elium med Azure Active Directory (Azure AD). När du integrerar Elium med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Elium.
* Gör det möjligt för användarna att logga in automatiskt till Elium med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Elium för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Elium stöder **SP- och IDP**-initierad enkel inloggning
* Elium stöder **just-in-time**-användaretablering

## <a name="adding-elium-from-the-gallery"></a>Lägga till Elium från galleriet

För att konfigurera integreringen av Elium i Azure AD måste du lägga till Elium från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Elium** i sökrutan.
1. Välj **Elium** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Konfigurera och testa Azure AD SSO för Elium

Konfigurera och testa Azure AD SSO med Elium med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Elium.

Om du vill konfigurera och testa Azure AD SSO med Elium, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ELIUM SSO](#configure-elium-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Elium test User](#create-elium-test-user)** -om du vill ha en motsvarighet till B. Simon i Elium som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Elium** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du får dessa värden från den **SP-metadatafilen** som kan laddas ner på `https://<platform-domain>.elium.com/login/saml2/metadata`, som beskrivs senare i den här självstudien.

1. Elium-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Elium-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| ----------------|
    | e-post   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Det här är standardanspråk. **Endast e-anspråk krävs**. Även för JIT-etablering krävs endast e-anspråk. Andra anpassade anspråk kan variera från en kundplattform till en annan kundplattform.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Elium** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Elium.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan med program väljer du **Elium**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-elium-sso"></a>Konfigurera Elium SSO

1. Om du vill automatisera konfigurationen i Elium måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Elium** för att dirigera dig till Elium-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Elium. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Elium manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Elium-företags webbplats som administratör och utför följande steg:

1. Klicka på **användar profilen** från det högra övre hörnet och välj sedan **Inställningar**.

    ![Konfigurera enkla Sign-On Elium 01](./media/elium-tutorial/elium-01.png)

1. Välj **säkerhet** under **Avancerat**.

    ![Konfigurera enkla Sign-On Elium 02](./media/elium-tutorial/elium-02.png)

1. Rulla ned till avsnittet **Enkel inloggning (SSO)** och utför följande steg:

    ![Konfigurera Single Sign-On Elium 03](./media/elium-tutorial/elium-03.png)

    a. Kopiera värdet för **Verify that SAML2 authentication works for your account** (Kontrollera att SAML2-autentiseringen fungerar för ditt konto) och klistra in det i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    > [!NOTE]
    > När du har konfigurerat SSO kan du alltid få åtkomst till standardsidan för fjärrinloggning via följande webbadress: `https://<platform_domain>/login/regular/login` 

    b. Markera kryssrutan **Aktivera SAML2-federation**.

    c. Markera kryssrutan **JIT-etablering**.

    d. Öppna **SP-metadata** genom att klicka på knappen **Ladda ned**.

    e. Sök efter **entityID** i **SP-metadatafilen**, kopiera **entityID** värdet och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. 

    ![Konfigurera Single Sign-On Elium 04](./media/elium-tutorial/elium-04.png)

    f. Sök efter **AssertionConsumerService** i **SP-metadatafilen**, kopiera **platsvärdet** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    ![Konfigurera Single Sign-On Elium 05](./media/elium-tutorial/elium-05.png)

    ex. Öppna den nedladdade metadatafilen från Azure-portalen i Anteckningar, kopiera innehållet och klistra in det i textrutan **IdP-metadata**.

    h. Klicka på **Spara**.

### <a name="create-elium-test-user"></a>Skapa Elium-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i Elium. Elium stöder **just-in-time-etablering**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Elium skapas en ny när du försöker komma åt Elium.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [Elium-supportteamet](mailto:support@elium.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 
 
#### <a name="sp-initiated"></a>SP initierad:
 
* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Elium-inloggnings-URL där du kan starta inloggnings flödet.  
 
* Gå till Elium-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.
 
#### <a name="idp-initiated"></a>IDP initierad:
 
* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Elium som du har konfigurerat SSO för. 
 
Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen Elium i åtkomst panelen, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Elium som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Elium kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).