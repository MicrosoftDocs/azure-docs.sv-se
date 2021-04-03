---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Comm100 Live Chat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Comm100 Live Chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455501"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Comm100 Live Chat

I den här självstudien får du lära dig att integrera Comm100 Live chatt med Azure Active Directory (Azure AD). När du integrerar Comm100 Live chatt med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Comm100 Live Chat.
* Gör det möjligt för användarna att logga in automatiskt till Comm100 Live chat med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Comm100 Live Chat, enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Comm100 Live Chat har stöd för **SP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Lägga till Comm100 Live Chat från galleriet

För att konfigurera integreringen av Comm100 Live Chat i Azure AD måste du lägga till Comm100 Live Chat från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Comm100 Live Chat** i sökrutan.
1. Välj **Comm100 Live Chat** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Konfigurera och testa enkel inloggning med Azure AD för Comm100 Live Chat

Konfigurera och testa Azure AD SSO med Comm100 Live chat med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Comm100 Live Chat.

Om du vill konfigurera och testa Azure AD SSO med Comm100 Live Chat slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Comm100 Live Chat SSO](#configure-comm100-live-chat-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Comm100 Live Chat test User](#create-comm100-live-chat-test-user)** – om du vill ha en motsvarighet till B. Simon i Comm100 Live Chat som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Comm100 Live Chat** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkligt. Du uppdaterar värdet för inloggnings-URL med den faktiska inloggnings-URL:en, vilket förklaras senare i självstudien.

1. Comm100 Live Chat-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig Comm100 Live Chat-programmet att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name |  Källattribut|
    | ---------------| --------------- |
    |   e-post    | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Comm100 Live Chat** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Comm100 Live Chat.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Comm100 Live Chat**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-comm100-live-chat-sso"></a>Konfigurera Comm100 Live Chat SSO

1. Logga in på Comm100 Live Chat som säkerhets administratör i ett annat webbläsarfönster.

1. Längst upp till höger på sidan klickar du på **Mitt konto**.

   ![Comm100 Live Chat – myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Till vänster i menyn klickar du på **Security** och sedan på **Agent Single Sign-On** (Enkel inloggning för agent).

   ![Skärm bild som visar menyn på vänster sida med konto "säkerhet" och "enkel inloggning med agent" markerat.](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. På sidan **Agent Single Sign-On** (Enkel inloggning för agent) utför du följande steg:

   ![Comm100 Live Chat-säkerhet](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Kopiera den första markerade länken och klistra in den i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

   b. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

   c. I text rutan **utloggnings-URL** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.

   d. Klicka på **Välj en fil** för att ladda upp det base-64-kodade certifikat som du har laddat ned från Azure-portalen till **certifikatet**.

   e. Klicka på **Spara ändringar**.

### <a name="create-comm100-live-chat-test-user"></a>Comm100 Live Chat-testanvändare

För att Azure AD-användare ska kunna logga in på Comm100 Live Chat måste de tillhandahållas i Comm100 Live Chat. I Comm100 Live Chat är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Comm100 Live Chat som en säkerhets administratör.

2. Längst upp till höger på sidan klickar du på **Mitt konto**.

    ![Comm100 Live Chat – myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Till vänster i menyn klickar du på **Agenter** och sedan på **Ny agent**.

    ![Comm100 Live Chat-agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. På sidan **Ny agent** utför du följande steg:

    ![Comm100 Live Chat – ny agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. I text rutan **e-postadress** anger du e-postmeddelandet som **B. Simon \@ contoso.com**.

    b. I text rutan **förnamn** anger du det första namnet på användaren, t. ex. **B**.

    c. I textrutan **Efternamn** anger du efternamnet på användaren som **simon**.

    d. I text rutan **visnings namn** anger du visnings namnet för användaren, t **. ex. B. Simon**

    e. I textrutan **Lösenord** skriver du ditt lösenord.

    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Comm100 Live Chat-panelen i åtkomstpanelen bör du automatiskt loggas in på Comm100 Live Chat som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Comm100 Live chatt med Azure AD](https://aad.portal.azure.com/)