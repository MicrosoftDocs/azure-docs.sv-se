---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Check Point CloudGuard Dome9 båge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e5bb8a32cfd73e67141a25531594e8a3b6f793c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732235"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Check Point CloudGuard Dome9 båge

I den här självstudien får du lära dig hur du integrerar Check Point CloudGuard Dome9 Arc med Azure Active Directory (Azure AD). När du integrerar Check Point CloudGuard Dome9 båge med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Check Point CloudGuard Dome9 båge.
* Låt användarna vara automatiskt inloggade för att checka in Point CloudGuard Dome9-bågen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Check Point CloudGuard Dome9 Arc Single Sign-on (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Check Point CloudGuard Dome9-båge stöder **SP-och IDP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Lägga till Check Point CloudGuard Dome9 båge från galleriet

Om du vill konfigurera integreringen av Check Point CloudGuard Dome9-bågen i Azure AD måste du lägga till Check Point CloudGuard Dome9 båge från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Check Point CloudGuard Dome9 Arc** i sökrutan.
1. Välj **Check Point CloudGuard Dome9 båge** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-check-point-cloudguard-dome9-arc"></a>Konfigurera och testa Azure AD SSO för Check Point CloudGuard Dome9 båge

Konfigurera och testa Azure AD SSO med Check Point CloudGuard Dome9 båge med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Check Point CloudGuard Dome9 Arc.

Utför följande steg för att konfigurera och testa Azure AD SSO med Check Point CloudGuard Dome9 båge:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Check Point CloudGuard Dome9 båge SSO](#configure-check-point-cloudguard-dome9-arc-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Check Point CloudGuard Dome9 Arc test User](#create-check-point-cloudguard-dome9-arc-test-user)** -för att få en motsvarighet till B. Simon i Check Point CloudGuard Dome9-båge som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Check Point CloudGuard Dome9 Arc** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Du får `<company name>` värdet från avsnittet **Konfigurera Check Point CloudGuard DOME9 Arc SSO** , som beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Check Point CloudGuard Dome9 Arc-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig CloudGuard Dome9 ARC-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.
    
    | Name |  Källattribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta hur du skapar roller i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Check Point CloudGuard Dome9 Arc** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Check Point CloudGuard Dome9 båge.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Check Point CloudGuard Dome9 båge**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Konfigurera Check Point CloudGuard Dome9 båge SSO

1. Om du vill automatisera konfigurationen inom Check Point CloudGuard Dome9 Arc måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **installations kontroll punkt CloudGuard Dome9 båge** dirigerar dig till Check Point CloudGuard Dome9 båg Application. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Check Point CloudGuard Dome9 Arc. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill installera Check Point CloudGuard Dome9 båge manuellt, öppnar du ett nytt webbläsarfönster och loggar in på Check Point CloudGuard Dome9 Arc Company site som administratör och utför följande steg:

2. Klicka på **profilinställningarna** uppe i högra hörnet och klicka sedan på **Account Settings** (Kontoinställningar). 

    ![Skärm bild som visar menyn "profil inställningar" där "konto inställningar" har valts.](./media/dome9arc-tutorial/configure1.png)

3. Gå till **SSO** och klicka sedan på **ENABLE**.

    ![Skärm bild som visar fliken "S S O" och "Aktivera" vald.](./media/dome9arc-tutorial/configure2.png)

4. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Kontroll punkt CloudGuard Dome9 Arc Configuration](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i textrutan **Account ID** (Konto-ID). Det här värdet ska användas i **svars** -och **inloggnings** -URL: en som nämns i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. I textrutan **Idp endpoint url** (url till Idp-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).

    e. Klicka på **Spara**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Skapa Check Point CloudGuard Dome9 Arc test User

Om du vill att Azure AD-användare ska kunna logga in till Check Point CloudGuard Dome9 båge måste de tillhandahållas i program. Check Point CloudGuard Dome9-bågen stöder just-in-Time-etablering men för att det ska fungera korrekt måste användaren välja en viss **roll** och tilldela den samma för användaren.

   >[!Note]
   >För att skapa **roller** och andra uppgifter kontaktar du [Check Point CloudGuard Dome9 Arc client support team](mailto:Dome9@checkpoint.com).

**Utför följande steg om du vill etablera ett användarkonto manuellt:**

1. Logga in på Check Point CloudGuard Dome9 Arc Company site som administratör.

2. Klicka på **Users & Roles** (Användare och roller) och sedan på **Users** (Användare).

    ![Skärm bild som visar "användare & roller" med åtgärden "användare" vald.](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Skärm bild som visar "användare & roller" med knappen "Lägg till användare" vald.](./media/dome9arc-tutorial/user2.png)

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren, till exempel B.Simon@contoso.com.

    b. I text rutan **förnamn** skriver du förnamn för användaren som B.

    c. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    d. Välj **På** för **användaren**.

    e. Klicka på **skapa**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Check Point CloudGuard Dome9 Arc Sign on URL där du kan starta inloggnings flödet.  

* Gå till Check Point CloudGuard Dome9 Arc Sign-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till Check Point-CloudGuard Dome9-bågen som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på ikonen för kontroll punktens CloudGuard Dome9 båge i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Check Point CloudGuard Dome9 båge som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Check Point CloudGuard Dome9 båge kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
