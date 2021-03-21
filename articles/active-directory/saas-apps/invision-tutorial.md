---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med insikt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och insikter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ec35917ca18064d58279d8ed2b3fb1f0e83a88fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736941"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med insikt

I den här självstudien får du lära dig att integrera insikter med Azure Active Directory (Azure AD). När du integrerar insikter med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till insikt.
* Gör det möjligt för användarna att logga in automatiskt till insikter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Insikts aktive rad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Insikten stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-invision-from-the-gallery"></a>Lägga till insikt från galleriet

Om du vill konfigurera integrering av insikter i Azure AD måste du lägga till insikt från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **insikter** i sökrutan.
1. Välj **insikt** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Konfigurera och testa Azure AD SSO för insikter

Konfigurera och testa Azure AD SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i insikten.

Utför följande steg för att konfigurera och testa Azure AD SSO med insikt:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera insikt SSO](#configure-invision-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa insikts test användare](#create-invision-test-user)** – för att få en motsvarighet till B. Simon i insikt som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan Azure Portal går du till sidan för **Insiktens** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.invisionapp.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Få de här värdena genom att kontakta [Insiktens support team](mailto:support@invisionapp.com) . Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera insikt** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till insikt.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **insikter**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-invision-sso"></a>Konfigurera insikt SSO

1. Om du vill automatisera konfigurationen i insikten måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **Konfigurera insikten** för att dirigera dig till insikts programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på insikten. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera insikt manuellt i ett annat webbläsarfönster loggar du in på din insikts företags webbplats som administratör.

1. Klicka på **team** och välj **Inställningar**.

    ![Skärm bild som visar fliken team med valda inställningar.](./media/invision-tutorial/config1.png)

1. Rulla ned till **enkel inloggning** och klicka sedan på **ändra**.

    ![Skärm bild som visar knappen Ändra för enkel inloggning.](./media/invision-tutorial/config3.png)

1. Utför följande steg på sidan **enkel inloggning** :

    ![Skärm bild som visar sidan för enkel inloggning där du anger värden i det här steget.](./media/invision-tutorial/config4.png)

    a. Ändra **KRÄV SSO för varje medlem i < konto namnet >** till **på**.

    b. I text rutan **namn** anger du namnet på till exempel som `azureadsso` .

    c. Ange inloggnings-URL-värdet i text rutan för **inloggnings-URL** : en.

    d. I text rutan för **utloggnings-URL** klistrar du in värdet för **Logga ut** URL, som du har kopierat från Azure Portal.

    e. I text rutan **SAML-certifikat** öppnar du det nedladdade **certifikatet (base64)** i anteckningar, kopierar innehållet och klistrar in det i text rutan för SAML-certifikatet.

    f. I text rutan **namn-ID-format** använder `Unspecified` du för **namn-ID-formatet**.

    ex. Välj **SHA-256** i list rutan för **hash-algoritmen**.

    h. Ange ett namn på **etiketten för SSO-knappen**.

    i. Gör **Tillåt just-in-Time-etablering** på.

    j. Klicka på **Uppdatera**.

### <a name="create-invision-test-user"></a>Skapa insikts test användare

1. Logga in på insiktens webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på **team** och välj **personer**.

    ![Skärm bild som visar fliken team med personer markerade.](./media/invision-tutorial/config2.png)

1. Klicka på **+-ikonen** för att lägga till en ny användare.

    ![Skärm bild som visar +-ikonen för att lägga till en användare.](./media/invision-tutorial/user1.png)

1. Ange användarens e-postadress och klicka på **Nästa**.

    ![Skärm bild som visar dialog rutan Bjud in till där du kan ange adresser.](./media/invision-tutorial/user2.png)

1. Kontrol lera e-postadressen och klicka sedan på **Bjud in**.

    ![Skärm bild som visar dialog rutan inbjudan där du kan välja Bjud in för att fortsätta.](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till en insamlad inloggnings-URL där du kan starta inloggnings flödet.

* Gå till insiktens inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till den insikt som du konfigurerade SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen insikt i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till den insikt som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat insikten kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).