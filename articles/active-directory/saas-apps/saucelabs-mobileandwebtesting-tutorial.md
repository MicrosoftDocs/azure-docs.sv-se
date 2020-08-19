---
title: 'Självstudie: Azure Active Directory integrering med recept Labs – mobil-och webb testning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och recept Labs – mobil-och webb testning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8dd5ca148474a1fe1464217f60c79e3815a5b26d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547927"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Självstudie: Azure Active Directory integrering med recept Labs – mobil-och webb testning

I den här självstudien får du lära dig att integrera recept Labs – mobil-och webbtest med Azure Active Directory (Azure AD).
Att integrera recept Labs – mobil-och webb testning med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har till gång till recept Labs – mobil-och webb testning.
* Du kan göra det möjligt för användarna att logga in automatiskt till recept Labs – mobil-och webbtest (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med recept Labs-mobil-och webb testning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Recept Labs – mobil-och webbtestad prenumeration med enkel inloggning aktive rad

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Recept Labs – mobil-och webb testning stöder **IDP** INITIERAd SSO
* Recept Labs – mobil-och webb testning stöder **just-in-Time** User-etablering

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Lägga till recept Labs – mobil-och webb testning från galleriet

Om du vill konfigurera integreringen av recept Labs – mobil-och webbtest i Azure AD, måste du lägga till recept Labs – mobil-och webb testning från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till recept Labs – mobil-och webb testning från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **recept Labs-mobil och webb testning**, väljer **recept Labs – mobil och webbtest** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Recept Labs – mobil-och webb testning i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med recept Labs – mobil-och webb testning baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i recept Labs-mobil-och webb testning vara upprättad.

Om du vill konfigurera och testa enkel inloggning med recept Labs – mobil-och webb testning i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera recept Labs – enkel inloggning för mobila och webbtester](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** – så här konfigurerar du inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa recept Labs – test användare av mobila och webbtester](#create-sauce-labs---mobile-and-web-testing-test-user)** – om du vill ha en motsvarighet till Britta Simon i recept Labs – mobil-och webbtester som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med recept Labs – mobil-och webb testning i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **recept Labs – mobil och webb testnings** program integrering.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Recept Labs – mobil-och webbtests domän och webb adresser enkel inloggnings information](common/preintegrated.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera recept labb – mobil och webbtest** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurera recept Labs – enkel inloggning för mobil-och webb testning

1. Logga in på din recept Labs – mobil-och webb test företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **användar ikonen** och välj fliken **team hantering** .

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Ange ditt **domän namn** i text rutan.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klicka på **Konfigurera** flik.

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Utför följande steg i avsnittet **Konfigurera enkel inloggning** .

    ![Konfigurera enkel inloggning](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klicka på **Bläddra** och ladda upp den hämtade metadata-filen från Azure AD.

    b. Markera kryss rutan **Tillåt just-in-Time-etablering** .

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
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till recept Labs – mobil-och webb testning.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **recept Labs-mobil och webb testning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **recept Labs – mobil-och webb testning**.

    ![Länken recept Labs – Mobile och Web testing i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Skapa recept Labs – test användare för mobil-och webb testning

I det här avsnittet skapas en användare som kallas Britta Simon i recept Labs – mobil-och webb testning. Recept Labs – mobil-och webb testning stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i recept Labs – mobil-och webb testning, skapas en ny efter autentiseringen.

> [!Note]
> Om du behöver skapa en användare manuellt kan du kontakta [recept Labs – mobil-och support teamet för webb testning](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen recept Labs – mobil och webbtester på åtkomst panelen, bör du loggas in automatiskt på de recept Labs-mobila och webbtester som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

