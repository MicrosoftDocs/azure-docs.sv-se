---
title: 'Självstudie: Azure Active Directory integrering med ArcGIS Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Online.
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
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646358"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Självstudie: Azure Active Directory integrering med ArcGIS Online

I den här självstudien får du lära dig hur du integrerar ArcGIS Online med Azure Active Directory (Azure AD). När du integrerar ArcGIS Online med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ArcGIS Online.
* Gör det möjligt för användarna att logga in automatiskt till ArcGIS Online med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ArcGIS Online, enkel inloggning (SSO) aktive rad prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ArcGIS Online stöder **SP** -initierad SSO.

## <a name="add-arcgis-online-from-the-gallery"></a>Lägg till ArcGIS Online från galleriet

För att konfigurera integreringen av ArcGIS Online till Azure AD behöver du lägga till ArcGIS Online från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ArcGIS Online** i sökrutan.
1. Välj **ArcGIS Online** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Konfigurera och testa Azure AD SSO för ArcGIS Online

Konfigurera och testa Azure AD SSO med ArcGIS Online med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ArcGIS Online.

Utför följande steg för att konfigurera och testa Azure AD SSO med ArcGIS Online:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ArcGIS Online SSO](#configure-arcgis-online-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ArcGIS Online-test](#create-arcgis-online-test-user)** för att få en motsvarighet till B. Simon i ArcGIS Online som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **ArcGIS Online** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.maps.arcgis.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [ArcGIS Onlines kundsupportteam](https://support.esri.com/en/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. Om du vill automatisera konfigurationen i **ArcGIS Online**, måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/arcgis-tutorial/install-extension.png)

7. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera ArcGIS Online**. Du omdirigeras då till ArcGIS Online-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på ArcGIS Online. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar stegen i avsnitt **Konfigurera enkel inloggning med ArcGIS Online**.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ArcGIS Online.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **ArcGIS Online**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-arcgis-online-sso"></a>Konfigurera ArcGIS Online SSO

1. Om du vill konfigurera ArcGIS Online manuellt öppnar du ett nytt webbläsarfönster och loggar in på din ArcGIS Online-företagsplats som administratör och utför följande steg:

2. Klicka på **Redigera inställningar**.

    ![Redigera inställningar](./media/arcgis-tutorial/settings.png "Redigera inställningar")

3. Klicka på **Säkerhet**.

    ![Säkerhet](./media/arcgis-tutorial/secure.png "Säkerhet")

4. Under **Företagsinloggningar**, klickar du på **SET IDENTITY PROVIDER**.

    ![Företags inloggningar](./media/arcgis-tutorial/enterprise.png "Företags inloggningar")

5. Utför följande steg på konfigurationssidan **Ange identitetsprovider**:

    ![Ange identitets leverantör](./media/arcgis-tutorial/identity-provider.png "Ange identitets leverantör")

    a. I textrutan **Namn** skriver du namnet på din organisation.

    b. För **Metadata för företagsidentitetsprovider kommer att tillhandahållas med** väljer du **En fil**.

    c. För att ladda upp den nedladdade metadatafilen klickar du på **Välj fil**.

    d. Klicka på **SET IDENTITY PROVIDER**.

### <a name="create-arcgis-online-test-user"></a>Skapa ArcGIS Online-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på ArcGIS Online måste de etableras i ArcGIS Online.  
När det gäller ArcGIS Online är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **ArcGIS**-klient.

2. Klicka på **BJUD IN MEDLEMMAR**.

    ![Bjud in medlemmar](./media/arcgis-tutorial/invite.png "Bjud in medlemmar")

3. Välj **Lägg till medlemmar automatiskt utan att skicka ett e-postmeddelande** och klicka sedan på **NÄSTA**.

    ![Lägg till medlemmar automatiskt](./media/arcgis-tutorial/members.png "Lägg till medlemmar automatiskt")

4. I dialogrutan **Medlemmar** utför du följande steg:

    ![Lägg till och granska](./media/arcgis-tutorial/review.png "Lägg till och granska")

     a. Ange **e-postadress**, **förnamn** och **efter namn** för ett giltigt Azure AD-konto som du vill etablera.

     b. Klicka på **LÄGG TILL OCH GRANSKA**.
5. Granska de data du har angett och klicka sedan på **LÄGG TILL MEDLEMMAR**.

    ![Lägg till medlem](./media/arcgis-tutorial/add.png "Lägg till medlem")

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till ArcGIS Online-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till ArcGIS Online-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen ArcGIS Online i Mina appar omdirigeras detta till ArcGIS Online-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ArcGIS Online kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).