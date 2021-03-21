---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med monday.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och monday.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 06f4ddff9c897858eec27f97f8e45a3d656e951d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650795"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med monday.com

I den här självstudien får du lära dig hur du integrerar monday.com med Azure Active Directory (Azure AD). När du integrerar monday.com med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till monday.com.
* Gör det möjligt för användarna att logga in automatiskt till monday.com med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* monday.com för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* monday.com stöder **SP-och IDP** -INITIERAd SSO
* monday.com stöder **just-in-Time** User-etablering

## <a name="add-mondaycom-from-the-gallery"></a>Lägg till monday.com från galleriet

Om du vill konfigurera integreringen av monday.com i Azure AD måste du lägga till monday.com från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Monday.com** i sökrutan.
1. Välj **Monday.com** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-mondaycom"></a>Konfigurera och testa Azure AD SSO för monday.com

Konfigurera och testa Azure AD SSO med monday.com med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i monday.com.

Utför följande steg för att konfigurera och testa Azure AD SSO med monday.com:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Monday.com SSO](#configure-mondaycom-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Monday.com test User](#create-mondaycom-test-user)** -om du vill ha en motsvarighet till B. Simon i Monday.com som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Monday.com** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en **metadata-fil för Service Provider** och vill konfigurera i **IDP** initierat läge, utför du följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration.

    > [!Note]
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt fyller du i värdena manuellt. **Identifieraren** och **SVARs-URL** : en är samma och värdet är i följande mönster:`https://<your-domain>.monday.com/saml/saml_callback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och Sign-On-URL. Kontakta [Monday.com client support team](https://monday.com/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. monday.com-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild som visar användarattribut & anspråk med standardvärden som givenName User. givenName och EmailAddress User. mail.](common/default-attributes.png)

1. Utöver ovan förväntar sig monday.com-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut |
    |--|--|
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **konfigurera Monday.com** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till monday.com.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Monday.com**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-mondaycom-sso"></a>Konfigurera monday.com SSO

1. Om du vill automatisera konfigurationen i monday.com måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren klickar du på **konfigurera Monday.com** som leder dig till Monday.com-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på monday.com. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera monday.com manuellt öppnar du ett nytt webbläsarfönster och loggar in på monday.com som administratör och utför följande steg:

1. Gå till **profilen** i det övre högra hörnet av sidan och klicka på **administratör**.

    ![Skärm bild som visar den valda administratörs profilen.](./media/mondaycom-tutorial/configuration-1.png)

1. Välj **säkerhet** och se till att klicka på **Öppna** bredvid SAML.

    ![Skärm bild som visar fliken säkerhet med alternativet att öppna bredvid SAML.](./media/mondaycom-tutorial/configuration-2.png)

1. Fyll i informationen nedan från din IDP.

    ![Skärm bild visar SAML-providern där du kan ange information från I D P.](./media/mondaycom-tutorial/configuration-3.png)

    > [!NOTE]
    > Mer information finns i [den här](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) artikeln

### <a name="create-mondaycom-test-user"></a>Skapa monday.com test användare

I det här avsnittet skapas en användare som heter B. Simon i monday.com. monday.com stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i monday.com skapas en ny när du försöker få åtkomst till monday.com.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till monday.com-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till monday.com-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Monday.com som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen monday.com i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på monday.com som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat monday.com kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).