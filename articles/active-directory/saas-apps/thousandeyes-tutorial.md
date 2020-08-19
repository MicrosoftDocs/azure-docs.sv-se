---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ThousandEyes | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: 2b99d5ea33693431fb3811af50385891684a366e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546465"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ThousandEyes

I den här självstudien får du lära dig hur du integrerar ThousandEyes med Azure Active Directory (Azure AD). När du integrerar ThousandEyes med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ThousandEyes.
* Gör det möjligt för användarna att logga in automatiskt till ThousandEyes med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ThousandEyes för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ThousandEyes stöder **SP-och IDP** -INITIERAd SSO
* ThousandEyes har stöd för [ **Automatisk** användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-thousandeyes-from-the-gallery"></a>Lägga till ThousandEyes från galleriet

Om du vill konfigurera integreringen av ThousandEyes i Azure AD måste du lägga till ThousandEyes från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **ThousandEyes** i sökrutan.
1. Välj **ThousandEyes** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Konfigurera och testa enkel inloggning med Azure AD för ThousandEyes

Konfigurera och testa Azure AD SSO med ThousandEyes med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ThousandEyes.

Om du vill konfigurera och testa Azure AD SSO med ThousandEyes, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera THOUSANDEYES SSO](#configure-thousandeyes-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa ThousandEyes test User](#create-thousandeyes-test-user)** -om du vill ha en motsvarighet till B. Simon i ThousandEyes som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **ThousandEyes** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.thousandeyes.com/login/sso`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera ThousandEyes** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ThousandEyes.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ThousandEyes**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-thousandeyes-sso"></a>Konfigurera ThousandEyes SSO

1. Logga in på din **ThousandEyes** -företags webbplats som administratör i ett annat webbläsarfönster.

2. På menyn längst upp klickar du på **Inställningar**.

    ![Inställningar](./media/thousandeyes-tutorial/ic790066.png "Inställningar")

3. Klicka på **konto**

    ![Konto](./media/thousandeyes-tutorial/ic790067.png "Konto")

4. Klicka på fliken **säkerhets & autentisering** .

    ![Autentisering av säkerhets &](./media/thousandeyes-tutorial/ic790068.png "Autentisering av säkerhets &")

5. Utför följande steg i avsnittet **Konfigurera enkel inloggning** :

    ![Konfigurera enkel inloggning](./media/thousandeyes-tutorial/ic790069.png "Konfigurera enkel inloggning")

    a. Välj **aktivera enkel inloggning**.

    b. I text rutan **URL för inloggnings sida** , klistra in **inloggnings-URL**, som du har kopierat från Azure Portal.

    c. I text rutan URL för inloggnings **sida** , klistra in **URL**för att klistra in, som du har kopierat från Azure Portal.

    d. Text ruta för **identitets leverantör** , klistra in **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    e. I **verifierings certifikat**klickar du på **Välj fil**och överför sedan det certifikat som du har laddat ned från Azure Portal.

    f. Klicka på **Spara**.

### <a name="create-thousandeyes-test-user"></a>Skapa ThousandEyes test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i ThousandEyes. ThousandEyes stöder automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](thousandeyes-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din ThousandEyes-företags webbplats som administratör.

2. Klicka på **Inställningar**.

    ![Inställningar](./media/thousandeyes-tutorial/IC790066.png "Inställningar")

3. Klicka på **Konto**.

    ![Konto](./media/thousandeyes-tutorial/IC790067.png "Konto")

4. Klicka på fliken **konton & användare** .

    ![Konton & användare](./media/thousandeyes-tutorial/IC790073.png "Konton & användare")

5. I avsnittet **Lägg till användare & konton** utför du följande steg:

    ![Lägg till användar konton](./media/thousandeyes-tutorial/IC790074.png "Lägg till användar konton")

    a. I text rutan **namn** skriver du namnet på användaren, t. ex. **B. Simon**.

    b. Skriv e-postmeddelandets e-postadress i rutan **e-post** b.simon@contoso.com .

    b. Klicka på **Lägg till ny användare till konto**.

    > [!NOTE]
    > Azure Active Directory konto innehavaren får ett e-postmeddelande med en länk för att bekräfta och aktivera kontot.

> [!NOTE]
> Du kan använda andra verktyg för ThousandEyes av användar konton eller API: er som tillhandahålls av ThousandEyes för att etablera Azure Active Directory användar konton.


## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ThousandEyes på åtkomst panelen, bör du loggas in automatiskt på den ThousandEyes som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ThousandEyes med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)