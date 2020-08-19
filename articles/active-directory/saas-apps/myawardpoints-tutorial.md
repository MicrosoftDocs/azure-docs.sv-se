---
title: 'Självstudie: Azure Active Directory integrering med mina belönings punkter överst till-/topp-teamet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mina belönings punkter överst till höger/översta teamet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 06104964c06b0853498a7de272a62d2913b10be6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518734"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Självstudie: Azure Active Directory integrering med mina belönings punkter överst till-/topp-teamet

I den här självstudien får du lära dig hur du integrerar mina belönings punkter överst till Azure Active Directory (Azure AD).
Genom att integrera mina belönings punkter överst till-/topp-teamet med Azure AD får du följande fördelar:

* Du kan kontrol lera i Azure AD vem som har åtkomst till mina belönings punkter överst till-/topp-teamet.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade på mina belönings punkter överst till höger/översta teamet (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande om du vill konfigurera Azure AD-integrering med mina tilldelnings platser överst till-/-teamet:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mina belönings punkter överst under-och topp team med enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mina belönings punkter överst till-/topp-teamet stöder **SP** -INITIERAd SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Lägga till mina belönings punkter överst till/från det översta teamet från galleriet

Om du vill konfigurera integreringen av mina belönings punkter överst till/från överst i Azure AD, måste du lägga till mina belönings punkter överst till/från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till mina tilldelnings punkter överst till/från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du det **översta sub/Top-teamet**, väljer **Mina tilldelnings punkter överst till/** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Mina belönings punkter överst till-/topp-teamet i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med mina belönings punkter överst till höger/överst, baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i det översta under-och topp-teamet.

Om du vill konfigurera och testa enkel inloggning med Azure AD med mitt belönings punkter överst till höger/överst måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **Konfigurera mina belönings punkter överst under-och topp grupp enkel inloggning** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **Skapa mina belönings punkter överst under-och topp grupps test användare** – om du vill ha en motsvarighet till Britta Simon i mina belönings punkter övre/högsta-teamet som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med mina belönings punkter överst till höger/Top-teamet:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **Mina upphandlings platser överst/översta team** program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Mina belönings punkter överst under-och topp grupp domän och URL: er enkel inloggnings information](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > Värdet är inte verkligt. Du får `<Azure AD Identifier>` värdet i de senare stegen i den här självstudien.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På sidan **Konfigurera mina upphandlings platser överst** till höger i gruppen kopierar du lämpliga URL: er enligt ditt krav. 

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

    >[!NOTE]
    >Lägg till det kopierade ID-värdet för Azure AD med inloggnings-URL: en i stället för `<Azure AD Identifier>` i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Konfigurera mina belönings punkter överst under-och topp grupp enkel inloggning

Om du vill konfigurera enkel inloggning på **Mina belönings punkter överst till höger-/topp-gruppen** måste du skicka den hämtade **XML-koden för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [Mina belönings punkter överst till support teamet för sub/Top team](mailto:myawardpoints@biworldwide.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till mina belönings punkter överst till höger/översta teamet.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Mina tilldelnings punkter överst till-/topp-teamet**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du det **översta sub/Top-teamet**.

    ![Länken Mina uppdelnings punkter överst/överst i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Skapa mina belönings punkter överst till-grupp för sub/topp-test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i mitt belönings Points överst/högsta-teamet. Arbeta med [Mina belönings punkter överst stöd för sub/Top team support team](mailto:myawardpoints@biworldwide.com) för att lägga till användarna i den översta delen av under-och-huvud-teamet. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den övre delen av gruppen mina belönings punkter överst/översta gruppen på åtkomst panelen, bör du loggas in automatiskt på det översta under-och topp-teamet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
