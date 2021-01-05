---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Meraki-instrumentpanel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Meraki-instrumentpanelen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 0c43e085725ceb5db718c9b4c9c9d83787964d63
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813538"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Meraki-instrumentpanel

I den här självstudien får du lära dig att integrera Meraki-instrumentpanelen med Azure Active Directory (Azure AD). När du integrerar Meraki-instrumentpanelen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Meraki-instrumentpanelen.
* Gör det möjligt för användarna att logga in automatiskt till Meraki-instrumentpanelen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Meraki-prenumeration med enkel inloggning (SSO) för instrument panelen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Meraki-instrumentpanelen stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Lägga till Meraki-instrumentpanelen från galleriet

Om du vill konfigurera integreringen av Meraki-instrumentpanelen i Azure AD måste du lägga till Meraki-instrumentpanelen från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Meraki Dashboard** i sökrutan.
1. Välj **Meraki instrument panel** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Konfigurera och testa Azure AD SSO för Meraki-instrumentpanelen

Konfigurera och testa Azure AD SSO med Meraki-instrumentpanelen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på Meraki-instrumentpanelen.

Utför följande steg för att konfigurera och testa Azure AD SSO med Meraki-instrumentpanelen:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Meraki-instrumentpanelen SSO](#configure-meraki-dashboard-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Meraki instrument panel test User](#create-meraki-dashboard-test-user)** -om du vill ha en motsvarighet till B. Simon på Meraki-instrumentpanelen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Meraki instrument panels** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:
     
    I text rutan **svars-URL** skriver du en URL med följande mönster:  `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera det här värdet med det faktiska svars-URL-värdet, vilket beskrivs senare i självstudien.

1. Klicka på knappen **Spara**.

1. Meraki instrument panels program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Meraki-instrumentpanelen att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Namn | Källattribut|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns [här](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera URL-värdet för utloggning i avsnittet **Konfigurera Meraki-instrumentpanel** och spara det på datorn.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Meraki-instrumentpanelen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Meraki instrument panel**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.

    ![användarroll](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > **Välj ett roll** alternativ kommer att inaktive ras och standard rollen är användare för den valda användaren.

1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-meraki-dashboard-sso"></a>Konfigurera Meraki-instrumentpanelen SSO

1. Om du vill automatisera konfigurationen på Meraki-instrumentpanelen måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **Konfigurera Meraki-instrumentpanelen** för att dirigera dig till Meraki-instrumentpanelens program. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Meraki-instrumentpanelen. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Meraki-instrumentpanelen manuellt i ett annat webbläsarfönster loggar du in på företags platsen för din Meraki-instrumentpanel som administratör.

1. Navigera till **organisations**  ->  **Inställningar**.

    ![Fliken instrument panels inställningar Meraki](./media/meraki-dashboard-tutorial/configure-1.png)

1. Under autentisering, ändra **SAML SSO** till **SAML SSO aktiverat**.

    ![Meraki instrument panels autentisering](./media/meraki-dashboard-tutorial/configure-2.png)

1. Klicka på **Lägg till en SAML-IDP**.

    ![Meraki-instrumentpanel Lägg till en SAML-IdP](./media/meraki-dashboard-tutorial/configure-3.png)

1. Klistra in det **tumavtryck** -värde som du har kopierat från Azure Portal till **X. 590 cert-finger avtryck** . Klicka sedan på **Spara**. När du har sparat visas konsument-URL: en. Kopiera URL-värde för konsument och klistra in den i text rutan för **svars-URL** i **avsnittet grundläggande SAML-konfiguration** i Azure Portal.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Skapa Meraki instrument panel test användare

1. Logga in på Meraki-instrumentpanelen som administratör i ett annat webbläsarfönster.

1. Gå till **organisations**  ->  **Administratörer**.

    ![Meraki instrument panels administratörer](./media/meraki-dashboard-tutorial/user-1.png)

1. I avsnittet SAML-administratörs roller klickar du på knappen **Lägg till SAML-roll** .

    ![Meraki instrument panel Lägg till SAML-roll knapp](./media/meraki-dashboard-tutorial/user-2.png)

1. Ange roll **meraki_full_admin**, markera **organisationens åtkomst** som **fullständig** och klicka på **skapa roll**. Upprepa processen för **meraki_readonly_admin**, den här tids markeringen **organisations åtkomst** som **skrivskyddad** .
 
    ![Meraki instrument panel skapa användare](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den Meraki-instrumentpanel som du ställer in SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på Meraki instrument panels panel i Mina appar, bör du loggas in automatiskt på den Meraki-instrumentpanel som du konfigurerade SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Meraki-instrumentpanelen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).