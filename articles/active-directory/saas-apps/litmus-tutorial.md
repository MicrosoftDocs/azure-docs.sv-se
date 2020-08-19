---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Litmus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2020
ms.author: jeedes
ms.openlocfilehash: 3e1386fdfafdea3112e26ce6480c7d20061236ba
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmus"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Litmus

I den här självstudien får du lära dig hur du integrerar Litmus med Azure Active Directory (Azure AD). När du integrerar Litmus med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Litmus.
* Gör det möjligt för användarna att logga in automatiskt till Litmus med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Litmus för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Litmus stöder **SP-och IDP** -INITIERAd SSO
* När du har konfigurerat Litmus kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-litmus-from-the-gallery"></a>Lägga till Litmus från galleriet

Om du vill konfigurera integreringen av Litmus i Azure AD måste du lägga till Litmus från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Litmus** i sökrutan.
1. Välj **Litmus** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-litmus"></a>Konfigurera och testa enkel inloggning med Azure AD för Litmus

Konfigurera och testa Azure AD SSO med Litmus med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Litmus.

Om du vill konfigurera och testa Azure AD SSO med Litmus, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LITMUS SSO](#configure-litmus-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Litmus test User](#create-litmus-test-user)** -om du vill ha en motsvarighet till B. Simon i Litmus som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Litmus** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://litmus.com/sessions/new`

1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera Litmus** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Litmus.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Litmus**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-litmus-sso"></a>Konfigurera Litmus SSO

1. Logga in på Litmus-programmet som administratör i ett annat webbläsarfönster.

1. Klicka på **säkerhet** på den vänstra navigerings panelen.

    ![Litmus-konfiguration](./media/litmus-tutorial/security-img.png)

1. I avsnittet **Konfigurera SAML-autentisering** utför du följande steg:

    ![Litmus-konfiguration](./media/litmus-tutorial/configure1.png)

    a. Växla till **Aktivera SAML** -växling.

    b. Välj **generisk** för providern.

    c. Ange namnet på **namnet på identitets leverantören**. för t. ex. `Azure AD`

1. Utför följande steg:

    ![Litmus-konfiguration](./media/litmus-tutorial/configure3.png)

    a. I text rutan **SAML 2,0-slutpunkt (http)** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    b. Öppna Hämtad **certifikat** fil från Azure Portal till anteckningar och klistra in innehållet i text rutan **X. 509-certifikat** .

    c. Klicka på **Spara SAML-inställningar**.

### <a name="create-litmus-test-user"></a>Skapa Litmus test användare

1. Logga in på Litmus-programmet som administratör i ett annat webbläsarfönster.

1. Klicka på **kontona** i den vänstra navigerings panelen.

    ![Litmus-konfiguration](./media/litmus-tutorial/accounts-img.png)

1. Klicka på **Lägg till ny användare** fliken.

    ![Litmus-konfiguration](./media/litmus-tutorial/add-new-user.png)

1. I avsnittet **Lägg till användare** utför du följande steg:

    ![Litmus-konfiguration](./media/litmus-tutorial/user-profile.png)

    a. I text rutan **e-post** anger du e-postadressen för användaren som **B.Simon@contoso.com**

    b. I text rutan för **förnamn** anger du det första namnet på användaren som **B**.

    c. I text rutan **efter namn** anger du användarens efter namn som **Simon**.

    d. Klicka på **Skapa användare**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Litmus på åtkomst panelen, bör du loggas in automatiskt på den Litmus som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Litmus med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Litmus med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
