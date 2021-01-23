---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Adobe Identity Management | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726408"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Adobe Identity Management

I den här självstudien får du lära dig hur du integrerar Adobe Identity Management med Azure Active Directory (Azure AD). När du integrerar Adobe Identity Management med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adobe Identity Management.
* Gör det möjligt för användarna att logga in automatiskt till Adobe Identity Management med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumerationen för enkel inloggning med Adobe Identity Management (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Adobe Identity Management stöder **SP** -INITIERAd SSO

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Lägga till Adobe Identity Management från galleriet

Om du vill konfigurera integrationen av Adobe Identity Management i Azure AD måste du lägga till Adobe Identity Management från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Adobe Identity Management** i sökrutan.
1. Välj **Adobe Identity Management** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Konfigurera och testa Azure AD SSO för Adobe Identity Management

Konfigurera och testa Azure AD SSO med Adobe Identity Management med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Adobe Identity Management.

Utför följande steg för att konfigurera och testa Azure AD SSO med Adobe Identity Management:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Adobe Identity Management SSO](#configure-adobe-identity-management-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa test användare av Adobe Identity Management](#create-adobe-identity-management-test-user)** – för att få en motsvarighet till B. Simon i Adobe Identity Management som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Adobe Identity Management** -programintegration i Azure Portal letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://adobe.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [support teamet för Adobe Identity Management-klienten](mailto:identity@adobe.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Adobe Identity Management** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Adobe Identity Management.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Adobe Identity Management**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-adobe-identity-management-sso"></a>Konfigurera Adobe Identity Management SSO

1. Om du vill automatisera konfigurationen i Adobe Identity Management måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera Adobe Identity Management** för att dirigera dig till Adobe Identity Management-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Adobe Identity Management. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Adobe Identity Management manuellt i ett annat webbläsarfönster loggar du in på ditt Adobe Identity Management företags webbplats som administratör.

4. Gå till fliken **Inställningar** och klicka på **Skapa katalog**.

    ![Inställningar för Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Ge katalog namnet i text rutan och välj **FEDERERAT ID**, klicka på **Nästa**.

    ![Skapa katalog för Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Välj **andra SAML-providers** och klicka på **Nästa**.
 
    ![SAML-providers för Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Klicka på **Välj** för att ladda upp **metadata-XML-** filen som du har laddat ned från Azure Portal.

    ![SAML-konfiguration för Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Klicka på **färdig**.

### <a name="create-adobe-identity-management-test-user"></a>Skapa test användare av Adobe Identity Management

1. Gå till fliken **användare** och klicka på **Lägg till användare**.

    ![Lägg till användare i Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. Ge **e-postadressen** i text rutan **Ange användarens e-postadress** .

    ![Spara användare i Adobe Identity Management](./media/adobe-identity-management-tutorial/save-user.png)

3. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Adobe Identity Management-inloggnings-URL där du kan starta inloggnings flödet.

* Gå till inloggnings-URL: en för Adobe Identity Management direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Adobe Identity Management i Mina appar omdirigeras den till inloggnings-URL: en för Adobe Identity Management. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Adobe Identity Management kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).