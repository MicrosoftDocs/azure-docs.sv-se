---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Workteam | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workteam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: c95fceaa90ca379519379d37c19fb15dcd1de321
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546380"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Workteam

I den här självstudien får du lära dig hur du integrerar Workteam med Azure Active Directory (Azure AD). När du integrerar Workteam med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Workteam.
* Gör det möjligt för användarna att logga in automatiskt till Workteam med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Workteam för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Workteam stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-workteam-from-the-gallery"></a>Lägga till Workteam från galleriet

Om du vill konfigurera integreringen av Workteam i Azure AD måste du lägga till Workteam från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Workteam** i sökrutan.
1. Välj **Workteam** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Konfigurera och testa enkel inloggning med Azure AD för Workteam

Konfigurera och testa Azure AD SSO med Workteam med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Workteam.

Om du vill konfigurera och testa Azure AD SSO med Workteam, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera WORKTEAM SSO](#configure-workteam-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Workteam test User](#create-workteam-test-user)** -om du vill ha en motsvarighet till B. Simon i Workteam som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Workteam** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://app.workte.am`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Workteam** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Workteam.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Workteam**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-workteam-sso"></a>Konfigurera Workteam SSO

1. Om du vill automatisera konfigurationen i Workteam måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations Workteam** för att dirigera dig till Workteam-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Workteam. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Workteam manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Workteam-företags webbplats som administratör och utför följande steg:

4. Klicka på **profil logo typ** i det övre högra hörnet och klicka sedan på **organisations inställningar**. 

    ![Workteam-inställningar](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Under avsnittet **autentisering** klickar du på **Inställningar logo typ**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Utför följande steg på sidan **SAML-inställningar** :

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Välj **SAML-IDP** som **AD Azure**.

    b. I text rutan för **SAML-tjänsten för enkel inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. I text rutan **SAML-entitets-ID** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. I anteckningar öppnar du det **bas-64-kodade certifikatet** som du laddade ned från Azure Portal, kopierar dess innehåll och klistrar in det i rutan **SAML signerings certifikat (base64)** .

    e. Klicka på **OK**.

### <a name="create-workteam-test-user"></a>Skapa Workteam test användare

Om du vill att Azure AD-användare ska kunna logga in på Workteam måste de tillhandahållas i Workteam. I Workteam är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Workteam som säkerhets administratör.

2. Klicka på **användare** längst upp i mitten av sidan **organisations inställningar** och klicka sedan på **ny användare**.

    ![Workteam-användare](./media/workteam-tutorial/tutorial_workteam_user.png)

3. På sidan **Ny medarbetare** utför du följande steg:

    ![Workteam ny användare](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. I text rutan **namn** anger du det första namnet på användaren, t **. ex. B. Simon**.

    b. I textrutan **E-post** anger du användarens e-postadress som `B.Simon\@contoso.com`.

    c. Klicka på **OK**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Workteam på åtkomst panelen, bör du loggas in automatiskt på den Workteam som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Workteam med Azure AD](https://aad.portal.azure.com/)

