---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mottagare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Envoy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.openlocfilehash: 1925ef48ee08a7b4f3b3b4c1b1319463d4134f6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mottagare

I den här självstudien får du lära dig hur du integrerar mottagare med Azure Active Directory (Azure AD). När du integrerar mottagare med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till mottagare.
* Gör det möjligt för användarna att logga in automatiskt till mottagare med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Mottagare för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Envoy har stöd för **SP**-initierad enkel inloggning

* Envoy har stöd för **just-in-time**-användaretablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-envoy-from-the-gallery"></a>Lägga till Envoy från galleriet

För att konfigurera integrering av Envoy i Azure AD behöver du lägga till Envoy från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **mottagare** i sökrutan.
1. Välj **mottagare** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-envoy"></a>Konfigurera och testa enkel inloggning med Azure AD för mottagare

Konfigurera och testa Azure AD SSO med mottagare med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i mottagare.

Om du vill konfigurera och testa Azure AD SSO med mottagare, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera mottagare SSO](#configure-envoy-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa mottagare test User](#create-envoy-test-user)** -om du vill ha en motsvarighet till B. Simon i mottagare som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **mottagare** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för Envoy](https://envoy.com/contact/) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera mottagare** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till mottagare.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Envoy**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-envoy-sso"></a>Konfigurera mottagare SSO

1. Om du vill automatisera konfigurationen i mottagare måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations mottagare** för att dirigera dig till mottagare-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på mottagare. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera mottagare manuellt öppnar du ett nytt webbläsarfönster och loggar in på din mottagare-företags webbplats som administratör och utför följande steg:

4. I verktygsfältet längst upp klickar du på **Inställningar**.

    ![Envoy](./media/envoy-tutorial/ic776782.png "Envoy")

5. Klicka på **Företag**.

    ![Firm](./media/envoy-tutorial/ic776783.png "Företag")

6. Klicka på **SAML**.

    ![SAML](./media/envoy-tutorial/ic776784.png "SAML")

7. I avsnittet för konfiguration av **SAML-autentisering** utför du följande steg:

    ![SAML-autentisering](./media/envoy-tutorial/ic776785.png "SAML-autentisering")
    
    >[!NOTE]
    >Värdet för HQ-plats-ID genereras automatiskt av programmet.
    
    a. I textrutan **Fingeravtryck** klistrar du in värdet för det **Tumavtryck** för certifikatet som du har kopierat från Azure-portalen.
    
    b. Klistra in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen i textrutan **IDENTITY PROVIDER HTTP SAML URL** (identitetsproviderns HTTP SAML-URL).
    
    c. Klicka på **Spara ändringar**.

### <a name="create-envoy-test-user"></a>Skapa Envoy-testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i mottagare. Mottagare stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i mottagare skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Envoy-panelen i åtkomstpanelen bör du automatiskt loggas in på Envoy som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova mottagare med Azure AD](https://aad.portal.azure.com/)

