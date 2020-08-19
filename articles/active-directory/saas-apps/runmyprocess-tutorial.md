---
title: 'Självstudie: Azure Active Directory integrering med RunMyProcess | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RunMyProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: db47849575c097cf56824b623dddbb6e0835d1b4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543626"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Självstudie: integrera RunMyProcess med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar RunMyProcess med Azure Active Directory (Azure AD). När du integrerar RunMyProcess med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RunMyProcess.
* Gör det möjligt för användarna att logga in automatiskt till RunMyProcess med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* RunMyProcess för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RunMyProcess stöder **SP** -INITIERAd SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>Lägga till RunMyProcess från galleriet

Om du vill konfigurera integreringen av RunMyProcess i Azure AD måste du lägga till RunMyProcess från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **RunMyProcess** i sökrutan.
1. Välj **RunMyProcess** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med RunMyProcess med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RunMyProcess.

Om du vill konfigurera och testa Azure AD SSO med RunMyProcess, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera RUNMYPROCESS SSO](#configure-runmyprocess-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa RunMyProcess test User](#create-runmyprocess-test-user)** -om du vill ha en motsvarighet till B. Simon i RunMyProcess som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **RunMyProcess** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [RunMyProcess client support team](mailto:support@runmyprocess.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera RunMyProcess** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurera RunMyProcess SSO

1. Logga in på din RunMyProcess-klient som administratör i ett annat webbläsarfönster.

1. Klicka på **konto** i den vänstra navigerings panelen och välj **konfiguration**.

    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Gå till avsnittet **autentiseringsmetod** och utför stegen nedan:

    ![Konfigurera enkel inloggning på appsidan](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Som **metod**väljer du **SSO med Samlv2**.

    b. I text rutan för **enkel inloggning** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. I text rutan **Logga ut omdirigering** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    d. I text rutan **namn-ID-format** skriver du värdet för **namn identifierare formatet** **urn: Oasis: Names: TC: SAML: 1.1: NameID-format: EmailAddress**.

    e. Öppna den hämtade certifikat filen från Azure Portal i anteckningar, kopiera innehållet i certifikat filen och klistra in den i text rutan **certifikat** .

    f. Klicka på **Spara** ikon.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RunMyProcess.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **RunMyProcess**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-runmyprocess-test-user"></a>Skapa RunMyProcess test användare

För att Azure AD-användare ska kunna logga in på RunMyProcess måste de tillhandahållas i RunMyProcess. När det gäller RunMyProcess är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din RunMyProcess-företags webbplats som administratör.

1. Klicka på **konto** och välj **användare** i den vänstra navigerings panelen och klicka sedan på **ny användare**.

    ![Ny användare](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Ny användare")

1. I avsnittet **användar inställningar** utför du följande steg:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Ange **namnet** och **e-postadressen** för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna.

    b. Välj ett **IDE-språk**, **språk**och **profil**.

    c. Välj **skicka e-post för att skapa konto till mig**.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Du kan använda andra verktyg för RunMyProcess av användar konton eller API: er som tillhandahålls av RunMyProcess för att etablera Azure Active Directory användar konton.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen RunMyProcess på åtkomst panelen, bör du loggas in automatiskt på den RunMyProcess som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)