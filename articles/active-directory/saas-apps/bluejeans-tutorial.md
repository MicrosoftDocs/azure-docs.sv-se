---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BlueJeans för Azure AD | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BlueJeans för Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: 94b8a26a398ad8604a469486185d805fcbb6a8b2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BlueJeans för Azure AD

I den här självstudien får du lära dig hur du integrerar BlueJeans för Azure AD med Azure Active Directory (Azure AD). När du integrerar BlueJeans för Azure AD med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BlueJeans för Azure AD.
* Gör det möjligt för användarna att logga in automatiskt till BlueJeans för Azure AD med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* BlueJeans för Azure AD-aktiverad prenumeration med enkel inloggning (SSO).

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* BlueJeans för Azure AD stöder **SP** -INITIERAd SSO

* BlueJeans för Azure AD har stöd för [ **Automatisk** användar etablering](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Lägga till BlueJeans för Azure AD från galleriet

Om du vill konfigurera integreringen av BlueJeans för Azure AD i Azure AD måste du lägga till BlueJeans för Azure AD från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **BLUEJEANS för Azure AD** i sökrutan.
1. Välj **BlueJeans för Azure AD** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurera och testa enkel inloggning med Azure AD för BlueJeans för Azure AD

Konfigurera och testa Azure AD SSO med BlueJeans för Azure AD med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i BlueJeans för Azure AD.

Om du vill konfigurera och testa Azure AD SSO med BlueJeans för Azure AD, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BlueJeans för Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa BlueJeans för Azure AD-testanvändare](#create-bluejeans-for-azure-ad-test-user)** – om du vill ha en motsvarighet till B. Simon i BlueJeans för Azure AD som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **BLUEJEANS för Azure AD** -programintegration, hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.bluejeans.com`

    a. I text rutan **identifierare (enhets-ID)** anger du en URL: `http://samlsp.bluejeans.com`

    a. Skriv en URL i textrutan **Svars-URL**: `https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [BlueJeans för Azure AD client support-teamet](https://support.bluejeans.com/contact) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. BlueJeans-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig BlueJeans-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ---------| --------- |
    | Telefon | user.telephonenumber |
    | rubrik | user.jobtitle |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera BlueJeans för Azure AD** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BlueJeans för Azure AD.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **BlueJeans för Azure AD**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurera BlueJeans för Azure AD SSO

1. Logga in på din **BlueJeans för Azure AD** -företags platsen som administratör i ett annat webbläsarfönster.

2. Gå till **ADMIN \>GRUPPINSTÄLLNINGAR \> SÄKERHET**.

    ![Administratör](./media/bluejeans-tutorial/ic785868.png "Administratör")

3. I avsnittet **SÄKERHET** utför du följande steg:

    ![SAML enkel inloggning](./media/bluejeans-tutorial/ic785869.png "SAML enkel inloggning")

    a. Välj **SAML Single Sign On** (SAML enkel inloggning).

    b. Välj **aktivera automatisk etablering**.

4. Gå vidare med följande steg:

    ![Certifikat Sök väg](./media/bluejeans-tutorial/ic785870.png "Certifikat Sök väg")

    a. Klicka på **Välj fil** för att ladda upp det base-64-kodade certifikatet som du har laddat ned från Azure-portalen.

    b. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. I textrutan **Password Change URL** (URL för Byt lösenord) klistrar du in värdet för **URL:en för Byt lösenord** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

5. Gå vidare med följande steg:

    ![Spara ändringar](./media/bluejeans-tutorial/ic785874.png "Spara ändringar")

    a. Skriv i text rutan **användar-ID** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    b. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Skapa BlueJeans för Azure AD-test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i BlueJeans för Azure AD. BlueJeans för Azure AD har stöd för automatisk användar etablering, som är aktiverat som standard. Du hittar mer information [här](bluejeans-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

1. Logga in på din **BlueJeans för Azure AD** -företags platsen som administratör.

2. Gå till **ADMIN \> HANTERA ANVÄNDARE \> LÄGG TILL ANVÄNDARE**.

    ![Administratör](./media/bluejeans-tutorial/ic785877.png "Administratör")

    > [!IMPORTANT]
    > Fliken **LÄGG TILL ANVÄNDARE** är endast tillgänglig om **Aktivera automatisk etablering** är avmarkerat på fliken **SÄKERHET**.

3. I avsnittet **Lägg till användare** utför du följande steg:

    ![Lägg till användare](./media/bluejeans-tutorial/ic785886.png "Lägg till användare")

    a. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **B**.

    b. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    c. I text rutan **Välj en BlueJeans för Azure AD-användarnamn** anger du användar namnet som **Brittasimon**

    d. I textrutan **Skapa ett lösenord** anger du ditt lösenord.

    e. I textrutan **Företag** skriver du ditt företag.

    f. I textrutan **E-postadress** anger du användarens e-postadress, som `b.simon\@contoso.com`.

    ex. Ange ditt mötes-ID i text rutan **skapa en BlueJeans för Azure AD-möte i. D** .

    h. I textrutan för att **välja ett lösenord för moderator** anger du ditt lösenord.

    i. Klicka på **Fortsätt**.

    ![Lägg till användare](./media/bluejeans-tutorial/ic785887.png "Lägg till användare")

    J. Klicka på **LÄGG TILL ANVÄNDARE**.

> [!NOTE]
> Du kan använda andra BlueJeans för verktyg för att skapa Azure AD-användarkonto eller API: er från BlueJeans för Azure AD för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen BlueJeans för Azure AD på åtkomst panelen, bör du loggas in automatiskt på BlueJeans för Azure AD som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa BlueJeans för Azure AD med Azure AD](https://aad.portal.azure.com/)
