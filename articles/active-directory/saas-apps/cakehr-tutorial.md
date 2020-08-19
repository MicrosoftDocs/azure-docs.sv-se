---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CakeHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CakeHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 2884302ae636739243e0277fa49ef6889b077c86
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547604"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CakeHR

I den här självstudien får du lära dig hur du integrerar CakeHR med Azure Active Directory (Azure AD). När du integrerar CakeHR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CakeHR.
* Gör det möjligt för användarna att logga in automatiskt till CakeHR med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CakeHR för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CakeHR stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-cakehr-from-the-gallery"></a>Lägga till CakeHR från galleriet

Om du vill konfigurera integreringen av CakeHR i Azure AD måste du lägga till CakeHR från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **CakeHR** i sökrutan.
1. Välj **CakeHR** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Konfigurera och testa enkel inloggning med Azure AD för CakeHR

Konfigurera och testa Azure AD SSO med CakeHR med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CakeHR.

Om du vill konfigurera och testa Azure AD SSO med CakeHR, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CAKEHR SSO](#configure-cakehr-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa CakeHR test User](#create-cakehr-test-user)** -om du vill ha en motsvarighet till B. Simon i CakeHR som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **CakeHR** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<yourcakedomain>.cake.hr/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [CakeHR client support team](mailto:info@cake.hr) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du värdet **tumavtryck** och sparar det i anteckningar.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera CakeHR** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CakeHR.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **CakeHR**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cakehr-sso"></a>Konfigurera CakeHR SSO

1. Om du vill automatisera konfigurationen i CakeHR måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera CakeHR** för att dirigera dig till CakeHR-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på CakeHR. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera CakeHR manuellt öppnar du ett nytt webbläsarfönster och loggar in på din CakeHR-företags webbplats som administratör och utför följande steg:

1. I det övre högra hörnet på sidan klickar du på **profil** och navigerar sedan till **Inställningar**.

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config01.png)

1. Klicka på **integration**i SAML SSO på vänster sida av meny raden  >  **SAML SSO** och utför följande steg:

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config02.png)

    a. I text rutan **entitets-ID** skriver du `cake.hr` .

    b. I text rutan **URL för autentisering** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. I text rutan **Key finger avtryck (SHA1-format)** klistrar du in det **tumavtryck** -värde som du har kopierat från Azure Portal.

    d. Markera kryss rutan **aktivera enkel inloggning** .

    e. Klicka på **Spara**.

### <a name="create-cakehr-test-user"></a>Skapa CakeHR test användare

Om du vill att Azure AD-användare ska kunna logga in på CakeHR måste de tillhandahållas i CakeHR. I CakeHR är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på CakeHR som säkerhets administratör.

2. Klicka på **företags**tillägg på vänster sida av meny raden  >  **ADD**.

    ![CakeHR-konfiguration](./media/cakehr-tutorial/config03.png)

3. Utför följande steg på popup-fönstret **Lägg till ny medarbetare** :

     ![CakeHR-konfiguration](./media/cakehr-tutorial/config04.png)

    a. I text rutan **fullständigt namn** anger du namnet på användaren, t. ex. B. Simon.

    b. I text rutan **arbets-e-postadress** anger du e-postadressen till användaren `B.Simon@contoso.com` .

    c. Klicka på **Skapa konto**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen CakeHR på åtkomst panelen, bör du loggas in automatiskt på den CakeHR som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CakeHR med Azure AD](https://aad.portal.azure.com/)
