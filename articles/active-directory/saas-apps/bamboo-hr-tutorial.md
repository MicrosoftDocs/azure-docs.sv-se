---
title: 'Självstudie: Azure Active Directory integrering med BambooHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 9e733aa9f609928f8129123c4286cfa98e51e1d4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549491"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Självstudie: Azure Active Directory integrering med BambooHR

I de här självstudierna får du lära dig hur du integrerar BambooHR med Azure Active Directory (Azure AD).
Genom att integrera BambooHR med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till BambooHR i Azure AD.
* Du kan göra så att dina användare loggas in automatiskt på BambooHR (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera integrering av Azure AD med BambooHR, behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* BambooHR-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* BambooHR stöder **SP**-initierad enkel inloggning

## <a name="adding-bamboohr-from-the-gallery"></a>Lägga till BambooHR från galleriet

För att konfigurera integreringen av BambooHR i Azure AD, behöver du lägga till BambooHR från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BambooHR från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **BambooHR**, väljer **BambooHR** i resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![BambooHR i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD på BambooHR baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i BambooHR upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD på BambooHR, måste du slutföra följande steg:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning på BambooHR](#configure-bamboohr-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en testanvändare för BambooHR](#create-bamboohr-test-user)** – för att få en motsvarighet till Britta Simon i BambooHR som är länkad till en Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD på BambooHR:

1. I [Azure Portal](https://portal.azure.com/), på sidan för **BambooHR**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning på BambooHR-domäner och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.bamboohr.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `BambooHR-SAML`

    > [!NOTE]
    > Värdet för **inloggnings-URL** är inte verkligt. Uppdatera värdet med faktiskt inloggnings-URL. Kontakta [BambooHR-klientens supportteam](https://www.bamboohr.com/contact.php) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera BambooHR** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bamboohr-single-sign-on"></a>Konfigurera enkel inloggning på BambooHR

1. I ett nytt webbläsarfönster loggar du in på din BambooHR-företagsplats som administratör.

2. Gör följande på startsidan:
   
    ![Sidan för enkel inloggning med BambooHR](./media/bamboo-hr-tutorial/ic796691.png "för Aha!")   

    a. Välj **Appar**.
   
    b. I fönstret **Appar** väljer du **Enkel inloggning**.
   
    c. Välj **Enkel inloggning med SAML**.

3. I fönstret **Enkel inloggning med SAML** gör du följande:
   
    ![Fönstret för enkel inloggning i SAML](./media/bamboo-hr-tutorial/IC796692.png "SAML enkel inloggning")
   
    a. I rutan för **inloggnings-Url för enkel inloggning** klistrar du in den **inloggnings-URL** som du kopierade från Azure-portalen i steg 6.
      
    b. I Anteckningar öppnar du det base64-kodade certifikat som du laddade ned från Azure-portalen, kopierar innehållet och klistrar sedan in det i rutan för **X.509-certifikatet**.
   
    c. Välj **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till BambooHR.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **BambooHR**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **BambooHR**.

    ![BambooHR-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bamboohr-test-user"></a>Skapa BambooHR-testanvändare

Om du vill göra det möjligt för Azure AD-användare att logga in på BambooHR, konfigurerar du dem manuellt i BambooHR genom att göra följande:

1. Logga in på din **BambooHR**-webbplats som administratör.

2. Välj **Inställningar** i verktygsfältet högst upp.
   
    ![Knappen Inställningar](./media/bamboo-hr-tutorial/IC796694.png "Inställning")

3. Välj **Översikt**.

4. I det vänstra fönstret väljer du **säkerhets**  >  **användare**.

5. Skriv användarnamn, lösenord och e-postadress för det giltiga Azure AD-konto som du vill konfigurera.

6. Välj **Spara**.
        
>[!NOTE]
>Om du vill konfigurera Azure AD-användarkonton kan du också använda verktyg för att skapa BambooHR-användarkonton eller API:er.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på BambooHR-panelen i åtkomstpanelen bör du automatiskt loggas in på BambooHR som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

