---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med slack | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Slack.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: fdea1f3b2d4cff0203951b6ec5ef6b86b62cdf9c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527582"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med slack

I den här självstudien får du lära dig hur du integrerar slack med Azure Active Directory (Azure AD). När du integrerar slack med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till slack.
* Gör det möjligt för användarna att logga in automatiskt till slack med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktiverings prenumeration med enkel inloggning (SSO) för slack.

> [!NOTE]
> Om du behöver integrera med fler än en slack-instans i en klient, kan identifieraren för varje program vara en variabel.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Slack har stöd för **SP**-initierad enkel inloggning
* Slack stöder **just-in-time**-användaretablering
* Slack har stöd för [**automatisk** användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* När du har konfigurerat slack kan du framtvinga kontroll över sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-slack-from-the-gallery"></a>Lägga till Slack från galleriet

För att kunna konfigurera integreringen av Slack i Azure AD måste du lägga till Slack från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **slack** i sökrutan.
1. Välj **slack** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Konfigurera och testa enkel inloggning med Azure AD för slack

Konfigurera och testa Azure AD SSO med slack med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i slack.

Om du vill konfigurera och testa Azure AD SSO med slack slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SLACK SSO](#configure-slack-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa slack-test användare](#create-slack-test-user)** – om du vill ha en motsvarighet till B. Simon i slack som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **slack** -programmets integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://< DOMAIN NAME>.slack.com/sso/saml/start`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL: `https://slack.com`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Slack-kundsupporten](https://slack.com/help/contact) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    
    > [!NOTE]
    > Värdet för **identifierare (entitets-ID)** kan vara en variabel om du har fler än en slack-instans som du behöver integrera med klienten. Använd mönstret `https://<DOMAIN NAME>.slack.com` . I det här scenariot måste du också koppla till en annan inställning i slacket med samma värde.

1. Slack-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Förutom över, förväntar slack-programmet några fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav. Du måste också lägga till `email` attributet. Om användaren inte har en e-postadress mappar du **EmailAddress** till **User. UserPrincipalName** och mappar **e-post** till **User. UserPrincipalName**.

    | Name | Källattribut |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | e-post | user.userprincipalname |
    | | |

   > [!NOTE]
   > För att kunna konfigurera Service Provider (SP)-konfigurationen måste du klicka på **expandera** bredvid **Avancerade alternativ** på sidan SAML-konfiguration. I rutan **Service Provider Issuer** anger du URL för arbets ytan. Standardvärdet är slack.com. 

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera slack** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till slack.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Slack** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-slack-sso"></a>Konfigurera slack SSO

1. Logga in på ditt slack-företags webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **Microsoft Azure AD** och sedan till **Teaminställningar**.

     ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. I avsnittet **Teaminställningar** klickar du på fliken **Autentisering** och sedan på **Ändra inställningar**.

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Gör följande i dialogrutan **Inställningar för SAML-autentisering**:

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  I textrutan **SAML 2.0 Endpoint (HTTP)** (SAML 2.0-slutpunkt (HTTP)) klistrar du in värdet för **inloggnings-URL:en**, som du har kopierat från Azure-portalen.

    b.  I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) klistrar du in värdet för **Azure Ad-identifierare**, som du har kopierat från Azure-portalen.

    c.  Öppna den hämtade certifikat filen i anteckningar, kopiera innehållet i den till Urklipp och klistra sedan in den i text rutan för **offentligt certifikat** .

    d. Konfigurera de tre inställningarna ovan efter behov för ditt Slack-team. Mer information om inställningarna finns i **Slacks konfigurationsguide för enkel inloggning** här. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Konfigurera enkel inloggning på App-sida](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Klicka på **expandera** och ange `https://slack.com` i text rutan **Service Provider Issuer** .

    f.  Klicka på **Spara konfiguration**.
    
    > [!NOTE]
    > Om du har mer än en slack-instans som du måste integrera med Azure AD anger `https://<DOMAIN NAME>.slack.com` du till **tjänst leverantörens utfärdare** så att den kan kopplas till inställningen för Azure- **programidentifieraren** .

### <a name="create-slack-test-user"></a>Skapa Slack-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i slack. Slack har stöd för just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas vid ett försök att få åtkomst till Slack, om den inte redan finns. Slack stöder även automatisk användaretablering. Mer information finns [här](slack-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [Slack-supporten](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect är synkroniseringsprogrammet som kan synkronisera lokala Active Directory identiteter till Azure AD och dessa synkroniserade användare kan också använda programmen precis som andra moln användare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Slack-panelen i åtkomstpanelen bör du automatiskt loggas in på Slack som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova slack med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
