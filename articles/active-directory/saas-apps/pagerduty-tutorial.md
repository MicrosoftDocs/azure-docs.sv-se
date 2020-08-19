---
title: 'Självstudie: Azure Active Directory integrering med PagerDuty | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2bc486987dd25d899728af4861c0b71ef14d9f4a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554100"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PagerDuty

I den här självstudien får du lära dig hur du integrerar PagerDuty med Azure Active Directory (Azure AD). När du integrerar PagerDuty med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PagerDuty.
* Gör det möjligt för användarna att logga in automatiskt till PagerDuty med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PagerDuty för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Om du använder MFA eller lösenordsbaserad autentisering med Azure AD stänger du av AuthnContext-värdet i SAML-begäran. Annars genererar Azure AD fel vid matchning av AuthnContext och skickar inte tillbaka token till programmet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PagerDuty stöder **SP** -INITIERAd SSO
* När du har konfigurerat PagerDuty kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Lägga till PagerDuty från galleriet

Om du vill konfigurera integreringen av PagerDuty i Azure AD måste du lägga till PagerDuty från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **PagerDuty** i sökrutan.
1. Välj **PagerDuty** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Konfigurera och testa enkel inloggning med Azure AD för PagerDuty

Konfigurera och testa Azure AD SSO med PagerDuty med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PagerDuty.

Om du vill konfigurera och testa Azure AD SSO med PagerDuty, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PAGERDUTY SSO](#configure-pagerduty-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PagerDuty test User](#create-pagerduty-test-user)** -om du vill ha en motsvarighet till B. Simon i PagerDuty som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **PagerDuty** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.pagerduty.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Kontakta [PagerDuty client support team](https://www.pagerduty.com/support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PagerDuty** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PagerDuty.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **PagerDuty**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-pagerduty-sso"></a>Konfigurera PagerDuty SSO

1. Logga in på din PagerDuty-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **konto inställningar**på menyn högst upp.

    ![Konto inställningar](./media/pagerduty-tutorial/ic778535.png "Konto inställningar")

3. Klicka på **enkel inloggning**.

    ![Enkel inloggning](./media/pagerduty-tutorial/ic778536.png "Enkel inloggning")

4. Utför följande steg på sidan **aktivera enkel inloggning (SSO)** :

    ![Aktivera enkel inloggning](./media/pagerduty-tutorial/ic778537.png "Aktivera enkel inloggning")

    a. Öppna ditt bas-64-kodade certifikat som du har laddat ned från Azure Portal i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i rutan **X. 509-certifikat**
  
    b. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
  
    c. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. Välj **Tillåt användar namn/lösen ord inloggning**.

    e. Markera kryss rutan **KRÄV exakt autentiserings kontext jämförelse** .

    f. Klicka på **Spara ändringar**.

### <a name="create-pagerduty-test-user"></a>Skapa PagerDuty test användare

Om du vill att Azure AD-användare ska kunna logga in på PagerDuty måste de tillhandahållas i PagerDuty. När det gäller PagerDuty är etableringen en manuell uppgift.

> [!NOTE]
> Du kan använda andra verktyg för PagerDuty av användar konton eller API: er som tillhandahålls av PagerDuty för att etablera Azure Active Directory användar konton.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **PagerDuty** -klient.

2. Klicka på **användare**på menyn högst upp.

3. Klicka på **Lägg till användare**.
   
    ![Lägg till användare](./media/pagerduty-tutorial/ic778539.png "Lägg till användare")

4.  I dialog rutan **Bjud in ditt team** utför du följande steg:
   
    ![Bjud in ditt team](./media/pagerduty-tutorial/ic778540.png "Bjud in ditt team")

    a. Skriv det **första och sista namnet** på användaren, t. ex. **B. Simon**. 
   
    b. Ange **e-** postadress till användaren, t **. ex. b. Simon \@ contoso.com**.
   
    c. Klicka på **Lägg till**och sedan på **skicka inbjudningar**.
   
    > [!NOTE]
    > Alla tillagda användare får en inbjudan att skapa ett PagerDuty-konto.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen PagerDuty på åtkomst panelen, bör du loggas in automatiskt på den PagerDuty som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PagerDuty med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du PagerDuty med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

