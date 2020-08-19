---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med NS1 SSO för Azure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NS1 SSO för Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: d0fee044506a9a19e09478ef8d70b3719ecc167a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554268"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med NS1 SSO för Azure

I den här självstudien får du lära dig hur du integrerar NS1 SSO för Azure med Azure Active Directory (Azure AD). När du integrerar NS1 SSO för Azure med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NS1 SSO för Azure.
* Gör det möjligt för användarna att logga in automatiskt till NS1 SSO för Azure med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* NS1 SSO för Azure Single Sign-on (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* NS1 SSO för Azure stöder SP-och IDP-initierad SSO.
* När du har konfigurerat NS1 SSO för Azure kan du framtvinga kontroll av sessionen. Detta skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Lägg till NS1 SSO för Azure från galleriet

Om du vill konfigurera integrationen av NS1 SSO för Azure i Azure AD måste du lägga till NS1 SSO för Azure från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ns1 SSO för Azure** i sökrutan.
1. Välj **ns1 SSO för Azure** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurera och testa enkel inloggning med Azure AD för NS1 SSO för Azure

Konfigurera och testa Azure AD SSO med NS1 SSO för Azure med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera upprättar du en länkad relation mellan en Azure AD-användare och den relaterade användaren i NS1 SSO för Azure.

Här följer allmänna steg för att konfigurera och testa Azure AD SSO med NS1 SSO för Azure:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.

    a. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.

    b. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera ns1 SSO för Azure SSO](#configure-ns1-sso-for-azure-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.

    a. **[Skapa en NS1 SSO för Azure test User](#create-an-ns1-sso-for-azure-test-user)** för att ha en motsvarighet till B. Simon i ns1 SSO för Azure. Den här motsvarigheten är länkad till användarens Azure AD-representation.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **ns1 SSO för Azure** application integration och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML Page med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. Skriv följande URL i text rutan **identifierare** : `https://api.nsone.net/saml/metadata`

    b. I text rutan **svars-URL** anger du en URL som använder följande mönster: `https://api.nsone.net/saml/sso/<ssoid>`

1. Välj **Ange ytterligare URL: er**och utför följande steg om du vill konfigurera programmet i **SP** -initierat läge:

    Skriv följande URL i text rutan **inloggnings-URL** :  `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Svars-URL-värdet är inte verkligt. Uppdatera värdet för svars-URL med den faktiska svars-URL:en. Kontakta [ns1 SSO för Azure client support-teamet](mailto:techops@nsone.net) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NS1 SSO för Azure-programmet förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för de här attributen från avsnittet **användarattribut &-anspråk** på sidan program integration. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för att öppna dialog rutan **användarattribut** .

    ![Skärm bild av avsnittet användarattribut &-anspråk med Penn ikonen markerad](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Välj attributnamnet för att redigera anspråket.

    ![Skärm bild av avsnittet användarattribut &-anspråk med ett attributnamn markerat](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Välj **omvandling**.

    ![Skärm bild av avsnittet hantera anspråk med omvandling markerat](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. I avsnittet **Hantera omvandling** utför du följande steg:

    ![Skärm bild av avsnittet hantera omvandling, med olika fält markerade](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Välj **ExactMailPrefix ()** som **omvandling**.

    1. Välj **User. UserPrincipalName** som **parameter 1**.

    1. Välj **Lägg till**.

    1. Välj **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du knappen Kopiera. Detta kopierar **URL: en för appens Federations-metadata** och sparar den på din dator.

    ![Skärm bild av SAML-signerings certifikat med knappen Kopiera markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:

   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i fältet **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NS1 SSO för Azure.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **ns1 SSO för Azure**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurera NS1 SSO för Azure SSO

Om du vill konfigurera enkel inloggning på NS1 SSO för Azure-sidan måste du skicka URL: en för appens Federations-metadata till [ns1 SSO för Azure support team](mailto:techops@nsone.net). De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Skapa en NS1 SSO för Azure test User

I det här avsnittet skapar du en användare som heter B. Simon i NS1 SSO för Azure. Arbeta med NS1 SSO för Azure support team för att lägga till användarna i NS1 SSO för Azure-plattformen. Du kan inte använda enkel inloggning förrän du har skapat och aktiverat användare.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer NS1 SSO för Azure på åtkomst panelen, bör du loggas in automatiskt på NS1 SSO för Azure som du ställer in SSO för. Mer information finns i [Introduktion till åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova NS1 SSO för Azure med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)