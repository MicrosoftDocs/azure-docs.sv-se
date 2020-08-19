---
title: 'Självstudie: Azure Active Directory integrering med UserVoice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och UserVoice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 3313e923bbe5218a965c58d2faee810182c00aa6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532864"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Självstudie: Azure Active Directory integrering med UserVoice

I den här självstudien får du lära dig hur du integrerar UserVoice med Azure Active Directory (Azure AD).
Att integrera UserVoice med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till UserVoice.
* Du kan göra det möjligt för användarna att logga in automatiskt till UserVoice (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med UserVoice behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Aktive rad prenumeration för enkel inloggning i UserVoice

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* UserVoice stöder **SP** -INITIERAd SSO

## <a name="adding-uservoice-from-the-gallery"></a>Lägga till UserVoice från galleriet

Om du vill konfigurera en integrering av UserVoice i Azure AD måste du lägga till UserVoice från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till UserVoice från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **UserVoice**, väljer **UserVoice** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![UserVoice i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med UserVoice baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i UserVoice.

Om du vill konfigurera och testa enkel inloggning med UserVoice i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera](#configure-uservoice-single-sign-on)** enkel inloggning för UserVoice för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa UserVoice test User](#create-uservoice-test-user)** -om du vill ha en motsvarighet till Britta Simon i UserVoice som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med UserVoice för Azure AD:

1. På sidan för **UserVoice** -programintegration i [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för UserVoice-domäner och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.UserVoice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [UserVoice-klientens support team](https://www.uservoice.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signerings certifikat** kopierar du **tumavtrycket** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera UserVoice** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-uservoice-single-sign-on"></a>Konfigurera enkel inloggning för UserVoice

1. Logga in på din UserVoice-företags webbplats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på **Inställningar**och väljer sedan **webb Portal** på menyn.
   
    ![Avsnittet Inställningar på App-sidan](./media/uservoice-tutorial/ic777519.png "Inställningar")

3. Öppna dialog sidan **Redigera användarautentisering** genom att klicka på **Redigera** i avsnittet **User Authentication** på fliken **webb Portal** .
   
    ![Fliken webb Portal](./media/uservoice-tutorial/ic777520.png "Webbportalen")

4. Utför följande steg på dialog sidan **Redigera användarautentisering** :
   
    ![Redigera användarautentisering](./media/uservoice-tutorial/ic777521.png "Redigera användarautentisering")
   
    a. Klicka på **enkel inloggning (SSO)**.
 
    b. Klistra in värdet för **inloggnings-URL** : en som du har kopierat från Azure Portal till inloggnings text rutan för **enkel** inloggning.

    c. Klistra in **URL** -värdet för utloggning, som du har kopierat från Azure Portal till inloggnings **text för enkel inloggning**.
 
    d. Klistra in det **tumavtryck** -värde som du kopierade från Azure Portal till den **aktuella certifikat-finger avtrycks** rutan.
    
    e. Klicka på **Spara autentiseringsinställningar**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till UserVoice.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **UserVoice**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **UserVoice**.

    ![UserVoice-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-uservoice-test-user"></a>Skapa UserVoice test användare

Om du vill att Azure AD-användare ska kunna logga in på UserVoice måste de tillhandahållas i UserVoice. När det gäller UserVoice är etableringen en manuell uppgift.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **UserVoice** -klient.

2. Gå till **Inställningar**.
   
    ![Inställningar](./media/uservoice-tutorial/ic777811.png "Inställningar")

3. Klicka på **Allmänt**.

4. Klicka på **agenter och behörigheter**.
   
    ![Agenter och behörigheter](./media/uservoice-tutorial/ic777812.png "Agenter och behörigheter")

5. Klicka på **Lägg till administratörer**.
   
    ![Lägg till administratörer](./media/uservoice-tutorial/ic777813.png "Lägg till administratörer")

6. I dialog rutan **Bjud in administratörer** utför du följande steg:
   
    ![Bjud in administratörer](./media/uservoice-tutorial/ic777814.png "Bjud in administratörer")
   
    a. Skriv e-postadressen för det konto som du vill etablera i text rutan e-postmeddelanden och klicka sedan på **Lägg till**.
   
    b. Klicka på **Bjud in**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa användar konton eller API: er från UserVoice för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen UserVoice i åtkomst panelen bör du loggas in automatiskt på UserVoice som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

