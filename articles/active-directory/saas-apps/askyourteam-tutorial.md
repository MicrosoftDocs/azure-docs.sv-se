---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AskYourTeam | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: ad68208f8993ce84c71c97d97de7e2b090f1e7da
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AskYourTeam

I den här självstudien får du lära dig hur du integrerar AskYourTeam med Azure Active Directory (Azure AD). När du integrerar AskYourTeam med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AskYourTeam.
* Gör det möjligt för användarna att logga in automatiskt till AskYourTeam med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* AskYourTeam för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* AskYourTeam stöder **SP-och IDP** -initierad SSO.
* När du har konfigurerat AskYourTeam kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Lägga till AskYourTeam från galleriet

Om du vill konfigurera integreringen av AskYourTeam i Azure AD måste du lägga till AskYourTeam från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **AskYourTeam** i sökrutan.
1. Välj **AskYourTeam** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Konfigurera och testa enkel inloggning med Azure AD för AskYourTeam

Konfigurera och testa Azure AD SSO med AskYourTeam med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AskYourTeam.

Om du vill konfigurera och testa Azure AD SSO med AskYourTeam, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ASKYOURTEAM SSO](#configure-askyourteam-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa AskYourTeam test User](#create-askyourteam-test-user)** -om du vill ha en motsvarighet till B. Simon i AskYourTeam som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **AskYourTeam** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska svars-URL: en och inloggnings-URL-värden som beskrivs senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera AskYourTeam** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till AskYourTeam.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **AskYourTeam**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-askyourteam-sso"></a>Konfigurera AskYourTeam SSO

1. Logga in på AskYourTeam-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **min organisation**.

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/user1.png)

1. Klicka på **integreringar**.

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/configure1.png)

1. Klicka på **Redigera inställningar**.

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/configure2.png)

1. Utför följande steg på sidan **Redigera enkel inloggnings integrering** : 

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/configure3.png)

    a. I text rutan för **SAML-tjänsten för enkel inloggnings-URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    b. I text rutan **SAML-entitets-ID** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.

    c. I text rutan för utloggnings **-URL** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.

    d. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan för **SAML-signerings certifikat – base64** .

    > [!NOTE]
    > Alternativt kan du också ladda upp **XML-** filen med federationsmetadata genom att klicka på alternativet **Välj fil** .

    e. Kopiera **svars-URL (intyg om mottagar tjänstens URL)** värde, klistra in det här värdet i text rutan **SVARs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    f. Kopiera **inloggnings-URL** -värde, klistra in det här värdet i text rutan **INLOGGNINGs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ex. Klicka på **Spara**.

### <a name="create-askyourteam-test-user"></a>Skapa AskYourTeam test användare

1. Logga in på AskYourTeam-webbplatsen som administratör i ett annat webbläsarfönster.

1. Klicka på **min organisation**.

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/user1.png)

1. Klicka på **användare** och välj **ny användare**.

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/user2.png)

1. Utför följande steg i avsnittet **ny användare** :

    ![AskYourTeam-konfiguration](./media/askyourteam-tutorial/user3.png)

    1. I text rutan för det **första namnet** anger du användarens förnamn.

    1. I text rutan **efter namn** anger du användarens efter namn.

    1. I text rutan för **e-post** anger du EmailAddress för den användare som B.Simon@contoso.com .

    1. Välj **rollen** för användaren enligt organisationens krav.

    1. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen AskYourTeam på åtkomst panelen, bör du loggas in automatiskt på den AskYourTeam som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova AskYourTeam med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
