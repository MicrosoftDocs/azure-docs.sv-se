---
title: 'Självstudie: Azure Active Directory integrering med MOVEit-överföring – Azure AD-integrering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MOVEit-överföring – Azure AD-integrering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653084"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Självstudie: Azure Active Directory integrering med MOVEit transfer – Azure AD-integrering

I den här självstudien får du lära dig att integrera MOVEit-överföring – Azure AD-integrering med Azure Active Directory (Azure AD). När du integrerar MOVEit transfer-Azure AD-integrering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MOVEit-överföring – Azure AD-integrering.
* Gör det möjligt för användarna att logga in automatiskt till MOVEit transfer – Azure AD-integrering med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* MOVEit-överföring – Azure AD integration enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MOVEit-överföring – Azure AD-integrering stöder **SP** -INITIERAd SSO

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Lägg till MOVEit transfer – Azure AD-integrering från galleriet

Om du vill konfigurera integrationen av MOVEit transfer-Azure AD-integrering i Azure AD måste du lägga till MOVEit transfer-Azure AD integration från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **MOVEit transfer – Azure AD integration** i sökrutan.
1. Välj **MOVEit transfer – Azure AD integration** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Konfigurera och testa Azure AD SSO för MOVEit-överföring – Azure AD-integrering

Konfigurera och testa Azure AD SSO med MOVEit transfer – Azure AD-integrering med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i MOVEit transfer – Azure AD-integrering.

Utför följande steg för att konfigurera och testa Azure AD SSO med MOVEit transfer – Azure AD-integrering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera MOVEit-överföring – Azure AD-integrering SSO](#configure-moveit-transfer---azure-ad-integration-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa MOVEit transfer – Azure AD integration test User](#create-moveit-transfer---azure-ad-integration-test-user)** -om du vill ha en motsvarighet till B. Simon i MOVEit-överföring – Azure AD-integrering som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **MOVEit transfer – Azure AD integration** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värde automatiskt i avsnittet **grundläggande SAML-konfiguration** :

    ![MOVEit-överföring – Azure AD-integrerings domän och enkel inloggnings information för URL: er](common/sp-identifier-reply.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://contoso.com`

    > [!NOTE]
    > **Inloggnings-URL** -värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [MOVEit transfer – support teamet för Azure AD integration client](https://community.ipswitch.com/s/support) för att hämta värdet. Du kan ladda ned **metadata filen för tjänst leverantören** från **providerns metadata-URL** , som beskrivs senare i avsnittet **Konfigurera Moveit-överföring – Azure AD-integrering** i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera MOVEit-överföring – Azure AD-integrering** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MOVEit transfer – Azure AD-integrering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **MOVEit transfer – Azure AD-integrering**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Konfigurera MOVEit-överföring – Azure AD-integrering SSO

1. Logga in på MOVEit transfer-klienten som administratör.

2. I det vänstra navigeringsfönstret klickar du på **Inställningar**.

    ![Avsnittet Inställningar på App-sidan](./media/moveittransfer-tutorial/settings.png)

3. Klicka på **single inloggning** Link, som finns under **säkerhets principer – > användarautentisering**.

    ![Säkerhets principer på App-sida](./media/moveittransfer-tutorial/sso.png)

4. Hämta Metadatadokumentet genom att klicka på länken för metadata-URL.

    ![Metadata-URL för tjänste leverantör](./media/moveittransfer-tutorial/metadata.png)
    
   * Verifiera **entityId** matchar **identifieraren** i avsnittet **grundläggande SAML-konfiguration** .
   * Verifiera **AssertionConsumerService** -platsens URL matchar **svars-URL**  i avsnittet **grundläggande SAML-konfiguration** .
    
     ![Konfigurera enkel inloggning på appsidan](./media/moveittransfer-tutorial/xml.png)

5. Klicka på knappen **Lägg till identitetsprovider** för att lägga till en ny federerad identitets leverantör.

    ![Lägg till identitetsprovider](./media/moveittransfer-tutorial/idp.png)

6. Klicka på **Bläddra...** om du vill välja den metadatafil som du laddade ned från Azure Portal, klickar du på **Lägg till identitets leverantör** för att ladda upp den nedladdade filen.

    ![SAML Identity Provider](./media/moveittransfer-tutorial/saml.png)

7. Välj "**Ja**" som **aktive rad** på sidan **Redigera inställningar för federerad identitetsprovider...** och klicka på **Spara**.

    ![Inställningar för federerade identitets leverantörer](./media/moveittransfer-tutorial/save.png)

8. På sidan **Redigera användar inställningar för federerad identitetsprovider** utför du följande åtgärder:
    
    ![Redigera inställningar för federerade identitets leverantörer](./media/moveittransfer-tutorial/attributes.png)
    
    a. Välj **SAML-NameID** som **inloggnings namn**.
    
    b. Välj **annat** som **fullständigt namn** och ange värdet i text rutan **attributnamn** : `http://schemas.microsoft.com/identity/claims/displayname` .
    
    c. Välj **annan** som **e-post** och i text rutan **attributnamn** sätter du in värdet: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .
    
    d. Välj **Ja** som **Skapa automatiskt konto på inloggning**.
    
    e. Klicka på knappen **Spara**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Skapa MOVEit-överföring – test användare för Azure AD-integration

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i MOVEit transfer – Azure AD-integrering. MOVEit-överföring – Azure AD-integration stöder just-in-Time-etablering, som du har aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att komma åt MOVEit-överföring – Azure AD-integration om den inte finns ännu.

>[!NOTE]
>Om du behöver skapa en användare manuellt måste du kontakta [support teamet MOVEit transfer-Azure AD integration-klient](https://community.ipswitch.com/s/support).

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till MOVEit-överföring – inloggnings-URL för Azure AD-integration där du kan starta inloggnings flödet. 

* Gå till MOVEit transfer – inloggnings-URL för Azure AD-integration direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen MOVEit transfer-Azure AD-integration i Mina appar, bör du loggas in automatiskt på den MOVEit-överföring – Azure AD-integration som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat MOVEit transfer – Azure AD-integrering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).