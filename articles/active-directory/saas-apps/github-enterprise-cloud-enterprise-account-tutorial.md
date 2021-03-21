---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med GitHub Enterprise Cloud-Enterprise Account | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub Enterprise Cloud-Enterprise-konto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650038"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med GitHub Enterprise Cloud-Enterprise-konto

I den här självstudien får du lära dig att integrera GitHub Enterprise Cloud-Enterprise-konto med Azure Active Directory (Azure AD). När du integrerar GitHub Enterprise Cloud-Enterprise-konto med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ett GitHub Enterprise-konto och organisationer inom företags kontot.


## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett [GitHub Enterprise-konto](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Ett GitHub-användarkonto som är en företags konto ägare. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* GitHub Enterprise Cloud-Enterprise-konto stöder **SP** -och **IDP** -initierad SSO.
* GitHub Enterprise Cloud-Enterprise-konto stöder **just-in-Time** User-etablering.

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Lägga till GitHub Enterprise Cloud-Enterprise-konto från galleriet

Om du vill konfigurera integrationen av GitHub Enterprise Cloud-Enterprise-kontot i Azure AD måste du lägga till GitHub Enterprise Cloud-Enterprise-konto från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **GitHub Enterprise Cloud-Enterprise Account** i sökrutan.
1. Välj **GitHub Enterprise Cloud – Enterprise Account** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurera och testa Azure AD SSO för GitHub Enterprise Cloud-Enterprise Account

Konfigurera och testa Azure AD SSO med GitHub Enterprise Cloud-Enterprise-konto med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i GitHub Enterprise Cloud-Enterprise-konto.

Utför följande steg för att konfigurera och testa Azure AD SSO med GitHub Enterprise Cloud-Enterprise-konto:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela din Azure AD-användare och test användar kontot till GitHub-appen](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** – om du vill aktivera ditt användar konto och testa användare `B.Simon` för att använda enkel inloggning med Azure AD.
1. **[Aktivera och testa SAML för företags kontot och dess organisationer](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Testa SSO med en annan ägare till företags kontot eller ett organisations medlems konto](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **GitHub Enterprise Cloud-Enterprise konto** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Ersätt `<ENTERPRISE-SLUG>` med det faktiska namnet på ditt GitHub Enterprise-konto.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateBase64.png)

1. I avsnittet **Konfigurera GitHub Enterprise Cloud-Enterprise Account** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i den Azure Portal som kallas `B.Simon` .

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Tilldela din Azure AD-användare och test användar kontot till GitHub-appen

I det här avsnittet ska du aktivera `B.Simon` och ditt användar konto för att använda enkel inloggning med Azure genom att bevilja åtkomst till GitHub Enterprise Cloud-Enterprise-konto.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **GitHub Enterprise Cloud-Enterprise Account**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** och ditt användar konto från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Aktivera och testa SAML för företags kontot och dess organisationer

Om du vill konfigurera enkel inloggning på **GitHub Enterprise Cloud-Enterprise-konto** sidan följer du stegen i [den här GitHub-dokumentationen](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Logga in på GitHub.com med ett användar konto som är [ägare till företags kontot](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Kopiera värdet från `Login URL` fältet i appen från Azure Portal och klistra in det i `Sign on URL` fältet i SAML-inställningarna för GitHub Enterprise-kontot. 
1. Kopiera värdet från `Azure AD Identifier` fältet i appen från Azure Portal och klistra in det i `Issuer` fältet i SAML-inställningarna för GitHub Enterprise-kontot. 
1. Kopiera innehållet i den **certifikat fil (base64)** som du laddade ned i stegen ovan från Azure Portal och klistra in dem i lämpligt fält i SAML-inställningarna för GitHub Enterprise-kontot. 
1. Klicka på `Test SAML configuration` och bekräfta att du kan autentisera från GitHub Enterprise-kontot till Azure AD.
1. När testet är klart sparar du inställningarna. 
1. Efter autentisering via SAML för första gången från GitHub Enterprise-kontot skapas en _länkad extern identitet_ i det GitHub Enterprise-konto som associerar det inloggade GitHub-användarkontot med Azure AD-användarkontot.  
 
När du har aktiverat SAML SSO för ditt GitHub Enterprise-konto är SAML SSO aktiverat som standard för alla organisationer som ägs av ditt företags konto. Alla medlemmar måste autentiseras med hjälp av SAML SSO för att få åtkomst till organisationer där de är medlemmar, och företags ägare måste autentiseras med hjälp av SAML SSO vid åtkomst till ett företags konto.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testa SSO med en annan ägare till företags kontot eller ett organisations medlems konto

När SAML-integrationen har kon figurer ATS för GitHub Enterprise-kontot (som även gäller för GitHub-organisationerna i företags kontot) bör andra företags konto ägare som är tilldelade till appen i Azure AD kunna navigera till GitHub Enterprise-URL ( `https://github.com/enterprises/<enterprise account>` ), autentisera via SAML och komma åt principerna och inställningarna under GitHub Enterprise-kontot. 

En organisations ägare för en organisation i ett företags konto bör kunna [bjuda in en användare att ansluta sig till sin GitHub-organisation](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Logga in på GitHub.com med ett organisations ägar konto och följ stegen i artikeln för att bjuda in `B.Simon` till organisationen. Ett GitHub-användarkonto måste skapas för `B.Simon` om det inte redan finns ett. 

Så här testar du GitHub organisations åtkomst under företags kontot med `B.Simon` test användar kontot:
1. Bjud in `B.Simon` till en organisation under företags kontot som organisations ägare. 
1. Logga in på GitHub.com med det användar konto som du vill länka till `B.Simon` användar kontot för Azure AD.
1. Logga in på Azure AD med hjälp av `B.Simon` användar kontot.
1. Gå till GitHub-organisationen. Användaren ska uppmanas att autentisera via SAML. Efter lyckad SAML-autentisering `B.Simon` bör kunna få åtkomst till organisations resurser. 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat GitHub Enterprise Cloud-Enterprise-kontot kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
