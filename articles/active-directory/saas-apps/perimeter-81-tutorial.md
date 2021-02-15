---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med perimeter 81 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och omkrets 81.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: cd6ba1da92a19a1f73fc67c0165bfb19b3bb77aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363894"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeter-81"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med perimeter 81

I den här självstudien får du lära dig hur du integrerar perimeter 81 med Azure Active Directory (Azure AD). När du integrerar perimeter 81 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till perimeter 81.
* Gör det möjligt för användarna att logga in automatiskt till perimeter 81 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på perimeter 81 enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Perimeter 81 stöder **SP-och IDP** -INITIERAd SSO
* Perimeter 81 stöder **just-in-Time** User-etablering

## <a name="adding-perimeter-81-from-the-gallery"></a>Lägga till perimeter 81 från galleriet

Om du vill konfigurera en integrering av perimeter 81 i Azure AD måste du lägga till perimeter 81 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **perimeter 81** i sökrutan.
1. Välj **Perimeter 81** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-perimeter-81"></a>Konfigurera och testa Azure AD SSO för perimeter 81

Konfigurera och testa Azure AD SSO med perimeter 81 med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i perimeter 81.

Utför följande steg för att konfigurera och testa Azure AD SSO med perimeter 81:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Perimeter 81 SSO](#configure-perimeter-81-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa perimeter 81-test användare](#create-perimeter-81-test-user)** – om du vill ha en motsvarighet till B. Simon i perimeter 81 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Perimeter 81** program integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du ett värde med hjälp av följande mönster: `urn:auth0:perimeter81:<SUBDOMAIN>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://auth.perimeter81.com/login/callback?connection=<SUBDOMAIN>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.perimeter81.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Perimeter 81-klient support teamet](mailto:support@perimeter81.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Perimeter 81** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till perimeter 81.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Perimeter 81**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-perimeter-81-sso"></a>Konfigurera perimeter 81 SSO

1. Om du vill automatisera konfigurationen inom perimeter 81 måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera Perimeter 81** för att dirigera dig till perimeter 81-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på perimeter 81. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera omkrets 81 manuellt, i ett annat webbläsarfönster, loggar du in på din perimeter 81-företags plats som administratör.

4. Gå till **Inställningar** och klicka på **identitets leverantörer**.

    ![Inställningar för perimeter 81](./media/perimeter-81-tutorial/settings.png)

5. Klicka på knappen **Lägg till provider** .

    ![Perimeter 81 Lägg till Provider](./media/perimeter-81-tutorial/add-provider.png)

6. Välj **SAML 2,0 Identity providers** och klicka på **Fortsätt** .

    ![Perimeter 81 Lägg till identitets leverantör](./media/perimeter-81-tutorial/add-identity-provider.png)

7. I avsnittet **SAML 2,0 Identity providers** , utför följande steg:

    ![Perimeter 81-konfiguration av SAML](./media/perimeter-81-tutorial/setting-up-saml.png)

    a. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    b. I text rutan **domän Ali Aset** anger du ditt domän Ali Aset-värde.

    c. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **X509-signerings certifikat** .

    > [!NOTE]
    > Du kan också klicka på **Ladda upp PEM/cert-filen** för att ladda upp det **certifikat (base64)** som du laddade ned från Azure Portal.
    
    d. Klicka på **Klar**.

### <a name="create-perimeter-81-test-user"></a>Skapa perimeter 81-test användare

I det här avsnittet skapas en användare som heter Britta Simon i omkrets 81. Perimeter 81 har stöd för just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i perimeter 81 skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till perimeter 81-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till perimeter 81-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den omkrets 81 som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på ikonen för perimeter 81 i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den perimeter 81 som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat perimeter 81 kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
