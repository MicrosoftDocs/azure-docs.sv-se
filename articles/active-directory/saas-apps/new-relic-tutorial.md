---
title: 'Självstudie: Azure Active Directory integration med nya Relic efter konto | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nya Relic efter konto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 19a471b9339828250baa190b1de57c86eb22b30c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554472"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nya Relic efter konto

I den här självstudien får du lära dig att integrera nya Relic efter konto med Azure Active Directory (Azure AD). När du integrerar nya Relic per konto med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till nya Relic efter konto.
* Gör det möjligt för användarna att logga in automatiskt till nya Relic per konto med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Nytt Relic-prenumeration med enkel inloggning (SSO) för konto.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Nya Relic efter konto stöder **SP** -INITIERAd SSO

* När du har konfigurerat den nya Relic med-kontot kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Lägga till nya Relic efter konto från galleriet

Om du vill konfigurera integrering av nya Relic per konto i Azure AD måste du lägga till nya Relic efter konto från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du in **New Relic efter konto** i rutan Sök.
1. Välj **nytt Relic efter konto** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Konfigurera och testa enkel inloggning med Azure AD för nya Relic efter konto

Konfigurera och testa Azure AD SSO med nya Relic per konto med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i nya Relic efter konto.

Om du vill konfigurera och testa Azure AD SSO med nya Relic per konto, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera nya Relic efter konto SSO](#configure-new-relic-by-account-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa ny Relic efter konto test användare](#create-new-relic-by-account-test-user)** – för att få en motsvarighet till B. Simon i nya Relic efter konto som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **ny Relic efter konto** program integration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)
1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` – Var noga med att ersätta `acc_id` med ditt eget konto-ID för nya Relic efter konto.

    b. I text rutan **identifierare (enhets-ID)** anger du en URL: `rpm.newrelic.com`

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera nya Relic efter konto** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till nya Relic per konto.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **nytt Relic efter konto**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-new-relic-by-account-sso"></a>Konfigurera nya Relic per konto-SSO

1. Logga in på ditt **nya Relic av kontots** företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **konto inställningar**på menyn högst upp.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797036.png "Konto inställningar")

3. Klicka på fliken **säkerhet och autentisering** och klicka sedan på fliken **enkel inloggning** .
   
    ![Enkel inloggning](./media/new-relic-tutorial/ic797037.png "för Aha!")

4. Utför följande steg på sidan SAML-dialog:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. Klicka på **Välj fil** för att ladda upp ditt hämtade Azure Active Directory certifikat.

    b. I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.
   
    c. I text rutan **Logga ut landnings-URL** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

    d. Klicka på **Spara mina ändringar**.

### <a name="create-new-relic-by-account-test-user"></a>Skapa en ny Relic efter konto test användare

1. Logga in på den **nya Relic av kontots** företags webbplats som administratör.

2. Klicka på **konto inställningar**på menyn högst upp.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797040.png "Konto inställningar")

3. I fönstret **konto** till vänster klickar du på **Sammanfattning**och sedan på **Lägg till användare**.
   
    ![Konto inställningar](./media/new-relic-tutorial/ic797041.png "Konto inställningar")

4. I dialog rutan **aktiva användare** utför du följande steg:
   
    ![Aktiva användare](./media/new-relic-tutorial/ic797042.png "Aktiva användare")
   
    a. I text rutan **e-post** anger du e-postadressen till en giltig Azure Active Directory användare som du vill etablera.

    b. Som **roll** väljer du **användare**.

    c. Klicka på **Lägg till den här användaren**.

> [!NOTE]
> Du kan använda andra nya Relic genom att skapa verktyg för användar konton eller API: er som tillhandahålls av New Relic efter konto för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen nytt Relic efter konto på åtkomst panelen, bör du loggas in automatiskt på det nya Relic efter konto som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa nya Relic efter konto med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)