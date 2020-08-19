---
title: 'Självstudie: Azure Active Directory integration med bonus | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och bonus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: fd82d3d2d2a76e0c0671c57d9254ff8c9030d3a3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548322"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Självstudie: Azure Active Directory integration med bonus

I den här självstudien får du lära dig hur du integrerar bonus med Azure Active Directory (Azure AD).
Att integrera bonus med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till bonus.
* Du kan göra det möjligt för dina användare att vara automatiskt inloggade i bonus (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bonus behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Bonus för enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bonus stöder **IDP** -INITIERAd SSO

## <a name="adding-bonusly-from-the-gallery"></a>Lägga till bonus från galleriet

Om du vill konfigurera en bonus integrering i Azure AD måste du lägga till bonus från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till bonus från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **bonus**och väljer **bonus** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Bonus i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med bonus baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i bonus upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med bonus måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med bonus](#configure-bonusly-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en bonus som testar användaren](#create-bonusly-test-user)** för att få en motsvarighet till Britta Simon i bonus som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med bonus:

1. I [Azure Portal](https://portal.azure.com/)på sidan **bonus** program integrering väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med bonus domän och URL: er](common/idp-reply.png)

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [support teamet för bonus](https://bonus.ly/contact) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. På sidan **Konfigurera bonus** , kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bonusly-single-sign-on"></a>Konfigurera enkel inloggning med bonus

1. Logga in på din **bonus** klient i ett annat webbläsarfönster.

1. I verktygsfältet högst upp klickar du på **Inställningar** och väljer sedan **integreringar och appar**.

    ![Bonus för sociala avdelningen](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **enkel inloggning**väljer du **SAML**.

1. Utför följande steg på sidan **SAML** -dialog:

    ![Egenskaps sida för bonus SAML](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. I text rutan **IDP SSO target URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    b. I text rutan för **inloggnings-URL för IDP** klistrar du in värdet för **inloggnings-URL**, som du har kopierat från Azure Portal.

    c. I text rutan **IDP Issuer** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.
    
    d. Klistra in det **tumavtryck** -värde som kopierats från Azure Portal i text rutan för **certifikat finger avtryck** .
    
    e. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till bonus.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **bonus**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **bonus**.

    ![Bonus länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bonusly-test-user"></a>Skapa en bonus test användare

För att Azure AD-användare ska kunna logga in på bonus måste de tillhandahållas i bonus. När det gäller bonus är etableringen en manuell uppgift.

> [!NOTE]
> Du kan använda andra bonus verktyg för användar konton eller API: er som tillhandahålls av bonus för att etablera Azure AD-användarkonton. 

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din bonus klient i ett webbläsarfönster.

1. Klicka på **Inställningar**.

    ![Inställningar](./media/bonus-tutorial/ic781041.png "Inställningar")

1. Klicka på fliken **användare och bonusar** .

    ![Användare och bonusar](./media/bonus-tutorial/ic781042.png "Användare och bonusar")

1. Klicka på **Hantera användare**.

    ![Hantera användare](./media/bonus-tutorial/ic781043.png "Hantera användare")

1. Klicka på **Lägg till användare**.

    ![Lägg till användare](./media/bonus-tutorial/ic781044.png "Lägg till användare")

1. I dialogrutan **Lägg till användare** utför du följande steg:

    ![Lägg till användare](./media/bonus-tutorial/ic781045.png "Lägg till användare")  

    a. I text rutan för det **första namnet** anger du det första namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. I textrutan **E-post** anger du e-postadressen för användaren, t.ex. `brittasimon\@contoso.com`.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Azure AD-kontonamnet får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktivt.  

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den bonus panelen i åtkomst panelen, bör du loggas in automatiskt på den bonus som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
