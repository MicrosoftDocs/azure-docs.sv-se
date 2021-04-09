---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AskYourTeam | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b239811e9fe49f420b5e9d15afafdcf26832dbf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735412"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AskYourTeam

I den här självstudien får du lära dig hur du integrerar AskYourTeam med Azure Active Directory (Azure AD). När du integrerar AskYourTeam med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AskYourTeam.
* Gör det möjligt för användarna att logga in automatiskt till AskYourTeam med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* AskYourTeam för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* AskYourTeam stöder **SP-och IDP** -initierad SSO.

## <a name="adding-askyourteam-from-the-gallery"></a>Lägga till AskYourTeam från galleriet

Om du vill konfigurera integreringen av AskYourTeam i Azure AD måste du lägga till AskYourTeam från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **AskYourTeam** i sökrutan.
1. Välj **AskYourTeam** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Konfigurera och testa Azure AD SSO för AskYourTeam

Konfigurera och testa Azure AD SSO med AskYourTeam med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AskYourTeam.

Utför följande steg för att konfigurera och testa Azure AD SSO med AskYourTeam:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ASKYOURTEAM SSO](#configure-askyourteam-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa AskYourTeam test User](#create-askyourteam-test-user)** -om du vill ha en motsvarighet till B. Simon i AskYourTeam som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **AskYourTeam** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska svars-URL: en och Sign-On URL-värden som beskrivs senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera AskYourTeam** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till AskYourTeam.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **AskYourTeam**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-askyourteam-sso"></a>Konfigurera AskYourTeam SSO

1. Om du vill automatisera konfigurationen i AskYourTeam måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera AskYourTeam** för att dirigera dig till AskYourTeam-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på AskYourTeam. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera AskYourTeam manuellt i ett annat webbläsarfönster loggar du in på AskYourTeam Company-platsen som administratör.

1. Klicka på **min organisation**.

    ![Skärm bild som visar länken min organisation.](./media/askyourteam-tutorial/user1.png)

1. Klicka på **integreringar**.

    ![Skärm bild som visar länken integreringar.](./media/askyourteam-tutorial/configure1.png)

1. Klicka på **Redigera inställningar**.

    ![Skärm bild som visar det enskilda Sign-On meddelandet med knappen Redigera inställningar.](./media/askyourteam-tutorial/configure2.png)

1. På sidan **Redigera en enskild Sign-On-integration** utför du följande steg: 

    ![Skärm bild som visar redigera enkel Sign-On-integrering där du kan ange värden för det här steget.](./media/askyourteam-tutorial/configure3.png)

    a. I text rutan för **SAML-Sign-On tjänstens URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    b. I text rutan **SAML-entitets-ID** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    c. I text rutan för utloggnings **-URL** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **SAML-signerings certifikat – base64** .

    > [!NOTE]
    > Alternativt kan du också ladda upp **XML-** filen med federationsmetadata genom att klicka på alternativet **Välj fil** .

    e. Kopiera **svars-URL (intyg om mottagar tjänstens URL)** värde, klistra in det här värdet i text rutan **SVARs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    f. Kopiera **inloggnings-URL** -värde, klistra in det här värdet i text rutan **INLOGGNINGs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ex. Klicka på **Spara**.

### <a name="create-askyourteam-test-user"></a>Skapa AskYourTeam test användare

1. Logga in på AskYourTeam-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **min organisation**.

    ![Skärm bild som visar länken min organisation där du startar den här uppgiften.](./media/askyourteam-tutorial/user1.png)

1. Klicka på **användare** och välj **ny användare**.

    ![Skärm bild som visar användarnas länkar med ny användare.](./media/askyourteam-tutorial/user2.png)

1. Utför följande steg i avsnittet **ny användare** :

    ![Skärm bild som visar ett nytt användar avsnitt där du anger användar information.](./media/askyourteam-tutorial/user3.png)

    1. I text rutan för det **första namnet** anger du användarens förnamn.

    1. I text rutan **efter namn** anger du användarens efter namn.

    1. I text rutan för **e-post** anger du EmailAddress för den användare som B.Simon@contoso.com .

    1. Välj **rollen** för användaren enligt organisationens krav.

    1. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till AskYourTeam-inloggnings-URL där du kan starta inloggnings flödet.

* Gå till AskYourTeam-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den AskYourTeam som du har konfigurerat SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen AskYourTeam i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på AskYourTeam som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AskYourTeam kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).