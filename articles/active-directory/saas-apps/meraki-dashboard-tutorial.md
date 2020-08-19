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
ms.date: 04/20/2020
ms.author: jeedes
ms.openlocfilehash: f4a4c38cf079c22dbd2b8eda5e68cc3f147b1fc0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535022"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Meraki-instrumentpanel

I den här självstudien får du lära dig att integrera Meraki-instrumentpanelen med Azure Active Directory (Azure AD). När du integrerar Meraki-instrumentpanelen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Meraki-instrumentpanelen.
* Gör det möjligt för användarna att logga in automatiskt till Meraki-instrumentpanelen med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Meraki-prenumeration med enkel inloggning (SSO) för instrument panelen.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Meraki-instrumentpanelen stöder **IDP** INITIERAd SSO
* När du har konfigurerat Meraki-instrumentpanelen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Lägga till Meraki-instrumentpanelen från galleriet

Om du vill konfigurera integreringen av Meraki-instrumentpanelen i Azure AD måste du lägga till Meraki-instrumentpanelen från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Meraki Dashboard** i sökrutan.
1. Välj **Meraki instrument panel** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-meraki-dashboard"></a>Konfigurera och testa enkel inloggning med Azure AD för Meraki-instrumentpanelen

Konfigurera och testa Azure AD SSO med Meraki-instrumentpanelen med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på Meraki-instrumentpanelen.

Om du vill konfigurera och testa Azure AD SSO med Meraki-instrumentpanelen Slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Meraki-instrumentpanelen SSO](#configure-meraki-dashboard-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Meraki instrument panel test User](#create-meraki-dashboard-test-user)** -om du vill ha en motsvarighet till B. Simon på Meraki-instrumentpanelen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Meraki instrument panels** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
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
    
    | Name | Källattribut|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. Kopiera URL-värdet för utloggning i avsnittet **Konfigurera Meraki-instrumentpanel** och spara det på datorn.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Meraki-instrumentpanelen.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Meraki instrument panel**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-meraki-dashboard-sso"></a>Konfigurera Meraki-instrumentpanelen SSO

1. Logga in på Meraki-instrumentpanelen som administratör i ett annat webbläsarfönster.

1. Navigera till **organisations**  ->  **Inställningar**.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/configure1.png)

1. Under autentisering, ändra **SAML SSO** till **SAML SSO aktiverat**.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/configure2.png)

1. Klicka på **Lägg till en SAML-IDP**.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/configure3.png)

1. Klistra in det **tumavtryck** -värde som du har kopierat från Azure Portal till **X. 590 cert-finger avtryck** . Klicka sedan på **Spara**. När du har sparat visas konsument-URL: en. Kopiera URL-värde för konsument och klistra in den i text rutan för **svars-URL** i **avsnittet grundläggande SAML-konfiguration** i Azure Portal.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Skapa Meraki instrument panel test användare

1. Logga in på Meraki-instrumentpanelen som administratör i ett annat webbläsarfönster.

1. Gå till **organisations**  ->  **Administratörer**.

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/user1.png)

1. I avsnittet SAML-administratörs roller klickar du på knappen **Lägg till SAML-roll** .

    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/user2.png)

1. Ange roll **meraki_full_admin**, markera **organisationens åtkomst** som **fullständig** och klicka på **skapa roll**. Upprepa processen för **meraki_readonly_admin**, den här tids markeringen **organisations åtkomst** som **skrivskyddad** .
 
    ![Konfiguration av Meraki-instrumentpanel](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Meraki instrument panel på åtkomst panelen, bör du loggas in automatiskt på den Meraki-instrumentpanel som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Meraki-instrumentpanelen med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Meraki-instrumentpanelen med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

