---
title: 'Självstudie: Azure Active Directory integrering med SkyDesk e-post | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SkyDesk e-post.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 71150ea5ba7d6f25b07a9afaa1f1dc60adcb3e24
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518796"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Självstudie: Azure Active Directory integrering med SkyDesk e-post

I den här självstudien får du lära dig att integrera SkyDesk e-post med Azure Active Directory (Azure AD).
Att integrera SkyDesk e-post med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till SkyDesk e-post.
* Du kan göra det möjligt för användarna att logga in automatiskt till SkyDesk e-post (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SkyDesk e-post behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SkyDesk e-postaktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SkyDesk e-post stöder **SP** -INITIERAd SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Lägga till SkyDesk e-post från galleriet

Om du vill konfigurera integreringen av SkyDesk-e-post i Azure AD måste du lägga till SkyDesk e-post från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SkyDesk e-post från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SkyDesk email**, väljer **SkyDesk e-post** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![SkyDesk e-post i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SkyDesk e-post baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och relaterad användare i SkyDesk e-post upprättas.

Om du vill konfigurera och testa enkel inloggning med SkyDesk e-post i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SkyDesk E-mail Single Sign-on](#configure-skydesk-email-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SkyDesk email test User](#create-skydesk-email-test-user)** – om du vill ha en motsvarighet till Britta Simon i SkyDesk-e-post som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SkyDesk e-post i Azure AD:

1. Välj **enkel inloggning**på sidan för **SkyDesk e-** postintegrering i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SkyDesk e-post domän och URL: er enkel inloggnings information](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [SkyDesk E-mail client support team](https://www.skydesk.jp/apps/support/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SkyDesk e-post** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurera SkyDesk e-post för enkel inloggning

1. Logga in på ditt SkyDesk e-postkonto som administratör i en annan webbläsare.

1. I menyn högst upp klickar du på **konfiguration**och väljer **org**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Klicka på **domäner** i den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Klicka på **Lägg till domän**.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Ange ditt domän namn och verifiera sedan domänen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Klicka på **SAML-autentisering** på den vänstra panelen.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Utför följande steg på dialog sidan **SAML-autentisering** :

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Om du vill använda SAML-baserad autentisering bör du antingen ha **verifierad domän** -eller **portal-URL** -installation. Du kan ange portalens URL med det unika namnet.

    ![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. I text rutan **utloggnings** -URL klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    c. **Ändra lösen ordets URL** är valfritt så lämna det tomt.

    d. Klicka på **Hämta nyckel från fil** för att välja det hämtade certifikatet från Azure Portal och klicka sedan på **Öppna** för att ladda upp certifikatet.

    e. Som **Algoritm** väljer du **RSA**.

    f. Spara ändringarna genom att klicka på **OK** .

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SkyDesk e-post.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SkyDesk e-post**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SkyDesk e-post**.

    ![SkyDesk-e-postlänken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-skydesk-email-test-user"></a>Skapa SkyDesk e-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i SkyDesk e-post.

Klicka på **användar åtkomst** på den vänstra panelen i SkyDesk e-post och ange ditt användar namn.

![Konfigurera enkel inloggning](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Om du behöver skapa Mass användare måste du kontakta [support teamet för SkyDesk e-postklient](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SkyDesk e-post på åtkomst panelen, bör du loggas in automatiskt på SkyDesk-e-postmeddelandet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

