---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med TextMagic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TextMagic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 81dc3a4f55b214ea6ca7ad0388e57f4b4fdc7124
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546482"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med TextMagic

I den här självstudien får du lära dig hur du integrerar TextMagic med Azure Active Directory (Azure AD). När du integrerar TextMagic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TextMagic.
* Gör det möjligt för användarna att logga in automatiskt till TextMagic med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* TextMagic för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* TextMagic stöder **IDP** INITIERAd SSO

* TextMagic stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-textmagic-from-the-gallery"></a>Lägga till TextMagic från galleriet

Om du vill konfigurera integreringen av TextMagic i Azure AD måste du lägga till TextMagic från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **TextMagic** i sökrutan.
1. Välj **TextMagic** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Konfigurera och testa enkel inloggning med Azure AD för TextMagic

Konfigurera och testa Azure AD SSO med TextMagic med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i TextMagic.

Om du vill konfigurera och testa Azure AD SSO med TextMagic, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera TEXTMAGIC SSO](#configure-textmagic-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa TextMagic test User](#create-textmagic-test-user)** -om du vill ha en motsvarighet till B. Simon i TextMagic som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **TextMagic** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I textrutan **Identifierare** skriver du en URL: `https://my.textmagic.com/saml/metadata`

5. TextMagic-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. TextMagic-programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig TextMagic-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name |   Källattribut| Namnområde  |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera TextMagic** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TextMagic.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **TextMagic**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-textmagic-sso"></a>Konfigurera TextMagic SSO

1. Om du vill automatisera konfigurationen i TextMagic måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations TextMagic** för att dirigera dig till TextMagic-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på TextMagic. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera TextMagic manuellt öppnar du ett nytt webbläsarfönster och loggar in på din TextMagic-företags webbplats som administratör och utför följande steg:

4. Välj **konto inställningar** under användar namnet.

    ![TextMagic-konfiguration](./media/textmagic-tutorial/config1.png)

5. Klicka på fliken **enkel inloggning (SSO)** och fyll i följande fält:  

    ![TextMagic-konfiguration](./media/textmagic-tutorial/config2.png)

    a. I text rutan **entitets-ID för identitetsprovider:** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    b. Klistra in värdet för **inloggnings-URL**: en som du har kopierat från Azure Portal i **URL: en för URL för identitetsprovider** .

    c. I **URL: en för URL för identitets leverantör** , klistra in värdet för **Logga ut URL**, som du har kopierat från Azure Portal.

    d. Öppna ditt **bas-64-kodade certifikat** i anteckningar som hämtats från Azure Portal, kopiera innehållet i det till Urklipp och klistra in det i text rutan för det **offentliga x509-certifikatet:** .

    e. Klicka på **Spara**.

### <a name="create-textmagic-test-user"></a>Skapa TextMagic test användare

Programmet stöder **just-in-Time-etablering** och efter att användare av autentisering skapas i programmet automatiskt. Du måste fylla i informationen en gång vid den första inloggningen för att aktivera under kontot i systemet.
Det finns inget åtgärdsobjekt för dig i det här avsnittet.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TextMagic på åtkomst panelen, bör du loggas in automatiskt på den TextMagic som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova TextMagic med Azure AD](https://aad.portal.azure.com/)

