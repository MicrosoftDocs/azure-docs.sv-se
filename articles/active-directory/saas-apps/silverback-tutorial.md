---
title: 'Självstudie: Azure Active Directory integrering med Silverback | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Silverback.
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
ms.openlocfilehash: 43f7cddfa6fb887fd421d1f90a41145c6182654d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517205"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>Självstudie: Azure Active Directory integrering med Silverback

I den här självstudien får du lära dig hur du integrerar Silverback med Azure Active Directory (Azure AD).
Genom att integrera Silverback med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Silverback.
* Du kan göra det möjligt för användarna att logga in automatiskt till Silverback (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Silverback behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Silverback-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Silverback stöder **SP** -INITIERAd SSO

## <a name="adding-silverback-from-the-gallery"></a>Lägga till Silverback från galleriet

Om du vill konfigurera integreringen av Silverback i Azure AD måste du lägga till Silverback från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Silverback från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Silverback**, väljer **Silverback** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Silverback i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Silverback baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Silverback upprättas.

Om du vill konfigurera och testa enkel inloggning med Silverback i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Silverback-enkel inloggning](#configure-silverback-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Silverback test User](#create-silverback-test-user)** – om du vill ha en motsvarighet till Britta Simon i Silverback som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Silverback i Azure AD:

1. Välj **enkel inloggning**på sidan **Silverback** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Silverback-domän och URL: er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<YOURSILVERBACKURL>.com/ssp`

    b. I rutan **identifierare** anger du en URL med följande mönster: `<YOURSILVERBACKURL>.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Silverback client support team](mailto:helpdesk@matrix42.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-silverback-single-sign-on"></a>Konfigurera Silverback enkel inloggning

1. Logga in på din Silverback-server som administratör i en annan webbläsare.

2. Gå till **admin**  >  **autentiseringsprovider**.

3. Utför följande steg på sidan **Inställningar för autentiseringsprovider** :

    ![Administratören](./media/silverback-tutorial/tutorial_silverback_admin.png)

    a.  Klicka på **Importera från URL**.

    b.  Klistra in den kopierade metadata-URL: en och klicka på **OK**.

    c.  Bekräfta med **OK** kommer värdena att fyllas i automatiskt.

    d.  Aktivera **Visa på inloggnings sidan**.

    e.  Aktivera **generering av dynamiskt användare** om du vill lägga till av Azure AD-auktoriserade användare automatiskt (valfritt).

    f.  Skapa en **rubrik** för knappen på självbetjänings portalen.

    ex.  Ladda upp en **ikon** genom att klicka på **Välj fil**.

    h.  Välj bakgrunds **färg** för knappen.

    i.  Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Silverback.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Silverback**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Silverback**.

    ![Silverback-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-silverback-test-user"></a>Skapa Silverback test användare

För att Azure AD-användare ska kunna logga in på Silverback måste de tillhandahållas i Silverback. I Silverback är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Silverback-servern som administratör.

2. Navigera till **användare** och **Lägg till en ny enhets användare**.

3. Utför följande steg på sidan **grundläggande** :

    ![Användaren](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. I text rutan **användar namn** anger du namnet på användaren som **Britta**.

    b. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    d. I text rutan e-postadress anger du e- **postadressen** till användaren **Brittasimon@contoso.com** .

    e. Ange ditt lösen ord i text rutan **lösen ord** .

    f. Ange lösen ordet på nytt i text rutan **Bekräfta lösen ord** och bekräfta.

    ex. Klicka på **Spara**.

> [!NOTE]
> Om du inte vill skapa varje användare manuellt aktiverar du kryss rutan för att **skapa dynamiska användare** under autentiseringsprovider för **Administration**  >  **Authentication Provider**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Silverback på åtkomst panelen, bör du loggas in automatiskt på den Silverback som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

