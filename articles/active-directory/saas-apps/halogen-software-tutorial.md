---
title: 'Självstudie: Azure Active Directory integrering med Saba TalentSpace | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 1743908652a70667a368dced18b2e808ce590a1b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Saba TalentSpace

I den här självstudien får du lära dig att integrera Saba-TalentSpace med Azure Active Directory (Azure AD). När du integrerar Saba-TalentSpace med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Saba-TalentSpace.
* Gör det möjligt för användarna att logga in automatiskt till Saba TalentSpace med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Saba TalentSpace-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Saba TalentSpace stöder **SP** -INITIERAd SSO
* När du har konfigurerat Saba TalentSpace kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Lägga till Saba TalentSpace från galleriet

Om du vill konfigurera integreringen av Saba-TalentSpace i Azure AD måste du lägga till Saba TalentSpace från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Saba TalentSpace** i sökrutan.
1. Välj **Saba TalentSpace** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Konfigurera och testa enkel inloggning med Azure AD för Saba-TalentSpace

Konfigurera och testa Azure AD SSO med Saba TalentSpace med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Saba TalentSpace.

Om du vill konfigurera och testa Azure AD SSO med Saba TalentSpace slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Saba TALENTSPACE SSO](#configure-saba-talentspace-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Saba TalentSpace test User](#create-saba-talentspace-test-user)** – om du vill ha en motsvarighet till B. Simon i Saba TalentSpace som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Saba TalentSpace** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://global.hgncloud.com/[companyname]/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. I text rutan **svars-URL: en (intygs mottagar tjänst)** anger du en URL med följande mönster: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Saba TalentSpace client support team](https://support.saba.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Saba TalentSpace** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Saba TalentSpace.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Saba TalentSpace**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-saba-talentspace-sso"></a>Konfigurera Saba TalentSpace SSO

1. Logga in på ditt **Saba TalentSpace** -program som administratör i ett annat webbläsarfönster.

2. Klicka på fliken **Alternativ**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. I det vänstra navigeringsfönstret klickar du på **SAML-konfiguration**.
  
    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. I dialogrutan **SAML-konfiguration** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. För **Unik identifierare** väljer du **NameID**.

    b. För **Unique Identifier Maps To** (Unik identifierare mappar till) väljer du **Användarnamn**.
  
    c. Om du vill ladda upp din nedladdade metadatafil klickar du på **Bläddra** för att välja filen och sedan på **Ladda upp fil**.

    d. Testa konfigurationen genom att klicka på **Kör test**.

    > [!NOTE]
    > Du måste vänta på meddelandet "SAML-*testet har slutförts. Stäng det här fönstret*. Stäng sedan det öppnade webbläsarfönstret. Kryssrutan **Aktivera SAML** är bara tillgänglig om testet har slutförts.

    e. Välj **Aktivera SAML**.

    f. Klicka på **Spara ändringar**.

### <a name="create-saba-talentspace-test-user"></a>Skapa Saba TalentSpace-test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Saba TalentSpace.

**Utför följande steg för att skapa en användare med namnet Britta Simon i Saba TalentSpace:**

1. Logga in på ditt **Saba TalentSpace** -program som administratör.

2. Klicka på fliken **User Center** (Användarcenter) och sedan på **Skapa användare**.

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. I dialogrutan **Ny användare** utför du följande steg:

    ![Vad är Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. I text rutan **förnamn** skriver du förnamn för användaren som **B**.

    b. I text rutan **efter namn** skriver du efter namnet på användaren som **Simon**.

    c. I text rutan **användar namn** skriver du **B. Simon**, användar namnet som i Azure Portal.

    d. I text rutan **lösen ord** skriver du ett lösen ord för B. Simon.

    e. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Saba TalentSpace på åtkomst panelen, bör du loggas in automatiskt på Saba-TalentSpace som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Saba TalentSpace med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)