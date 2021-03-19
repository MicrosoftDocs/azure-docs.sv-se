---
title: 'Självstudie: Azure Active Directory integrering med Wdesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: jeedes
ms.openlocfilehash: d85d7ef37536b54ecfc1b65d19eafd1d499ca050
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104603351"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Wdesk

I den här självstudien får du lära dig hur du integrerar Wdesk med Azure Active Directory (Azure AD). När du integrerar Wdesk med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Wdesk.
* Gör det möjligt för användarna att logga in automatiskt till Wdesk med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Wdesk för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Wdesk stöder **SP** -och **IDP** -initierad SSO.

## <a name="add-wdesk-from-the-gallery"></a>Lägg till Wdesk från galleriet

Om du vill konfigurera integreringen av Wdesk i Azure AD måste du lägga till Wdesk från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Wdesk** i sökrutan.
1. Välj **Wdesk** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-wdesk"></a>Konfigurera och testa Azure AD SSO för Wdesk

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Wdesk baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Wdesk upprättas.

Utför följande steg för att konfigurera och testa Azure AD SSO med Wdesk:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera WDESK SSO](#configure-wdesk-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Wdesk test User](#create-wdesk-test-user)** -om du vill ha en motsvarighet till B. Simon i Wdesk som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Wdesk** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Du får dessa värden från WDesk-portalen när du konfigurerar SSO.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Wdesk** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Wdesk.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Wdesk**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-wdesk-sso"></a>Konfigurera Wdesk SSO

1. Logga in på Wdesk som säkerhets administratör i ett annat webbläsarfönster.

1. Längst ned till vänster klickar du på **admin** och väljer **konto administratör**:
 
    ![Skärm bild som visar konto administratör som valts på administratörs menyn.](./media/wdesk-tutorial/account.png)

1. I Wdesk-administratör navigerar du till **säkerhet**, sedan **SAML**  >  **SAML Settings**:

    ![Skärm bild som visar de SAML-inställningar som valts från fliken SAML.](./media/wdesk-tutorial/settings.png)

1. Under **Inställningar för SAML-användar-ID**, kontrol lera **SAML User ID är Wdesk username**.

    ![Skärm bild som visar inställningar för SAML-användare I D där du kan välja SAML-användare I D är W Skriv bords användar namn.](./media/wdesk-tutorial/wdesk-username.png)

4. Under **allmänna inställningar** markerar du **aktivera enkel inloggning med SAML**:

    ![Skärm bild som visar Redigera SAML-inställningar där du kan välja Aktivera enkel inloggning med SAML.](./media/wdesk-tutorial/user-settings.png)

5. Utför följande steg under **tjänst leverantörs information**:

    ![Skärm bild som visar information om tjänst leverantör där du kan ange de värden som beskrivs.](./media/wdesk-tutorial/service-provider.png)

    1. Kopiera **inloggnings-URL** och klistra in den i text rutan för **inloggnings-URL** i Azure Portal.

    1. Kopiera **URL: en för metadata** och klistra in den i text rutan **identifierare** i Azure Portal.

    1. Kopiera **konsument-URL: en** och klistra in den i text rutan **svars-URL** på Azure Portal.

    1. Spara ändringarna genom att klicka på **Spara** på Azure Portal.      

1. Klicka på **Konfigurera IDP inställningar** för att öppna dialog rutan **Redigera inställningar för IDP** . Klicka på **Välj fil** för att hitta **Metadata.xml** filen som du sparade från Azure Portal och ladda sedan upp den.
    
    ![Skärm bild som visar redigera I d P-inställningar där du kan ladda upp metadata.](./media/wdesk-tutorial/metadata.png)
  
1. Klicka på **Spara ändringar**.

    ![Skärm bild som visar knappen Spara ändringar.](./media/wdesk-tutorial/save.png)

### <a name="create-wdesk-test-user"></a>Skapa Wdesk test användare

Om du vill att Azure AD-användare ska kunna logga in på Wdesk måste de tillhandahållas i Wdesk. I Wdesk är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Wdesk som säkerhets administratör.

2. Gå till **Administratörs**  >  **konto administratör**.

     ![Skärm bild som visar konto administratör som valts på administratörs menyn.](./media/wdesk-tutorial/account.png)

3. Klicka på **medlemmar** under **personer**.

4. Klicka nu på **Lägg till medlem** för att öppna dialog rutan **Lägg till medlem** . 
   
    ![Skärm bild som visar fliken medlemmar där du kan välja Lägg till medlem.](./media/wdesk-tutorial/create-user-1.png)  

5. I text rutan **användare** anger du användar namnet för användaren, till exempel b.simon@contoso.com och klickar på knappen **Fortsätt** .

    ![Skärm bild som visar dialog rutan Lägg till medlem där du kan ange en användare.](./media/wdesk-tutorial/create-user-3.png)

6.  Ange informationen på det sätt som visas nedan:
  
    ![Skärm bild som visar dialog rutan Lägg till medlem där du kan lägga till grundläggande information för en användare.](./media/wdesk-tutorial/create-user-4.png)
 
    a. I text rutan **e-postadress** anger du e-postadressen till användaren b.simon@contoso.com .

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **B**.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

7. Klicka på knappen **Spara medlem** .  

    ![Skärm bild som visar skicka Välkommen-e-post med knappen Spara medlem.](./media/wdesk-tutorial/create-user-5.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Wdesk-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Wdesk-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Wdesk som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Wdesk i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Wdesk som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Wdesk kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
