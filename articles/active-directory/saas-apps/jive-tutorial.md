---
title: 'Självstudie: Azure Active Directory integrering med jives | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och jives.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 432ca72a667caa9c2a78b08396884d957b4c650b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547111"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med jives

I den här självstudien får du lära dig hur du integrerar jives med Azure Active Directory (Azure AD). När du integrerar jives med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till jives.
* Gör det möjligt för användarna att logga in automatiskt till jives med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Jives för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Jives stöder **SP** -INITIERAd SSO
* Jives har stöd för [ **Automatisk** användar etablering](jive-provisioning-tutorial.md)
* När du har konfigurerat jives kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Lägga till jives från galleriet

Om du vill konfigurera integreringen av jives i Azure AD måste du lägga till jives från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **jives** i sökrutan.
1. Välj **jives** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Konfigurera och testa enkel inloggning med Azure AD för jives

Konfigurera och testa Azure AD SSO med jives med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i jives.

Om du vill konfigurera och testa Azure AD SSO med jives, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera JIVES SSO](#configure-jive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa jives test User](#create-jive-test-user)** -om du vill ha en motsvarighet till B. Simon i jives som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **jives** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

   a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instance name>.jivecustom.com`

   b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [jives client support team](https://www.jivesoftware.com/services-support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera jives** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till jives.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **jives**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-jive-sso"></a>Konfigurera jives SSO

1. Om du vill konfigurera enkel inloggning på **jives** -sidan loggar du in på jives-klienten som administratör.

1. Klicka på **SAML**på menyn högst upp.

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_002.png)

    a. Välj **aktive rad** på fliken **Allmänt** .

    b. Klicka på knappen **Spara alla SAML-inställningar** .

1. Gå till fliken **IDP metadata** .

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_003.png)

    a. Kopiera innehållet i XML-filen med hämtade metadata och klistra sedan in den i text rutan **identitets leverantör (IdP) metadata** .

    b. Klicka på knappen **Spara alla SAML-inställningar** .

1. Välj fliken **Mappning av användar-attribut** .

    ![Konfigurera enkel inloggning på appsidan](./media/jive-tutorial/tutorial_jive_004.png)

    a. Kopiera och klistra in attributnamnet för **e** -postvärdet i text rutan **e-post** .

    b. Kopiera och klistra in attributnamnet för **givenName** -värde i text rutan för **förnamn** .

    c. I text rutan **efter namn** kopierar och klistrar du in attributnamnet för **värdet efter namn.**

### <a name="create-jive-test-user"></a>Skapa jives test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i jives. Jives stöder automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](jive-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

Om du behöver skapa användare manuellt arbetar du med [jives-klientens support team](https://www.jivesoftware.com/services-support/) för att lägga till användarna i jives-plattformen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen jives på åtkomst panelen, bör du loggas in automatiskt på den jives som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova jives med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Konfigurera användar etablering](jive-provisioning-tutorial.md)

- [Så här skyddar du jives med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
