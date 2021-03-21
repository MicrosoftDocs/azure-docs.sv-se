---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshworks | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651730"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshworks

I den här självstudien får du lära dig hur du integrerar Freshworks med Azure Active Directory (Azure AD). När du integrerar Freshworks med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freshworks.
* Gör det möjligt för användarna att logga in automatiskt till Freshworks med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Freshworks för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Freshworks stöder **SP-och IDP** -INITIERAd SSO

## <a name="add-freshworks-from-the-gallery"></a>Lägg till Freshworks från galleriet

Om du vill konfigurera integreringen av Freshworks i Azure AD måste du lägga till Freshworks från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Freshworks** i sökrutan.
1. Välj **Freshworks** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Konfigurera och testa Azure AD SSO för Freshworks

Konfigurera och testa Azure AD SSO med Freshworks med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Freshworks.

Utför följande steg för att konfigurera och testa Azure AD SSO med Freshworks:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FRESHWORKS SSO](#configure-freshworks-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Freshworks test User](#create-freshworks-test-user)** -om du vill ha en motsvarighet till B. Simon i Freshworks som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Freshworks** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.freshworks.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL. Kontakta [Freshworks client support team](mailto:support@freshworks.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Om du vill ändra **signerings** alternativen enligt ditt krav klickar du på knappen **Redigera** för att öppna dialog rutan certifikat för **SAML-signering** .

     ![image](common/edit-certificate.png)

     ![Skärm bild som visar dialog rutan "S A M L signerings certifikat" med knappen "redigera" markerad.](./media/freshworks-tutorial/response.png)

    a. Välj **signera SAML-svar** som **signerings alternativ**.

    b. Klicka på **Spara**.

1. I avsnittet **Konfigurera Freshworks** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Freshworks.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Freshworks**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-freshworks-sso"></a>Konfigurera Freshworks SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Freshworks-företags webbplats som administratör och utför följande steg:

2. På menyn till vänster på menyn klickar du på **säkerhets** ikon och kontrollerar sedan alternativet **enkel inloggning** och väljer **SAML SSO** under **autentiseringsmetoder**.

    ![Skärm bild som visar avsnittet "metoder för säkerhets autentisering" med alternativet "enkel inloggning" aktiverat och "S A M L s O" vald.](./media/freshworks-tutorial/configure01.png)

3. I avsnittet **enkel inloggning** utför du följande steg:

    ![Freshworks-konfiguration](./media/freshworks-tutorial/configure02.png)

    a. Klicka på **Kopiera** för att kopiera **enhets-ID: t för service providern (SP)** för din instans och klistra in den i **ID (enhets-ID)** text ruta i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    b. I det **entitets-ID som tillhandahålls av** text rutan IDP klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **URL för SAML SSO** klistrar du in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal.

    d. Öppna det Base64-kodade certifikatet i anteckningar, kopiera dess innehåll och klistra in det i text rutan **säkerhetscertifikat** .

    e. Klicka på **Spara**.

### <a name="create-freshworks-test-user"></a>Skapa Freshworks test användare

I det här avsnittet skapar du en användare som heter B. Simon i Freshworks. Arbeta med [Freshworks-klientens support team](mailto:support@freshworks.com) för att lägga till användare i Freshworks-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Freshworks-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Freshworks-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Freshworks som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Freshworks i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Freshworks som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Freshworks kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).