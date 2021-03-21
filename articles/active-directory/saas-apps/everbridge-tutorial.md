---
title: 'Självstudie: Azure Active Directory integrering med EverBridge | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EverBridge.
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
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643787"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Självstudie: Azure Active Directory integrering med EverBridge

I den här självstudien får du lära dig hur du integrerar EverBridge med Azure Active Directory (Azure AD).
När du integrerar EverBridge med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EverBridge.
* Tillåt att användarna loggas in automatiskt på EverBridge med sina Azure AD-konton. Den här åtkomst kontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med EverBridge behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En EverBridge-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EverBridge stöder IDP-initierad SSO.

## <a name="add-everbridge-from-the-gallery"></a>Lägg till EverBridge från galleriet

Om du vill konfigurera integreringen av EverBridge i Azure AD måste du lägga till EverBridge från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **EverBridge** i sökrutan.
1. Välj **EverBridge** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Konfigurera och testa Azure AD SSO för EverBridge

Konfigurera och testa Azure AD SSO med EverBridge med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EverBridge.

Utför följande steg för att konfigurera och testa Azure AD SSO med EverBridge:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EVERBRIDGE SSO](#configure-everbridge-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa EverBridge test User](#create-everbridge-test-user)** -om du vill ha en motsvarighet till B. Simon i EverBridge som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **EverBridge** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurera programmet antingen som chefs Portal *eller* som medlems Portal på både Azure Portal-och EverBridge-portalen.

4. Följ dessa steg för att konfigurera **EverBridge** -programmet som **EverBridge Manager-portalen** i avsnittet **grundläggande SAML-konfiguration** :

    ![Information om enkel inloggning för EverBridge-domän och URL: er](common/idp-intiated.png)

    a. I rutan **identifierare** anger du en URL som följer mönstret.
    `https://sso.everbridge.net/<API_Name>`

    b. I rutan **svars-URL** anger du en URL som följer mönstret.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska ID-och svars-URL-värden. Kontakta [EverBridge support-teamet](mailto:support@everbridge.com)om du vill hämta dessa värden. Du kan också referera till mönstren som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

5. Följ dessa steg för att konfigurera **EverBridge** -programmet som **EverBridge-medlems Portal** i avsnittet **grundläggande SAML-konfiguration** :

  * Följ dessa steg om du vill konfigurera programmet i IDP läge:

     ![Information om enkel inloggning för EverBridge-domän och URL: er för IDP-initierat läge](common/idp-intiated.png)

    a. I rutan **identifierare** anger du en URL som följer mönstret `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. I rutan **svars-URL** anger du en URL som följer mönstret `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och följer det här steget:

     ![Information om enkel inloggning för EverBridge-domän och URL: er för SP-initierat läge](common/both-signonurl.png)

     a. I rutan **inloggnings-URL** anger du en URL som följer mönstret `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL-värden. Kontakta [EverBridge support-teamet](mailto:support@everbridge.com)om du vill hämta dessa värden. Du kan också referera till mönstren som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

6. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **XML-metadata för federationsmetadata**. Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera EverBridge** kopierar du de webb adresser som du behöver för dina behov:

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EverBridge.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **EverBridge**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-everbridge-sso"></a>Konfigurera EverBridge SSO

Följ dessa steg om du vill konfigurera SSO på **EverBridge** som ett **EverBridge Manager-portalprogram** .
 
1. Logga in på EverBridge som administratör i ett annat webbläsarfönster.

1. I menyn högst upp väljer du fliken **Inställningar** . Under **säkerhet** väljer du **enkel inloggning**.
   
     ![Konfigurera enkel inloggning](./media/everbridge-tutorial/sso.png)
   
     a. I rutan **namn** anger du namnet på ID-providern. Ett exempel är företagets namn.
   
     b. I rutan **API-namn** anger du namnet på API: et.
   
     c. Välj **Välj fil** för att ladda upp metadatafilen som du laddade ned från Azure Portal.
   
     d. För **SAML-identitets platsen** väljer **du identitet i NameIdentifier-elementet för ämnes instruktionen**.
   
     e. I rutan **inloggnings-URL för identitetsprovider** klistrar du in URL-värdet för **inloggning** som du kopierade från Azure Portal.
   
     f. Välj **http-omdirigering** för **tjänste leverantörens initierade begär ande bindning**.

     ex. Välj **Spara**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Konfigurera EverBridge som EverBridge-medlems Portal SSO

Om du vill konfigurera enkel inloggning på **EverBridge** som en **EverBridge-medlems Portal** skickar du den hämtade **XML-koden för federationsmetadata** till [EverBridge support-teamet](mailto:support@everbridge.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-everbridge-test-user"></a>Skapa EverBridge test användare

I det här avsnittet skapar du test User Britta Simon i EverBridge. Om du vill lägga till användare i EverBridge-plattformen arbetar du med [EverBridge support-teamet](mailto:support@everbridge.com). Användare måste skapas och aktive ras i EverBridge innan du använder enkel inloggning. 

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den EverBridge som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen EverBridge i Mina appar, bör du loggas in automatiskt på den EverBridge som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EverBridge kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).