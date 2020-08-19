---
title: 'Självstudie: Azure Active Directory integration med SensoScientific Wireless temperatur övervaknings system | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SensoScientific-systemet för trådlös temperatur övervakning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 61807f90caad9e6be2a25e54dcaa837595afc729
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543187"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Självstudie: Azure Active Directory integrering med SensoScientific Wireless temperatur övervaknings system

I den här självstudien får du lära dig att integrera SensoScientific Wireless temperatur övervaknings system med Azure Active Directory (Azure AD).
Att integrera SensoScientific Wireless temperatur Monitoring System med Azure AD ger följande fördelar:

* Du kan kontrol lera i Azure AD vem som har åtkomst till SensoScientific-systemet för trådlös temperatur övervakning.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade för att SensoScientific trådlöst temperatur övervaknings system (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SensoScientific Wireless temperatur övervaknings system behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* SensoScientific trådlös temperatur övervakning av system med enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SensoScientific Wireless temperatur övervaknings system stöder **IDP** INITIERAd SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Lägga till SensoScientific för trådlöst temperatur övervaknings system från galleriet

Om du vill konfigurera integreringen av SensoScientific-systemet för trådlös temperatur övervakning i Azure AD måste du lägga till SensoScientific Wireless temperatur övervaknings system från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SensoScientific för trådlöst temperatur övervaknings system från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SensoScientific Wireless temperatur Monitoring System**, väljer **SensoScientific för trådlöst temperatur övervakning** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![SensoScientific för trådlöst temperatur övervaknings system i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med SensoScientific för trådlöst temperatur baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i SensoScientific Wireless temperatur övervaknings system.

Om du vill konfigurera och testa enkel inloggning i Azure AD med SensoScientific Wireless temperatur övervaknings system måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SensoScientific Wireless temperatur övervaknings system enkel inloggning](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** – konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SensoScientific Wireless temperatur övervaknings system testa användare](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** – om du vill ha en motsvarighet till Britta Simon i SensoScientific Wireless temperatur övervaknings system som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med SensoScientific Wireless temperatur övervaknings system:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **SensoScientific trådlös temperatur övervakning system** program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![SensoScientific trådlös temperatur övervakning system domän och webb adresser enkel inloggnings information](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SensoScientific Wireless temperatur övervaknings system** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurera SensoScientific Wireless temperatur övervaknings system enkel inloggning

1. Logga in på SensoScientific trådlös temperatur övervakning av system program som administratör.

1. I navigerings menyn högst upp klickar du på **konfiguration** och goto **Konfigurera** under **enkel inloggning** för att öppna inställningarna för enkel inloggning och utför följande steg:

    ![Konfigurera enkel inloggning](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Välj **utfärdarens namn** som Azure AD.

    b. I text rutan **utfärdar-URL** klistrar du in **Azure AD-identifieraren** som du kopierade från Azure Portal.

    c. I text rutan för **enkel inloggnings-URL** klistrar du in **inloggnings-URL:** en som du har kopierat från Azure Portal.

    d. I text rutan för **enkel inloggnings-URL** klistrar du in den **URL för utloggning** som du kopierade från Azure Portal.

    e. Bläddra efter det certifikat som du har laddat ned från Azure Portal och ladda upp.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SensoScientific Wireless temperatur övervaknings system.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SensoScientific Wireless temperatur Monitoring System**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SensoScientific Wireless temperatur Monitoring System**.

    ![System länken SensoScientific trådlös temperatur övervakning i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Skapa SensoScientific trådlös temperatur övervakning system test användare

För att Azure AD-användare ska kunna logga in på SensoScientific för trådlöst temperatur övervakning måste de tillhandahållas i SensoScientific Wireless temperatur övervaknings system. Arbeta med [SensoScientific-teamet för trådlös temperatur övervakning](https://www.sensoscientific.com/contact-us/)   för att lägga till användare i SensoScientific-plattformen för trådlös temperatur övervakning. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SensoScientific trådlös temperatur övervaknings system på åtkomst panelen, bör du loggas in automatiskt på det SensoScientific trådlösa temperatur övervaknings systemet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

