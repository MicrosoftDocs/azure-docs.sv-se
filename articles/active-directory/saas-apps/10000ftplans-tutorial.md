---
title: 'Självstudie: Azure Active Directory integrering med 10, 000ft-planer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 10, 000ft planer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 7ca157abf19918f72ce167e4abba4816dd7f2551
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539529"
---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Självstudie: Azure Active Directory integrering med 10, 000ft-planer

I den här självstudien får du lära dig att integrera 10, 000ft-planer med Azure Active Directory (Azure AD).
Integrering av 10, 000ft-planer med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till 10 000ft-planer.
* Du kan göra det möjligt för användarna att logga in automatiskt till 10 000ft planer (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med 10, 000ft-planer behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* 10, 000ft Plans prenumeration med enkel inloggning aktive rad

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* 10, 000ft planer stöder **SP** -INITIERAd SSO
* 10 har 000ft planer stöd **för just-in-Time** User-etablering

## <a name="adding-10000ft-plans-from-the-gallery"></a>Lägga till 10, 000ft-planer från galleriet

Om du vill konfigurera en integrering av 10, 000ft-planer i Azure AD, måste du lägga till 10 000ft-planer från galleriet till din lista över hanterade SaaS-appar.

**Gör så här om du vill lägga till 10, 000ft-planer från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **10, 000ft-planer**, väljer **10, 000ft-planer** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![10, 000ft planer i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med 10, 000ft planer baserade på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i 10, 000ft planer.

Om du vill konfigurera och testa enkel inloggning med 10, 000ft för Azure AD, måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera 10000Ft Plans enkel inloggning](#configure-10000ft-plans-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa 10000ft-planer testa användare](#create-10000ft-plans-test-user)** – för att få en motsvarighet till Britta Simon i 10 000ft planer som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med 10, 000ft planer:

1. I [Azure Portal](https://portal.azure.com/)på sidan **10, 000ft Plans** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![10, 000ft planerar domän-och URL-information för enkel inloggning](common/sp-identifier.png)

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://app.10000ft.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du URL:en: `https://app.10000ft.com/saml/metadata`

    > [!NOTE]
    > Värdet för **identifierare** skiljer sig om du har en anpassad domän. Kontakta [10, 000Ft planerar kund support teamet](https://www.10000ft.com/plans/support) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera 10, 000ft-planer** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-10000ft-plans-single-sign-on"></a>Konfigurera enkel inloggning för 10000ft-planer

Om du vill konfigurera enkel inloggning på **10 000Ft Plans** sida måste du skicka det hämtade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [10, 000ft Plans support team](https://www.10000ft.com/plans/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till 10 000ft-planer.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **10, 000ft planer**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **10, 000ft-planer**.

    ![Länken 10, 000ft abonnemang i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-10000ft-plans-test-user"></a>Skapa 10000ft-planer testa användare

I det här avsnittet skapas en användare som kallas Britta Simon i 10 000ft-planer. 10, 000ft planer stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i 10, 000ft planer skapas en ny efter autentiseringen.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [support teamet för 10, 000Ft plan](https://www.10000ft.com/plans/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen 10, 000ft planer i åtkomst panelen, bör du loggas in automatiskt på de 10, 000ft-planer som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
