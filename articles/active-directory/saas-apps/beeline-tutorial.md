---
title: 'Självstudie: Azure Active Directory integrering med Beeline | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Beeline.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 571ab3c3943faf6ef5cbccb401a5416c327e3975
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537858"
---
# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Självstudie: Azure Active Directory integrering med Beeline

I den här självstudien får du lära dig hur du integrerar Beeline med Azure Active Directory (Azure AD).
Genom att integrera Beeline med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Beeline.
* Du kan göra det möjligt för användarna att logga in automatiskt till Beeline (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Beeline behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Beeline-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Beeline stöder endast **IDP** INITIERAd SSO

## <a name="adding-beeline-from-the-gallery"></a>Lägga till Beeline från galleriet

Om du vill konfigurera integreringen av Beeline i Azure AD måste du lägga till Beeline från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Beeline från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Beeline**, väljer **Beeline** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Beeline i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Beeline baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Beeline upprättas.

Om du vill konfigurera och testa enkel inloggning med Beeline i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Beeline-enkel inloggning](#configure-beeline-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Beeline test User](#create-beeline-test-user)** – om du vill ha en motsvarighet till Britta Simon i Beeline som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Beeline i Azure AD:

1. Välj **enkel inloggning**på sidan **Beeline** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om enkel inloggning med BeeLine-domän och URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://projects.beeline.com/<ProjInstanceName>`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ```https
    https://projects.beeline.com/<ProjInstanceName>/SSO_External.ashx
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Beeline client support team](https://www.beeline.com/support-beeline/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Beeline-programmet förväntar sig SAML-intyg i ett särskilt format. Arbeta först med [Beeline support-teamet](https://www.beeline.com/support-beeline/) för att identifiera rätt användar-ID som ska mappas till programmet. Ta även vägledning från [Beeline support team](https://www.beeline.com/support-beeline/) om attributet som de vill använda för den här mappningen. Du kan hantera värdet för det här attributet på fliken **Användarattribut** i programmet. Följande skärmbild visar ett exempel på detta. Här har vi mappat **användar-ID** -anspråket med attributet **userPrincipalName** , som innehåller ett unikt användar-ID, som skickas till Beeline-programmet i varje lyckat SAML-svar.

    ![image](common/edit-attribute.png)

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I [Azure Portal](https://portal.azure.com/)på sidan **Beeline** program integration väljer du **Egenskaper** och kopierar URL för användar åtkomst.

    ![Kopiera URL för användar åtkomst](media/beeline-tutorial/client-access-url.png)


### <a name="configure-beeline-single-sign-on"></a>Konfigurera Beeline enkel inloggning

Om du vill konfigurera enkel inloggning på **Beeline** sida måste du skicka den hämtade **XML-koden för federationsmetadata** och URL: en för användar åtkomst från Azure Portal egenskaper till [Beeline support team](https://www.beeline.com/support-beeline/). De kräver webb adressen för metadata och användar åtkomst så att SAML SSO-anslutningen är korrekt konfigurerad på båda sidor.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Beeline.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Beeline**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Beeline**.

    ![Beeline-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-beeline-test-user"></a>Skapa Beeline test användare

I det här avsnittet ska du skapa en användare, Britta Simon, i Beeline. Beeline-programmet behöver alla användare som ska tillhandahållas i programmet innan de kan utföra enkel inloggning. Så arbeta med [Beeline support team](https://www.beeline.com/support-beeline/) för att etablera alla dessa användare i programmet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Beeline på åtkomst panelen, bör du loggas in automatiskt på Beeline-instansen där du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
