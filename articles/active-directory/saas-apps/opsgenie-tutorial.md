---
title: 'Självstudie: Azure Active Directory integrering med OpsGenie | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OpsGenie.
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
ms.openlocfilehash: 2ab1f9ee6095dfc0f708ec33622aad6f70fcae65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728588"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med OpsGenie

I den här självstudien får du lära dig hur du integrerar OpsGenie med Azure Active Directory (Azure AD). När du integrerar OpsGenie med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OpsGenie.
* Gör det möjligt för användarna att logga in automatiskt till OpsGenie med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* OpsGenie för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* OpsGenie stöder **IDP** INITIERAd SSO

## <a name="adding-opsgenie-from-the-gallery"></a>Lägga till OpsGenie från galleriet

Om du vill konfigurera integreringen av OpsGenie i Azure AD måste du lägga till OpsGenie från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **OpsGenie** i sökrutan.
1. Välj **OpsGenie** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-opsgenie"></a>Konfigurera och testa Azure AD SSO för OpsGenie

Konfigurera och testa Azure AD SSO med OpsGenie med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i OpsGenie.

Utför följande steg för att konfigurera och testa Azure AD SSO med OpsGenie:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera OPSGENIE SSO](#configure-opsgenie-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa OpsGenie test User](#create-opsgenie-test-user)** -om du vill ha en motsvarighet till B. Simon i OpsGenie som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **OpsGenie** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren och svars-URL, vilket beskrivs senare i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. I avsnittet **Konfigurera OpsGenie** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till OpsGenie.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **OpsGenie**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-opsgenie-sso"></a>Konfigurera OpsGenie SSO

1. Om du vill automatisera konfigurationen i OpsGenie måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera OpsGenie** för att dirigera dig till OpsGenie-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på OpsGenie. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera OpsGenie manuellt i ett annat webbläsarfönster loggar du in på OpsGenie Company-platsen som administratör.

2. Klicka på **Inställningar** och klicka sedan på fliken **enkel inloggning** .
   
    ![OpsGenie enkla Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Om du vill aktivera SSO väljer du **aktive rad**.
   
    ![Skärm bild som visar kryss rutan "aktive rad" markerad.](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. I avsnittet **Provider** klickar du på fliken **Azure Active Directory** .
   
    ![Skärm bild som visar avsnittet "Provider" med fliken "Azure Active Directory" vald.](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Utför följande steg på dialog sidan Azure Active Directory:
   
    ![Skärm bild som visar avsnittet "enkel inloggning" med växlingen "aktivera enkel inloggning", "S A M L 2,0-slutpunkt" och "metadata U R L".](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopiera **app-ID-URI** -värdet och klistra in det i ID-rutan för **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    a. Kopiera **svars-URL** -värdet och klistra in det i text rutan för **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    a. I text rutan **slut punkt för SAML 2,0** klistrar du in **inloggnings-URL**-värdet som du har kopierat från Azure Portal.
    
    b. I text rutan **metadata-URL:** klistrar du in URL-värdet för **app Federation-Metadata** som du har kopierat från Azure Portal.
    
    c. Aktivera Aktivera **enkel inloggning** för att aktivera SSO.

    d. Klicka på **Använd SSO-inställningar**.

### <a name="create-opsgenie-test-user"></a>Skapa OpsGenie test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i OpsGenie. 

1. Logga in på din OpsGenie-klient som administratör i ett webbläsarfönster.

2. Navigera till listan användare genom att klicka på **användare** i den vänstra panelen.
   
    ![OpsGenie-inställningar](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klicka på **Lägg till användare**.

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![Skärm bild som visar dialog rutorna "Lägg till användare" med text rutorna "e-post" och "fullständigt namn" markerade och knappen "Spara" markerad.](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. I text rutan **e-post** skriver du e-postadressen för B. Simon-adresserad i Azure Active Directory.
   
    b. Skriv **B. Simon** i text rutan **fullständigt namn** .
   
    c. Klicka på **Spara**. 

> [!NOTE]
> B. Simon får ett e-postmeddelande med anvisningar för att konfigurera profilen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den OpsGenie som du har konfigurerat SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen OpsGenie i Mina appar, bör du loggas in automatiskt på den OpsGenie som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

* När du har konfigurerat OpsGenie kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).