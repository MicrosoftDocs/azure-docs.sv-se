---
title: 'Självstudie: Azure Active Directory integrering med XaitPorter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: f729828627fdb0be18f220d34227aac756ed4e13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92894924"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Självstudie: Azure Active Directory integrering med XaitPorter

I den här självstudien får du lära dig hur du integrerar XaitPorter med Azure Active Directory (Azure AD).
Genom att integrera XaitPorter med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till XaitPorter.
* Du kan göra det möjligt för användarna att logga in automatiskt till XaitPorter (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med XaitPorter behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* XaitPorter-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* XaitPorter stöder **SP** -INITIERAd SSO

## <a name="adding-xaitporter-from-the-gallery"></a>Lägga till XaitPorter från galleriet

Om du vill konfigurera integreringen av XaitPorter i Azure AD måste du lägga till XaitPorter från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till XaitPorter från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **XaitPorter**, väljer **XaitPorter** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![XaitPorter i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med XaitPorter baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i XaitPorter upprättas.

Om du vill konfigurera och testa enkel inloggning med XaitPorter i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera XaitPorter enkel inloggning](#configure-xaitporter-single-sign-on)** – om du vill konfigurera de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa XaitPorter test User](#create-xaitporter-test-user)** – om du vill ha en motsvarighet till Britta Simon i XaitPorter som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med XaitPorter i Azure AD:

1. Välj **enkel inloggning** på sidan **XaitPorter** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för XaitPorter-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.xaitporter.com/saml/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.xaitporter.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [XaitPorter client support team](https://www.xait.com/support/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

6. Ange **IP-adressen** eller **URL: en för app Federation-Metadata** till [support teamet för SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/)så att XaitPorter kan se till att IP-adressen kan hämtas från XaitPorter-instansen som konfigurerar en godkänd lista på deras sida. 

### <a name="configure-xaitporter-single-sign-on"></a>Konfigurera XaitPorter Single Sign-On

1. Om du vill automatisera konfigurationen i XaitPorter måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations XaitPorter** för att dirigera dig till XaitPorter-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på XaitPorter. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera XaitPorter manuellt öppnar du ett nytt webbläsarfönster och loggar in på din XaitPorter-företags webbplats som administratör och utför följande steg:

4. Klicka på **administratör**.

    ![Skärm bild som visar den administratör som valts på XaitPorter-webbplatsen.](./media/xaitporter-tutorial/user1.png)

5. Välj **hantera enkel inloggning** i list rutan **system konfiguration** .

    ![Skärm bild som visar hantera enskilda Sign-On valt i systeminställningarna.](./media/xaitporter-tutorial/user2.png)

6. I avsnittet **hantera enkel inloggning** utför du följande steg:

    ![Skärm bild som visar avsnittet hantera enkel inloggning där du kan utföra de här stegen.](./media/xaitporter-tutorial/user3.png)

    a. Välj **aktivera enkel Sign-On autentisering**.

    b. I text rutan för **Inställningar för identitetsprovider** klistrar du in **URL för app Federation-Metadata** som du har kopierat från Azure Portal och klickar på **Hämta**.

    c. Välj **Aktivera skapande av användare**.

    d. Klicka på **OK**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till XaitPorter.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **XaitPorter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **XaitPorter**.

    ![XaitPorter-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-xaitporter-test-user"></a>Skapa XaitPorter test användare

I det här avsnittet skapar du en användare som heter Britta Simon i XaitPorter. Arbeta med [XaitPorter-klientens support team](https://www.xait.com/support/) för att lägga till användare i XaitPorter-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen XaitPorter på åtkomst panelen, bör du loggas in automatiskt på den XaitPorter som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)