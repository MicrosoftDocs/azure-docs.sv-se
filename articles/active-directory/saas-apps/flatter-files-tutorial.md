---
title: 'Självstudie: Azure Active Directory integrering med Flatter-filer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Flatter Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 7dd13df6222f6441b7edb2bd9a789c1f102168d7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554982"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Självstudie: Azure Active Directory integrering med Flatter-filer

I den här självstudien lär du dig att integrera Flatter Files med Azure Active Directory (AD Azure).
Integreringen av Flatter Files med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Flatter Files.
* Du kan göra så att dina användare loggas in automatiskt på Flatter Files (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Flatter Files behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Flatter Files-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Flatter Files har stöd för **IDP**-initierad enkel inloggning

## <a name="adding-flatter-files-from-the-gallery"></a>Lägga till Flatter Files från galleriet

För att konfigurera integreringen av Flatter Files i Azure AD måste du lägga till Flatter Files från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Flatter Files från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Flatter Files**, väljer **Flatter Files** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Flatter Files i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Flatter Files baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Flatter Files upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Flatter Files behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Flatter Files](#configure-flatter-files-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Flatter Files-testanvändare](#create-flatter-files-test-user)** – för att ha en motsvarighet för Britta Simon i Flatter Files som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Flatter Files:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Flatter Files** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Flatter Files-domän och information om URL:er för enkel inloggning](common/preintegrated.png)

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Flatter Files** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurera enkel inloggning för Flatter Files

1. Logga in på ditt Flatter Files-program som administratör.

2. Klicka på **DASHBOARD** (Instrumentpanel). 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klicka på **Inställningar** och utför följande steg på fliken **Företag**: 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Välj **Use SAML 2.0 for Authentication** (Använd SAML 2.0 för autentisering).
    
    b. Klicka på **Konfigurera SAML**.

4. I dialogrutan **SAML-konfiguration** utför du följande steg: 
   
    ![Konfigurera enkel inloggning](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. I textrutan **Domän** skriver du din registrerade domän.
   
   > [!NOTE]
   > Om du inte har en registrerad domän ännu kontaktar du Flatter-filerna support team via [support@flatterfiles.com](mailto:support@flatterfiles.com) . 
    
    b. I textrutan **Identity Provider URL** (URL för identitetsprovider) klistrar du in det värde för **inloggnings-URL** som du har kopierat från Azure-portalen.
   
    c.  Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **identitets leverantörs certifikat** .

    d. Klicka på **Uppdatera**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Flatter Files.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Flatter Files**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Flatter Files**.

    ![Länken för Flatter Files i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-flatter-files-test-user"></a>Skapa Flatter Files-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Flatter Files.

**Skapa en användare med namnet Britta Simon i Flatter Files genom att utföra följande steg:**

1. Logga in på din **Flatter Files**-företagswebbplats som administratör.

2. I navigeringsfönstret till vänster klickar du på **Inställningar** och sedan på fliken **Användare**.
   
    ![Skapa en Flatter Files-användare](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klicka på **Lägg till användare**. 

4. I dialogrutan **Lägg till användare** utför du följande steg:
   
    ![Skapa en Flatter Files-användare](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. I text rutan för det **första namnet** skriver du **Britta**.
   
    b. Skriv **Simon**i text rutan **efter namn** . 
   
    c. I textrutan **E-postadress** anger du Brittas e-postadress i Azure-portalen.
   
    d. Klicka på **Skicka**.   


### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Flatter Files-panelen i åtkomstpanelen bör du automatiskt loggas in på Flatter Files som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

