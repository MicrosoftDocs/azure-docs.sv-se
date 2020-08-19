---
title: 'Självstudie: Azure Active Directory integrering med MyWorkDrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MyWorkDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: aa0fe40ec28aea7f82ee0e635b3c42140a472816
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552449"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Självstudie: integrera MyWorkDrive med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar MyWorkDrive med Azure Active Directory (Azure AD). När du integrerar MyWorkDrive med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MyWorkDrive.
* Gör det möjligt för användarna att logga in automatiskt till MyWorkDrive med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* MyWorkDrive för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. MyWorkDrive stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Lägga till MyWorkDrive från galleriet

Om du vill konfigurera integreringen av MyWorkDrive i Azure AD måste du lägga till MyWorkDrive från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **MyWorkDrive** i sökrutan.
1. Välj **MyWorkDrive** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med MyWorkDrive med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i MyWorkDrive.

Om du vill konfigurera och testa Azure AD SSO med MyWorkDrive, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera MYWORKDRIVE SSO](#configure-myworkdrive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa MyWorkDrive test User](#create-myworkdrive-test-user)** – om du vill ha en motsvarighet till Britta Simon i MyWorkDrive som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **MyWorkDrive** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Mata in ditt eget företags MyWorkDrive-Server värd Namn: t. ex.
    > 
    > Svarswebbadress: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Inloggnings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakta [MyWorkDrive support team](mailto:support@myworkdrive.com) om du är osäker på hur du ställer in ditt eget värdnamn och TLS/SSL-certifikat för dessa värden.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** till Urklipp.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurera MyWorkDrive SSO

1. Om du vill automatisera konfigurationen i MyWorkDrive måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **installations MyWorkDrive** för att dirigera dig till MyWorkDrive-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på MyWorkDrive. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-4.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera MyWorkDrive manuellt i ett annat webbläsarfönster loggar du in på MyWorkDrive som säkerhets administratör.

1. På MyWorkDrive-servern på administratörs panelen klickar du på **Enterprise** och utför följande steg:

    ![Administratören](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Aktivera **SAML/ADFS SSO**.

    b. Välj **SAML – Azure AD**

    c. I text rutan för **Azure App federationens metadata-URL** klistrar du in värdet för **app federationens metadata-URL** som du har kopierat från Azure Portal.

    d. Klicka på **Spara**

    > [!NOTE]
    > Mer information finns i [Support artikeln för Azure AD-MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MyWorkDrive.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **MyWorkDrive**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-myworkdrive-test-user"></a>Skapa MyWorkDrive test användare

I det här avsnittet skapar du en användare som heter Britta Simon i MyWorkDrive. Arbeta med [MyWorkDrive support team](mailto:support@myworkdrive.com) för att lägga till användare i MyWorkDrive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen MyWorkDrive på åtkomst panelen, bör du loggas in automatiskt på den MyWorkDrive som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)