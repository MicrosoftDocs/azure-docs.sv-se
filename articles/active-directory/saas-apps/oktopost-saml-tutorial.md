---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med SAML-integrering med Oktopost | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Oktopost SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 53b640fcce4c25f4c5269b32c6355578b470030b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020938"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Oktopost SAML

I den här självstudien får du lära dig hur du integrerar Oktopost SAML med Azure Active Directory (Azure AD). När du integrerar Oktopost SAML med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Oktopost SAML.
* Gör det möjligt för användarna att logga in automatiskt för att Oktopost SAML med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Oktopost SAML-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Oktopost SAML stöder **SP-och IDP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-oktopost-saml-from-the-gallery"></a>Lägga till Oktopost SAML från galleriet

Om du vill konfigurera integrationen av Oktopost SAML i Azure AD måste du lägga till Oktopost SAML från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Oktopost SAML** i sökrutan.
1. Välj **OKTOPOST SAML** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Konfigurera och testa Azure AD SSO för Oktopost SAML

Konfigurera och testa Azure AD SSO med Oktopost SAML med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Oktopost SAML.

Utför följande steg för att konfigurera och testa Azure AD SSO med Oktopost SAML:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera OKTOPOST SAML SSO](#configure-oktopost-saml-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa OKTOPOST SAML test User](#create-oktopost-saml-test-user)** – om du vill ha en motsvarighet till B. Simon i Oktopost SAML som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **OKTOPOST SAML** application integration i Azure Portal letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://app.oktopost.com/auth/login`


1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera OKTOPOST SAML** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Oktopost SAML.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **OKTOPOST SAML**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-oktopost-saml-sso"></a>Konfigurera Oktopost SAML SSO

1. Logga in på Oktopost SAML som administratör.

1. Klicka på **ikonen för användaren > inställningar**.

    ![Oktopost SAML-inställningar](./media/oktopost-saml-tutorial/settings.png)

1. I **inställningarna** går du till sidan **säkerhets > enkel inloggning** och utför följande steg.

    ![Oktopost SAML-konfiguration](./media/oktopost-saml-tutorial/configure-sso.png)

    a. Välj **aktivera enkel inloggning** till **Ja**.

    b. I text rutan **SAML-slutpunkt** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    c. I text rutan **utfärdare** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **X. 509-certifikat** .
    
    e. Klicka på **Spara**.

### <a name="create-oktopost-saml-test-user"></a>Skapa Oktopost SAML test User

1. Logga in på Oktopost SAML som administratör.

1. Klicka på **ikonen för användaren > inställningar**.

    ![Oktopost SAML-test Användare1](./media/oktopost-saml-tutorial/settings.png)

1.  Gå till **användar hanterings > användare > Lägg till användare**.

    ![Oktopost SAML test användare2](./media/oktopost-saml-tutorial/add-user-1.png)

1. Fyll i de obligatoriska fälten i popup-fönstret och klicka på **Skicka**.

    ![Oktopost SAML test user3](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Oktopost SAML-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Oktopost SAML-inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till det Oktopost SAML som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på Oktopost SAML-panelen i Mina appar, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till Oktopost SAML som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Oktopost SAML kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


