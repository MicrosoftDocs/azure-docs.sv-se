---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Spotinst | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Spotinst.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 3b8297175c24aac132fd7d83580e0889e0da4730
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587968"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Spotinst

I den här självstudien får du lära dig hur du integrerar Spotinst med Azure Active Directory (Azure AD). När du integrerar Spotinst med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Spotinst.
* Gör det möjligt för användarna att logga in automatiskt till Spotinst med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Spotinst för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Spotinst stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-spotinst-from-the-gallery"></a>Lägga till Spotinst från galleriet

Om du vill konfigurera integreringen av Spotinst i Azure AD måste du lägga till Spotinst från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Spotinst** i sökrutan.
1. Välj **Spotinst** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurera och testa enkel inloggning med Azure AD för Spotinst

Konfigurera och testa Azure AD SSO med Spotinst med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Spotinst.

Om du vill konfigurera och testa Azure AD SSO med Spotinst, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SPOTINST SSO](#configure-spotinst-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Spotinst test User](#create-spotinst-test-user)** -om du vill ha en motsvarighet till B. Simon i Spotinst som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Spotinst** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i IDP initierat läge, så gör du följande:

   1. Se till att **svars-URL** är inställt på: https://console.spotinst.com/auth/saml .
   1. I **relä läge**anger du ditt Spotinst organisations-ID, som du också kan bekräfta på fliken **SSO** .
   1. **Inloggnings-URL: en** måste vara tom.

1. Klicka på **Spara**.

1. Spotinst-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Spotinst-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | -----| --------------- |
    | E-post | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Spotinst** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Spotinst.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Spotinst**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-spotinst-sso"></a>Konfigurera Spotinst SSO

1. Logga in på Spotinst som säkerhets administratör i ett annat webbläsarfönster.

2. Klicka på **ikonen användare** längst upp till höger på skärmen och klicka på **Inställningar**.

    ![Spotinst-inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klicka på fliken **säkerhet** överst och välj sedan **identitets leverantörer** och utför följande steg:

    ![Spotinst-säkerhet](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopiera värdet för **relä status** för din instans och klistra in det i text rutan för **Relay-tillstånd** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    b. Klicka på **Bläddra** för att ladda upp metadata-XML-filen som du har laddat ned från Azure Portal

    c. Klicka på **Spara**.

### <a name="create-spotinst-test-user"></a>Skapa Spotinst test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Spotinst.

1. Utför följande steg om du har konfigurerat programmet i det initierade läget **SP** :

   a. Logga in på Spotinst som säkerhets administratör i ett annat webbläsarfönster.

   b. Klicka på **ikonen användare** längst upp till höger på skärmen och klicka på **Inställningar**.

    ![Spotinst-inställningar](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klicka på **användare** och välj **Lägg till användare**.

    ![Spotinst-inställningar](./media/spotinst-tutorial/adduser1.png)

    d. I avsnittet Lägg till användare utför du följande steg:

    ![Spotinst-inställningar](./media/spotinst-tutorial/adduser2.png)

    * I text rutan **fullständigt namn** anger du det fullständiga namnet på användaren som **BrittaSimon**.

    * I text rutan **e-post** anger du användarens e-postadress `brittasimon\@contoso.com` .

    * Välj information om organisationen för **organisations rollen, konto rollen och kontona**.

2. Om du har konfigurerat programmet i det initierade **IDP** -läget finns det inget åtgärds objekt i det här avsnittet. Spotinst stöder just-in-Time-etablering, som är aktiverat som standard. En ny användare skapas vid ett försök att komma åt Spotinst om den inte redan finns.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Spotinst på åtkomst panelen, bör du loggas in automatiskt på den Spotinst som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Spotinst med Azure AD](https://aad.portal.azure.com/)

