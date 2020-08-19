---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Teamphoria | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Teamphoria.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 30971c9c5280bfba37f46aaab0aaf2c2506502c0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542351"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Teamphoria

I den här självstudien får du lära dig hur du integrerar Teamphoria med Azure Active Directory (Azure AD). När du integrerar Teamphoria med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Teamphoria.
* Gör det möjligt för användarna att logga in automatiskt till Teamphoria med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Teamphoria för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Teamphoria stöder **SP** -INITIERAd SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Lägga till Teamphoria från galleriet

Om du vill konfigurera integreringen av Teamphoria i Azure AD måste du lägga till Teamphoria från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Teamphoria** i sökrutan.
1. Välj **Teamphoria** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Konfigurera och testa enkel inloggning med Azure AD för Teamphoria

Konfigurera och testa Azure AD SSO med Teamphoria med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Teamphoria.

Om du vill konfigurera och testa Azure AD SSO med Teamphoria, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera TEAMPHORIA SSO](#configure-teamphoria-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Teamphoria test User](#create-teamphoria-test-user)** -om du vill ha en motsvarighet till B. Simon i Teamphoria som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Teamphoria** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Teamphoria client support team](https://www.teamphoria.com/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Teamphoria** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Teamphoria.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Teamphoria**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-teamphoria-sso"></a>Konfigurera Teamphoria SSO

1. Om du vill automatisera konfigurationen i Teamphoria måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Teamphoria** för att dirigera dig till Teamphoria-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Teamphoria. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Teamphoria manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Teamphoria-företags webbplats som administratör och utför följande steg:

4. Gå till alternativet **admin-inställningar** i det vänstra verktygsfältet och klicka på **enkel inloggning** på fliken Konfigurera för att öppna fönstret SSO-konfiguration.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Klicka på alternativet **Lägg till ny identitetsprovider** i det övre högra hörnet för att öppna formuläret för att lägga till inställningarna för SSO.

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Ange informationen i fälten enligt beskrivningen nedan –

    ![Konfigurera enkel inloggning](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **VISNINGS namn**: Ange visnings namnet för plugin-programmet på administratörs sidan.

    b. **Knapp namn**: namnet på den flik som visas på inloggnings sidan för inloggning via SSO.

    c. **Certifikat**: öppna det certifikat som hämtades tidigare från Azure Portal i anteckningar, kopiera innehållet i samma och klistra in det i rutan.

    d. **Start punkt**: klistra in **inloggnings-URL: en** som kopierats tidigare från Azure Portal.

    e. Växla alternativet till **på** och klicka på **Spara**.

### <a name="create-teamphoria-test-user"></a>Skapa Teamphoria test användare

För att Azure AD-användare ska kunna logga in på Teamphoria måste de tillhandahållas i Teamphoria. När det gäller Teamphoria är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Teamphoria-företags webbplats som administratör.

1. Klicka på **Administratörs** inställningar i det vänstra verktygsfältet och klicka på **användare** på fliken **Hantera** för att öppna administratörs sidan för användare.

    ![Lägga till medarbetare](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicka på alternativet för **manuell inbjudan** .

    ![Bjud in personer](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Utför följande åtgärder på den här sidan.

    ![Bjud in personer](./media/teamphoria-tutorial/manual_user_invite.png)

    a. I text rutan **e-postadress** anger du **e-postadressen** för användaren som B. Simon.

    b. I text rutan för **förnamn** anger du det första namnet på användaren som **B**.

    c. I text rutan **efter namn** anger du användarens efter namn som **Simon**.

    d. Klicka på **Bjud in 1 användare**. Användaren måste godkänna inbjudan för att kunna skapas i systemet.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Teamphoria på åtkomst panelen, bör du loggas in automatiskt på den Teamphoria som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Teamphoria med Azure AD](https://aad.portal.azure.com/)

