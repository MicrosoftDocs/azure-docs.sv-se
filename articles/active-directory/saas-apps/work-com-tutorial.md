---
title: 'Självstudie: Azure Active Directory integrering med Work.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Work.com.
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
ms.openlocfilehash: 5fe8697327203a84f91b42e8742db75150b6cb19
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88541471"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Självstudie: Azure Active Directory integrering med Work.com

I den här självstudien får du lära dig hur du integrerar Work.com med Azure Active Directory (Azure AD).
Genom att integrera Work.com med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Work.com.
* Du kan göra det möjligt för användarna att logga in automatiskt till Work.com (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Work.com behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Work.com-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Work.com stöder **SP** -INITIERAd SSO

## <a name="adding-workcom-from-the-gallery"></a>Lägga till Work.com från galleriet

Om du vill konfigurera integreringen av Work.com i Azure AD måste du lägga till Work.com från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Work.com från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Work.com**, väljer **Work.com** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Work.com i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Work.com baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Work.com upprättas.

Om du vill konfigurera och testa enkel inloggning med Work.com i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Work.com-enkel inloggning](#configure-workcom-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Work.com test User](#create-workcom-test-user)** – om du vill ha en motsvarighet till Britta Simon i Work.com som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

>[!NOTE]
>Om du vill konfigurera enkel inloggning måste du konfigurera ett anpassat domän namn för Work.com ännu. Du måste definiera minst ett domän namn, testa ditt domän namn och distribuera det till hela organisationen.

Utför följande steg för att konfigurera enkel inloggning med Work.com i Azure AD:

1. Välj **enkel inloggning**på sidan **Work.com** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Work.com-domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Work.com client support team](https://help.salesforce.com/articleView?id=000159855&type=3) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **konfigurera Work.com** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workcom-single-sign-on"></a>Konfigurera Work.com enkel inloggning

1. Logga in på Work.com-klienten som administratör.

2. Gå till **installations programmet**.
   
    ![Installation](./media/work-com-tutorial/ic794108.png "Installation")

3. Klicka på **domän hantering** i avsnittet **Administrera** i det vänstra navigerings fönstret för att expandera det relaterade avsnittet och klicka sedan på **min domän** för att öppna sidan **min domän** . 
   
    ![Min domän](./media/work-com-tutorial/ic767825.png "Min domän")

4. Kontrol lera att din domän har kon figurer ATS korrekt genom att se till att den är i "**steg 4 distribuerad till användare**" och granska dina "**Mina domän inställningar**".
   
    ![Domän distribuerad till användare](./media/work-com-tutorial/ic784377.png "Domän distribuerad till användare")

5. Logga in på din Work.com-klient.

6. Gå till **installations programmet**.
    
    ![Installation](./media/work-com-tutorial/ic794108.png "Installation")

7. Expandera menyn **säkerhets kontroller** och klicka sedan på **Inställningar för enkel inloggning**.
    
    ![Inställningar för enkel inloggning](./media/work-com-tutorial/ic794113.png "Inställningar för enkel inloggning")

8. På dialog sidan **Inställningar för enkel inloggning** utför du följande steg:
    
    ![SAML aktiverat](./media/work-com-tutorial/ic781026.png "SAML aktiverat")
    
    a. Välj **SAML-aktiverat **.
    
    b. Klicka på **nytt**.

9. I avsnittet **Inställningar för enkel inloggning i SAML** , utför följande steg:
    
    ![Inställning av SAML enkel inloggning](./media/work-com-tutorial/ic794114.png "Inställning av SAML enkel inloggning")
    
    a. I textrutan **Namn** skriver du ett namn för konfigurationen.  
       
    > [!NOTE]
    > Om du anger ett värde för **namnet** fylls text rutan för **API-namn** i automatiskt.
    
    b. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.
    
    c. Klicka på **Bläddra**om du vill ladda upp det hämtade certifikatet från Azure Portal.
    
    d. I text rutan **entitets-ID** skriver du `https://salesforce-work.com` .
    
    e. Som **SAML-identitets typ**väljer du **Assertion innehåller Federations-ID: t från användarobjektet**.
    
    f. Som **SAML-identitets plats**väljer du **identitet i NameIdentfier-elementet för ämnes instruktionen**.
    
    ex. I text rutan **inloggnings-URL för identitetsprovider** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    h. I text rutan **Logga in URL för identitets leverantör** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.
    
    i. Välj **http post**som **tjänst leverantör initierad begär ande bindning**.
    
    j. Klicka på **Spara**.

10. I den klassiska Work.com-portalen i det vänstra navigerings fönstret klickar du på **domän hantering** för att expandera det relaterade avsnittet och klickar sedan på **min domän** för att öppna sidan **min domän** . 
    
    ![Min domän](./media/work-com-tutorial/ic794115.png "Min domän")

11. På sidan **min domän** i avsnittet anpassning av **inloggnings sidan** klickar du på **Redigera**.
    
    ![Anpassning av inloggnings Sidan](./media/work-com-tutorial/ic767826.png "Anpassning av inloggnings Sidan")

12. På sidan **anpassning av inloggnings sidan** i avsnittet **Authentication Service** visas namnet på dina inställningar för **SAML SSO** . Markera det och klicka sedan på **Spara**.
    
    ![Anpassning av inloggnings Sidan](./media/work-com-tutorial/ic784366.png "Anpassning av inloggnings Sidan")

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Work.com.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Work.com**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Work.com**.

    ![Work.com-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workcom-test-user"></a>Skapa Work.com test användare

För att Azure Active Directory användare ska kunna logga in måste de tillhandahållas till Work.com. När det gäller Work.com är etableringen en manuell uppgift.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din Work.com-företags webbplats som administratör.

2. Gå till **installations programmet**.
   
    ![Installation](./media/work-com-tutorial/IC794108.png "Installation")

3. Gå till **Hantera användare \> användare**.
   
    ![Hantera användare](./media/work-com-tutorial/IC784369.png "Hantera användare")

4. Klicka på **Ny användare**.
   
    ![Alla användare](./media/work-com-tutorial/IC794117.png "Alla användare")

5. I avsnittet Redigera användare utför du följande steg i attribut för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna:
   
    ![Redigera användare](./media/work-com-tutorial/ic794118.png "Redigera användare")
   
    a. I text rutan **förnamn** skriver du det **första namnet** på användaren **Britta**.
    
    b. I text rutan **efter namn** skriver du det **senaste namnet** på användaren **Simon**.
    
    c. Skriv **namnet** på användaren **BrittaS**i text rutan **alias** .
    
    d. Skriv **e-postadressen** för användaren i text rutan för **e-post** Brittasimon@contoso.com .
    
    e. I text rutan **användar namn** anger du ett användar namn för användaren Brittasimon@contoso.com .
    
    f. I text rutan **namn på Nick** anger du ett Nick- **namn** för User **Simon**.
    
    ex. Välj **roll**, **användar licens**och **profil**.
    
    h. Klicka på **Spara**.  
      
    > [!NOTE]
    > Azure AD-konto innehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    > 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Work.com på åtkomst panelen, bör du loggas in automatiskt på den Work.com som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

