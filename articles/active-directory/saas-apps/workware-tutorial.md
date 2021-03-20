---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med arbets uppgifter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbets uppgifter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med arbets uppgifter

I den här självstudien får du lära dig hur du integrerar arbets uppgifter med Azure Active Directory (Azure AD). När du integrerar arbets uppgifter med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till arbets uppgifter.
* Gör det möjligt för användarna att logga in automatiskt till arbete med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktive rad prenumeration för enkel inloggning (SSO) för arbets uppgifter.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Arbets uppgifter stöder **IDP** INITIERAd SSO

## <a name="adding-workware-from-the-gallery"></a>Lägga till arbets uppgifter från galleriet

Om du vill konfigurera en integrering av arbets uppgifter i Azure AD måste du lägga till arbets program från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **arbets uppgifter** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **arbets** program från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Konfigurera och testa Azure AD SSO för arbets uppgifter

Konfigurera och testa Azure AD SSO med arbets program med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets uppgifter.

Utför följande steg för att konfigurera och testa Azure AD SSO med arbets uppgifter:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera arbets uppgifter](#configure-workware-sso)** för enkel inloggning – för att konfigurera inställningar för enkel inloggning på program sidan.
    * **[Skapa test av arbets uppgifter](#create-workware-test-user)** – om du vill ha en motsvarighet till B. Simon i arbets program som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integration i **arbets** program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<WORKWARE_URL>/WW/AuthServices`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Hämta de här värdena genom att kontakta arbets platsens [Support Team](mailto:support@activeops.com) . Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera arbets uppgifter** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets uppgifter.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **arbets uppgifter**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-workware-sso"></a>Konfigurera arbets uppgifter för enkel inloggning

Om du vill använda funktionen SSO i arbets uppgifter måste du utföra följande inställningar:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Aktivera SSO-behörigheter för arbets Systems administratörer

* Om du vill att arbets system administratörer ska kunna konfigurera SSO-autentisering måste behörigheten SSO (i **kategorin administrations > behörighet för system konfiguration i kategorin Systeminställningar > behörighet till roll** ) vara aktive rad för arbets system administratörer.

    ![Behörighet för SSO-autentisering](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Konfigurera SSO-autentisering i arbets uppgifter

1. Gå till sidan **system inställningar** och klicka på **SSO-autentisering**

1. I avsnittet **SSO-autentisering** klickar du på knappen **Lägg till SSO-autentisering** och utför följande steg: 

    ![SSO-autentisering](./media/workware-tutorial/authentication.png)

    1. Ange namnet på IDP i den **externa identitets leverantören**.
    1. Välj **SAML 2.0** som **Autentiseringstyp**
    1. I text rutan **identitets leverantörens inloggnings-URL** anger du **inloggnings-URL** -värdet, som du har kopierat från Azure Portal.
    1. I text rutan **URL för identitets leverantör** anger du värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.
    1. I text rutan **URL för identitets leverantörs utloggning** anger du URL-värdet för **utloggning** som du har kopierat från Azure Portal.
    1. Klicka på **Aktivera**.
    1. Överför det hämtade **certifikatet** till **identitets leverantörens certifikat** från Azure Portal.
    1. Klicka på **Spara**


### <a name="create-workware-test-user"></a>Skapa test användare av arbets uppgifter

1. Logga in på webbplatsen för arbets uppgifter som administratör.

1. Välj **Admin > skapa/visa > användar konton > Lägg till ny**

1. Utför följande steg på följande sida.

    ![Testa användare](./media/workware-tutorial/create-user.png)

    a. Ange ett giltigt namn i fältet **namn** .

    b. Välj **Autentiseringstyp** som **SSO**.

    c. Ange de obligatoriska fälten och klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på de arbets uppgifter som du konfigurerar SSO för.

* Du kan använda Microsoft Access-panelen. När du klickar på panelen arbets uppgifter i åtkomst panelen bör du loggas in automatiskt på de arbets uppgifter som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat arbets uppgifter kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).