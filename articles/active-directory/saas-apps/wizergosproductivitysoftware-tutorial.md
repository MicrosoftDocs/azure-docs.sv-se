---
title: 'Självstudie: Azure Active Directory integration med Wizergos produktivitets program | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wizergos produktivitets program.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 2d5bcb52b1feb7ea8202aa354793ebf975ed4f19
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88541493"
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Självstudie: Azure Active Directory integration med Wizergos produktivitets program

I den här självstudien får du lära dig att integrera Wizergos produktivitets program med Azure Active Directory (Azure AD).
Att integrera Wizergos produktivitets program med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till Wizergos produktivitets program.
* Du kan göra det möjligt för användarna att logga in automatiskt till Wizergos produktivitets program (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Wizergos produktivitets program behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Wizergos produktivitet – enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Wizergos Productivity-programvara stöder **IDP** INITIERAd SSO

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>Lägga till Wizergos produktivitets program från galleriet

Om du vill konfigurera integreringen av Wizergos produktivitets program i Azure AD måste du lägga till Wizergos produktivitets program från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Wizergos produktivitets program från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Wizergos produktivitets program vara**, väljer **Wizergos produktivitets program** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Wizergos produktivitets program i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Wizergos produktivitets program baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Wizergos produktivitets program.

Om du vill konfigurera och testa enkel inloggning med Wizergos produktivitets program i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Wizergos produktivitet program vara enkel inloggning](#configure-wizergos-productivity-software-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Wizergos produktivitet program test användare](#create-wizergos-productivity-software-test-user)** – om du vill ha en motsvarighet till Britta Simon i Wizergos produktivitets program som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Wizergos produktivitets program för Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **Wizergos Productivity Software** Application Integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om Wizergos produktivitets program domän och URL: er för enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://www.wizergos.net`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Wizergos produktivitets program** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-wizergos-productivity-software-single-sign-on"></a>Konfigurera Wizergos produktivitet program vara enkel inloggning

1. Logga in på din Wizergos produktivitets program varu klient som administratör i ett annat webbläsarfönster.

2. Från menyn Hamburger väljer du **admin**.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

3. Välj **autentisering** på sidan administratör på den vänstra menyn och klicka på **Azure AD**.

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

4. Utför följande steg i avsnittet **Authentication (autentisering** ).

    ![Konfigurera enkel inloggning på appsidan](./media/wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Klicka på knappen **överför** för att ladda upp det hämtade certifikatet från Azure AD.
    
    b. I text rutan **utfärdar-URL** klistrar du in det ID-värde för **Azure AD** som du har kopierat från Azure Portal.
    
    c. I text rutan för **enkel inloggnings-URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.
    
    d. I text rutan för **enkel inloggnings-URL** klistrar du in URL-värdet för **utloggning** som du har kopierat från Azure Portal.
    
    e. Klicka på knappen **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Wizergos produktivitets program.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Wizergos produktivitets program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Wizergos produktivitets program**.

    ![Wizergos produktivitets program länk i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wizergos-productivity-software-test-user"></a>Skapa Wizergos produktivitet program test användare

I det här avsnittet skapar du en användare som kallas Britta Simon i Wizergos produktivitets program. Arbeta med [Wizergos Productivity support team](mailTo:support@wizergos.com) för att lägga till användarna i Wizergos produktivitets program plattform.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Wizergos produktivitets programs panel på åtkomst panelen, bör du loggas in automatiskt på den Wizergos produktivitets program vara som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

