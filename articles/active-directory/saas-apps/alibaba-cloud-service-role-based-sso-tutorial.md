---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Alibaba Cloud Service (rollbaserad SSO) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Alibaba moln tjänst (rollbaserad SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.openlocfilehash: 0e9ccb3f4308a1a75a715a16ab4c1a2887b0a915
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Alibaba Cloud Service (rollbaserad SSO)

I den här självstudien får du lära dig hur du integrerar Alibaba Cloud Service (rollbaserad SSO) med Azure Active Directory (Azure AD). När du integrerar Alibaba Cloud Service (rollbaserad SSO) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Alibaba Cloud Service (rollbaserad SSO).
* Gör det möjligt för användarna att logga in automatiskt till Alibaba Cloud Service (rollbaserad SSO) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Alibaba Cloud Service (rollbaserad SSO) med enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Alibaba Cloud Service (rollbaserad SSO) stöder **IDP** INITIERAd SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Lägga till Alibaba Cloud Service (rollbaserad SSO) från galleriet

Om du vill konfigurera integreringen av Alibaba Cloud Service (rollbaserad SSO) i Azure AD måste du lägga till Alibaba Cloud Service (rollbaserad SSO) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Alibaba Cloud Service (rollbaserad SSO)** i sökrutan.
1. Välj **Alibaba Cloud Service (ROLLBASERAD SSO)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.
5. På sidan **Alibaba Cloud Service (ROLLBASERAD SSO)** klickar du på **Egenskaper** i det vänstra navigerings fönstret och kopierar **objekt-ID: t** och sparar det på datorn för efterföljande användning.

    ![Egenskaper för konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för Alibaba Cloud Service (rollbaserad SSO)

Konfigurera och testa Azure AD SSO med Alibaba Cloud Service (rollbaserad SSO) med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Alibaba Cloud Service (rollbaserad SSO).

Om du vill konfigurera och testa Azure AD SSO med Alibaba Cloud Service (rollbaserad SSO) slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera rollbaserad enkel inloggning i Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** – så att användarna kan använda den här funktionen.
    1. **[Konfigurera Alibaba Cloud Service (ROLLBASERAD SSO) SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Alibaba Cloud Service (ROLLBASERAD SSO)-test användare](#create-alibaba-cloud-service-role-based-sso-test-user)** – om du vill ha en motsvarighet till Britta Simon i Alibaba Cloud Service (rollbaserad SSO) som är länkad till Azure AD-representation av användare.
3. **[Testa enkel inloggning](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Alibaba Cloud Service (rollbaserad SSO)** för program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får metadata för tjänste leverantören från denna [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp hämtas **ID** -och **SVARs-URL** -värden automatiskt i avsnittet Alibaba Cloud Service (rollbaserad SSO):

    > [!Note]
    > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Alibaba Cloud Service (rollbaserad SSO) kräver att roller konfigureras i Azure AD. Roll anspråk är förkonfigurerat så att du inte behöver konfigurera det, men du måste fortfarande skapa dem i Azure AD med hjälp av den här [artikeln](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Alibaba Cloud Service (ROLLBASERAD SSO)** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Alibaba Cloud Service (rollbaserad SSO).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Alibaba Cloud Service (ROLLBASERAD SSO)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. På fliken **användare och grupper** väljer du U2 i listan användare och klickar på **Välj**. Klicka sedan på **tilldela**.

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Visa den tilldelade rollen och test Alibaba moln tjänst (rollbaserad SSO).

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >När du har tilldelat användaren (U2) kopplas den skapade rollen automatiskt till användaren. Om du har skapat flera roller måste du koppla rätt roll till användaren efter behov. Om du vill implementera rollbaserad SSO från Azure AD till flera Alibaba-moln konton upprepar du föregående steg.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurera rollbaserad enkel inloggning i Alibaba-moln tjänst

1. Logga in på Alibaba Cloud [ram-konsolen](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) med hjälp av Account1.

2. I navigerings fönstret på den vänstra sidan väljer du **SSO**.

3. Klicka på **skapa IDP**på fliken **rollbaserad SSO** .

4. På sidan som visas anger `AAD` du i fältet IdP namn, anger en beskrivning i fältet **notering** , klickar på **överför** för att ladda upp den federationsmetadata som du hämtade tidigare och klickar på **OK**.

5. När IdP har skapats klickar du på **skapa ram-roll**.

6. I fältet **ram-rollnamn** anger du `AADrole` väljer du `AAD` från List rutan **Välj IDP** och klickar på OK.

    >[!NOTE]
    >Du kan bevilja behörighet till rollen vid behov. När du har skapat IdP och motsvarande roll rekommenderar vi att du sparar ARNs för IdP och rollen för efterföljande användning. Du kan hämta ARNs på sidan information om IdP och roll information.

7. Associera Alibaba Cloud RAM-rollen (AADrole) med Azure AD-användaren (U2): om du vill koppla RAM-rollen till Azure AD-användaren måste du skapa en roll i Azure AD genom att följa dessa steg:

    a. Logga in på [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

    b. Klicka på **ändra behörigheter** för att få de behörigheter som krävs för att skapa en roll.

    ![Graph-konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Välj följande behörigheter i listan och klicka på **ändra behörigheter**, som du ser i följande bild.

    ![Graph-konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >När behörigheterna har beviljats loggar du in i Graph Explorer igen.

    d. På Graph Explorer-sidan väljer du **Hämta** från den första List rutan och **beta** fönstret i den andra List rutan. Ange sedan `https://graph.microsoft.com/beta/servicePrincipals` i fältet bredvid List rutorna och klicka på **Kör fråga**.

    ![Graph-konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Om du använder flera kataloger kan du ange `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` i fältet för frågan.

    e. I avsnittet för **hands version av svar** extraherar du egenskapen AppRoles från tjänstens huvud namn för efterföljande användning.

    ![Graph-konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Du kan hitta egenskapen appRoles genom att ange `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` i fältet för frågan. Observera att `objectID` objekt-ID: t som du har kopierat från sidan **Egenskaper** för Azure AD.

    f. Gå tillbaka till Graph Explorer, ändra metoden från **Hämta** till **patch**, klistra in följande innehåll i avsnittet **begär ande text** och klicka på **Kör fråga**:
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value`Är Arns för IDP och rollen som du skapade i ram-konsolen. Här kan du lägga till flera roller efter behov. Azure AD skickar värdet för dessa roller som anspråks värde i SAML-svar. Du kan dock bara lägga till nya roller efter `msiam_access` delen för korrigerings åtgärden. För att utjämna skapandet av processen rekommenderar vi att du använder en ID-generator, till exempel GUID-generator, för att generera ID i real tid.

    ex. När tjänstens huvud namn har uppdaterats med den roll som krävs, bifogar du rollen med Azure AD-användaren (U2) genom att följa anvisningarna i avsnittet **tilldela test användare i Azure AD** i självstudien.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurera Alibaba Cloud Service (rollbaserad SSO) SSO

Om du vill konfigurera enkel inloggning på **Alibaba Cloud Service (ROLLBASERAD SSO)** , måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [ALIBABA Cloud Service (rollbaserad SSO) support team](https://www.aliyun.com/service/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Skapa Alibaba Cloud Service (rollbaserad SSO)-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Alibaba Cloud Service (rollbaserad SSO). Arbeta med [Alibaba Cloud Service (ROLLBASERAD SSO) support team](https://www.aliyun.com/service/) för att lägga till användarna i Alibaba Cloud Service-plattformen (rollbaserad SSO). Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

När de föregående konfigurationerna har slutförts testar du Alibaba Cloud Service (rollbaserad SSO) genom att följa dessa steg:

1. Gå till sidan **Alibaba Cloud Service (ROLLBASERAD SSO)** i Azure Portal och välj **enkel inloggning**och klicka på **testa**.

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klicka på **Logga in aktuell användare**.

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. På sidan konto val väljer du U2.

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Följande sida visas, vilket indikerar att rollbaserad SSO lyckas.

    ![Testa konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Alibaba Cloud Service (rollbaserad SSO) med Azure AD](https://aad.portal.azure.com/)

