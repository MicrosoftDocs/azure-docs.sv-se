---
title: 'Självstudie: Azure Active Directory integration med Secret Server (On-Premises) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Secret Server (On-Premises).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: eeaf6917187c9688bf6a62b98b2fcf64519b1798
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543235"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Självstudie: integrera Secret Server (On-Premises) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Secret Server (On-Premises) med Azure Active Directory (Azure AD). När du integrerar Secret Server (On-Premises) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Secret Server (On-Premises).
* Gör det möjligt för användarna att logga in automatiskt för att Secret Server (On-Premises) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Secret Server (On-Premises) enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Secret Server (On-Premises) stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Lägga till Secret Server (On-Premises) från galleriet

Om du vill konfigurera integreringen av Secret Server (On-Premises) i Azure AD måste du lägga till Secret Server (On-Premises) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Secret Server (on-premises)** i sökrutan.
1. Välj **Secret Server (on-premises)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Secret Server (On-Premises) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Secret Server (On-Premises).

Om du vill konfigurera och testa Azure AD SSO med Secret Server (On-Premises) slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Secret Server (on-PREMISES) SSO](#configure-secret-server-on-premises-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa Secret Server (on-premises) testa användare](#create-secret-server-on-premises-test-user)** – om du vill ha en motsvarighet till B. Simon i Secret Server (on-premises) som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för **Secret Server (on-premises)** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du det valda värdet för användaren som exempel: `https://secretserveronpremises.azure`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Det entitets-ID som visas ovan är bara ett exempel och du kan välja ett unikt värde som identifierar din hemliga Server instans i Azure AD. Du måste skicka det här entitets-ID: t till [Secret Server (on-premises) klient support teamet](https://thycotic.force.com/support/s/) och de konfigurerar det på deras sida. Läs [den här artikeln](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)om du vill ha mer information.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Secret Server (on-premises) klient support teamet](https://thycotic.force.com/support/s/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **Redigera** -ikonen för att öppna dialog rutan för **SAML-signerings certifikat** .

    ![Signerings alternativ](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Välj **signerings alternativ** som **signerat SAML-svar och kontroll**.

    ![Signerings alternativ](./media/secretserver-on-premises-tutorial/signing-option.png)

1. I avsnittet **konfigurera Secret Server (on-premises)** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Konfigurera Secret Server (On-Premises) SSO

Om du vill konfigurera enkel inloggning på **Secret Server (on-premises)** sidan måste du skicka det nedladdade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Secret Server (on-premises) support teamet](https://thycotic.force.com/support/s/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Secret Server (On-Premises).

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Secret Server (on-premises)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-secret-server-on-premises-test-user"></a>Skapa Secret Server (On-Premises) test användare

I det här avsnittet skapar du en användare som kallas Britta Simon i Secret Server (On-Premises). Arbeta med [Secret Server (on-premises) support team](https://thycotic.force.com/support/s/) för att lägga till användare i Secret Server (on-premises)s plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Secret Server (On-Premises) på åtkomst panelen, bör du loggas in automatiskt på den Secret Server (On-Premises) som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)