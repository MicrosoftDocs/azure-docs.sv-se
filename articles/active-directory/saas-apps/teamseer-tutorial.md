---
title: 'Självstudie: Azure Active Directory integrering med TeamSeer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TeamSeer.
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
ms.openlocfilehash: 815c738f1680c12f94aa97e91cd123237bf1b4f1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542511"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Självstudie: Azure Active Directory integrering med TeamSeer

I den här självstudien får du lära dig hur du integrerar TeamSeer med Azure Active Directory (Azure AD).
Genom att integrera TeamSeer med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TeamSeer.
* Du kan göra det möjligt för användarna att logga in automatiskt till TeamSeer (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TeamSeer behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* TeamSeer-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TeamSeer stöder **SP** -INITIERAd SSO

## <a name="adding-teamseer-from-the-gallery"></a>Lägga till TeamSeer från galleriet

Om du vill konfigurera integreringen av TeamSeer i Azure AD måste du lägga till TeamSeer från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till TeamSeer från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **TeamSeer**, väljer **TeamSeer** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![TeamSeer i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TeamSeer baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i TeamSeer upprättas.

Om du vill konfigurera och testa enkel inloggning med TeamSeer i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TeamSeer-enkel inloggning](#configure-teamseer-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TeamSeer test User](#create-teamseer-test-user)** – om du vill ha en motsvarighet till Britta Simon i TeamSeer som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TeamSeer i Azure AD:

1. Välj **enkel inloggning**på sidan **TeamSeer** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TeamSeer-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [TeamSeer client support team](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera TeamSeer** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-teamseer-single-sign-on"></a>Konfigurera TeamSeer enkel inloggning

1. Logga in på din TeamSeer-företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **personal administration**.

    ![PERSONAL administratör](./media/teamseer-tutorial/ic789634.png "PERSONAL administratör")

1. Klicka på **Konfigurera**.

    ![Installation](./media/teamseer-tutorial/ic789635.png "Installation")

1. Klicka på **Konfigurera information om SAML-Provider**.

    ![SAML-inställningar](./media/teamseer-tutorial/ic789636.png "SAML-inställningar")

1. I avsnittet information om SAML-Provider utför du följande steg:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789637.png "SAML-inställningar")

    a. I text rutan **URL** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    b. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan **IDP offentlig certifikat** .

1. Utför följande steg för att slutföra konfigurationen av SAML-providern:

    ![SAML-inställningar](./media/teamseer-tutorial/ic789638.png "SAML-inställningar")

    a. I **test-e-postadresserna**anger du test användarens e-postadress.
  
    b. I text rutan **utfärdare** anger du utfärdar-URL: en för tjänst leverantören.
  
    c. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TeamSeer.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **TeamSeer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TeamSeer**.

    ![TeamSeer-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-teamseer-test-user"></a>Skapa TeamSeer test användare

Om du vill att Azure AD-användare ska kunna logga in på TeamSeer måste de tillhandahållas i ShiftPlanning. När det gäller TeamSeer är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **TeamSeer** -företags webbplats som administratör.

1. Gå till **personal admin- \> användare** och klicka sedan på **Kör guiden Ny användare**.

    ![PERSONAL administratör](./media/teamseer-tutorial/ic789640.png "PERSONAL administratör")

1. I avsnittet **användar information** utför du följande steg:

    ![Användar information](./media/teamseer-tutorial/ic789641.png "Användar information")

    a. Ange **förnamn**, efter **namn**, **användar namn (e-postadress)** för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna.
  
    b. Klicka på **Nästa**.

1. Följ anvisningarna på skärmen för att lägga till en ny användare och klicka på **Slutför**.

> [!NOTE]
> Du kan använda andra verktyg för TeamSeer av användar konton eller API: er som tillhandahålls av TeamSeer för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TeamSeer på åtkomst panelen, bör du loggas in automatiskt på den TeamSeer som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
