---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Leapsome | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ddc8cce7b56e0d9d4b3dc33dc1ad2d8c16582841
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458718"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Leapsome

I den här självstudien får du lära dig hur du integrerar Leapsome med Azure Active Directory (Azure AD). När du integrerar Leapsome med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Leapsome.
* Gör det möjligt för användarna att logga in automatiskt till Leapsome med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Leapsome för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Leapsome stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-leapsome-from-the-gallery"></a>Lägga till Leapsome från galleriet

Om du vill konfigurera integreringen av Leapsome i Azure AD måste du lägga till Leapsome från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Leapsome** i sökrutan.
1. Välj **Leapsome** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Konfigurera och testa enkel inloggning med Azure AD för Leapsome

Konfigurera och testa Azure AD SSO med Leapsome med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Leapsome.

Om du vill konfigurera och testa Azure AD SSO med Leapsome, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LEAPSOME SSO](#configure-leapsome-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Leapsome test User](#create-leapsome-test-user)** -om du vill ha en motsvarighet till B. Simon i Leapsome som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Leapsome** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. Skriv en URL i text rutan **identifierare** : `https://www.leapsome.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Den föregående svars-URL: en och inloggnings-URL-värdet är inte ett reellt värde. Du kommer att uppdatera dessa med de faktiska värdena, som beskrivs senare i självstudien.

1. Leapsome-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Leapsome-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut | Namnområde |
    | ---------------| --------------- | --------- |  
    | förnamn | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | efternamn | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | bild | URL till den anställdas bild | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Värdet för attributet Picture är inte verkligt. Uppdatera det här värdet med den faktiska bild-URL: en. För att få det här värdet kontaktar du [Leapsome client support team](mailto:support@leapsome.com).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Leapsome** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Leapsome.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Leapsome**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-leapsome-sso"></a>Konfigurera Leapsome SSO

1. Logga in på Leapsome som säkerhets administratör i ett annat webbläsarfönster.

1. Längst upp till höger klickar du på Inställningar logo typ och sedan på **Administratörs inställningar**.

    ![Leapsome uppsättning](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. På den vänstra meny raden klickar du på **enkel inloggning (SSO)** och på sidan **SAML-baserad enkel inloggning (SSO)** utför följande steg:

    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Välj **Aktivera SAML-baserad enkel inloggning**.

    b. Kopiera **inloggnings-URL: en (peka dina användare här för att starta inloggning)** och klistra in den i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    c. Kopiera **svars-URL: en (tar emot svar från din identitetsprovider)** och klistra in den i text rutan **svars-URL** i avsnittet  **grundläggande SAML-konfiguration** på Azure Portal.

    d. I text rutan **SSO-inloggnings-URL (tillhandahålls av identitetsprovider)** klistrar du in värdet för **inloggnings-URL: en** som du kopierade från Azure Portal.

    e. Kopiera det certifikat som du har laddat ned från Azure Portal utan `--BEGIN CERTIFICATE and END CERTIFICATE--` kommentarer och klistra in det i text rutan **certifikat (tillhandahålls av identitets leverantören)** .

    f. Klicka på **Uppdatera SSO-inställningar**.

### <a name="create-leapsome-test-user"></a>Skapa Leapsome test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Leapsome. Arbeta med [Leapsome-klientens support team](mailto:support@leapsome.com) för att lägga till användare eller domän som måste läggas till i en lista över tillåtna användare för Leapsome-plattformen. Om domänen läggs till av teamet, kommer användarna automatiskt att tillhandahålla Leapsome-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Leapsome på åtkomst panelen, bör du loggas in automatiskt på den Leapsome som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Leapsome med Azure AD](https://aad.portal.azure.com/)