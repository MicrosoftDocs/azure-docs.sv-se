---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Salesforce | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Salesforce

I den här självstudien får du lära dig hur du integrerar Salesforce med Azure Active Directory (Azure AD). När du integrerar Salesforce med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Salesforce.
* Gör det möjligt för användarna att logga in automatiskt till Salesforce med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration på Salesforce-aktiverad enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Salesforce stöder **SP** -initierad SSO.

* Salesforce stöder [ **automatiserad** användar etablering och avetablering](salesforce-provisioning-tutorial.md) (rekommenderas).

* Salesforce stöder **just-in-Time** User-etablering.

* Salesforce-mobilappen kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

## <a name="adding-salesforce-from-the-gallery"></a>Lägga till Salesforce från galleriet

För att kunna konfigurera integreringen av Salesforce i Azure AD måste du lägga till Salesforce från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **Salesforce** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **Salesforce** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Konfigurera och testa Azure AD SSO för Salesforce

Konfigurera och testa Azure AD SSO med Salesforce med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Salesforce.

Utför följande steg för att konfigurera och testa Azure AD SSO med Salesforce:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Salesforce-SSO](#configure-salesforce-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Salesforce-testanvändare](#create-salesforce-test-user)** – för att få en motsvarighet till B. Simon i Salesforce som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Salesforce** -programintegration i Azure Portal letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:
    
    a. I textrutan **Identifierare** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. I text rutan **svars-URL** skriver du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. I textrutan **Inloggnings-URL** anger du värdet med följande mönster:

    Enterprise-konto: `https://<subdomain>.my.salesforce.com`

    Developer-konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support) för att få dessa värden.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Salesforce** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Salesforce.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Salesforce** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-salesforce-sso"></a>Konfigurera Salesforce SSO

1. Om du vill automatisera konfigurationen i Salesforce måste du installera **tillägget Mina appar säker inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När tillägget har lagts till i webbläsaren, klickar du på **Konfigurera Salesforce** för att dirigera dig till salesforce-Sign-On program. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Salesforce-enkel inloggning. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-13.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Salesforce manuellt öppnar du ett nytt webbläsarfönster och loggar in på webbplatsen för Salesforce-företaget som administratör och utför följande steg:

1. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Konfigurera ikonen för enskilda Sign-On inställningar](./media/salesforce-tutorial/configure1.png)

1. Rulla ned till **INSTÄLLNINGAR** i navigeringsfönstret och klicka på **Identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Konfigurera inställningar för enskilda Sign-On](./media/salesforce-tutorial/sf-admin-sso.png)

1. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Konfigurera enkel Sign-On redigera](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Om du inte kan aktivera inställningar för enkel inloggning för ditt Salesforce-konto kanske du måste kontakta [Salesforce-kundsupporten](https://help.salesforce.com/support).

1. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Konfigurera Single Sign-On SAML-aktiverad](./media/salesforce-tutorial/sf-enable-saml.png)

1. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Konfigurera en enskild Sign-On ny från metadatafilen](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Konfigurera en enskild Sign-On välja fil](./media/salesforce-tutorial/xmlchoose.png)

1. På sidan **Inställningar för SAML-enkel Sign-On** , Fyll i fält automatiskt, om du vill använda SAML JIT väljer du **användar etableringen aktiverat** och väljer SAML- **identitets typ** som **kontroll innehåller Federations-ID: t från användarobjektet** , annars avmarkerar du **användar etableringen aktiverat** och väljer SAML- **identitets typ** som **kontroll innehåller användarens Salesforce-användarnamn**. Klicka på **Spara**.

    ![Konfigurera enskilda Sign-On användar etablering aktive rad](./media/salesforce-tutorial/salesforcexml.png)

1. I det vänstra navigeringsfönstret i Salesforce klickar du på **Företagsinställningar** för att expandera relaterat avsnitt och klickar sedan på **Min domän**.

    ![Konfigurera en enda Sign-On min domän](./media/salesforce-tutorial/sf-my-domain.png)

1. Rulla ned till avsnittet **Autentiseringskonfiguration** och klicka på knappen **Redigera**.

    ![Konfigurera konfiguration av enskild Sign-On autentisering](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. I avsnittet **konfiguration av autentisering** , kontrol lera **inloggnings sidan** och  **AZURESSO** som **autentiseringstjänst** för din SAML SSO-konfiguration och klicka sedan på **Spara**.

    ![Konfigurera tjänsten för enkel Sign-On-autentisering](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > Om mer än en autentiseringstjänst har valts uppmanas användarna att välja vilken autentiseringstjänst de vill logga in med när de initierar enkel inloggning i din Salesforce-miljö. Om du inte vill att det ska hända ska du **låta alla andra autentiseringstjänster vara avmarkerade**.

### <a name="create-salesforce-test-user"></a>Skapa Salesforce-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i Salesforce. Salesforce stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Salesforce skapas en ny när du försöker komma åt Salesforce. Salesforce stöder även automatisk användaretablering. Mer information finns [här](salesforce-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Salesforce-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Salesforce-inloggningens URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Salesforce i Mina appar, bör du loggas in automatiskt på den Salesforce som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-salesforce-mobile"></a>Testa SSO för Salesforce (mobil)

1. Öppna Salesforce-mobilapp. På inloggnings sidan klickar du på **Använd anpassad domän**.

    ![Salesforce-mobilapp använder anpassad domän](media/salesforce-tutorial/mobile-app1.png)

1. I text rutan **anpassad domän** anger du ditt registrerade anpassade domän namn och klickar på **Fortsätt**.

    ![Salesforce-anpassad domän för mobilapp](media/salesforce-tutorial/mobile-app2.png)

1. Ange dina autentiseringsuppgifter för Azure AD för att logga in i Salesforce-programmet och klicka på **Nästa**.

    ![Autentiseringsuppgifter för Azure AD för Salesforce-mobilapp](media/salesforce-tutorial/mobile-app3.png)

1. På sidan **Tillåt åtkomst** som visas nedan klickar du på **Tillåt** för att ge åtkomst till Salesforce-programmet.

    ![Salesforce-mobilapp tillåter åtkomst](media/salesforce-tutorial/mobile-app4.png)

1. När du har loggat in igen visas programmets start sida.

    ![Start sida för Salesforce ](media/salesforce-tutorial/mobile-app5.png) -mobilapp i ![ Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Salesforce kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)