---
title: 'Självstudie: Azure Active Directory integrering med Samanage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Samanage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 56018ff0be07a48cf9448b9b92de5694ebac18bc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543541"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Självstudie: Azure Active Directory integrering med Samanage

I den här självstudien lär du dig att integrera Samanage med Azure Active Directory (AD Azure).
Genom att integrera Samanage med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Samanage.
* Du kan göra så att dina användare automatiskt loggas in på Samanage (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Samanage behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Samanage-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Samanage stöder **IDP**-initierad enkel inloggning

## <a name="adding-samanage-from-the-gallery"></a>Lägga till Samanage från galleriet

För att konfigurera integreringen av Samanage till Azure AD behöver du lägga till Samanage från galleriet till listan över hanterade SaaS-appar.

**Lägg till Samanage från galleriet genom att utföra följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Samanage**, väljer **Samanage** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Samanage i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med Samanage baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Samanage upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Samanage behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Samanage](#configure-samanage-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Samanage-testanvändare](#create-samanage-test-user)** – för att ha en motsvarighet för Britta Simon i Samanage som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Samanage:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Samanage**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Samanage-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Detta förklaras senare i självstudien. Kontakta [supportteamet för Samanage-klienten](https://www.samanage.com/support) om du behöver mer information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Samanage** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-samanage-single-sign-on"></a>Konfigurera enkel inloggning för Samanage

1. I ett annat webbläsarfönster loggar du in på din Samanage-företagsplats som administratör.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Instrumentpanel](./media/samanage-tutorial/tutorial_samanage_001.png "Instrumentpanel")

3. Klicka på **Enkel inloggning**.
   
    ![Enkel inloggning](./media/samanage-tutorial/tutorial_samanage_002.png "för Aha!")

4. Gå till avsnittet **Login using SAML** (Logga in via SAML) och utför följande steg:
   
    ![Logga in med SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Logga in med SAML")
 
    a. Klicka på **Enable Single Sign-On with SAML** (Aktivera enkel inloggning med SAML).  
 
    b. I textrutan **Identity Provider URL** (URL för identitetsprovider) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.    
 
    c. Bekräfta att **inloggnings-URL:en** matchar **inloggnings-URL:en** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
 
    d. I textrutan för **utloggnings-URL:en** anger du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.
 
    e. I textrutan **SAML Issuer** (SAML-utfärdare) skriver du in den URI för app-ID som angetts i din identitetsprovider.
 
    f. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **Paste your Identity Provider x.509 Certificate below** (Klistra in x.509-certifikat för din identitetsprovider nedan).
 
    ex. Klicka på **Create users if they do not exist in Samanage** (Skapa användare om de inte finns i Samanage).
 
    h. Klicka på **Uppdatera**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Samanage.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Samanage**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Samanage**.

    ![Samanage-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-samanage-test-user"></a>Skapa Samanage-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Samanage måste de etableras i Samanage.  
När det gäller Samanage är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din Samanage-företagsplats som administratör.

2. Klicka på **Instrumentpanelen** och välj **Konfiguration** i det vänstra navigeringsfönstret.
   
    ![Installation](./media/samanage-tutorial/tutorial_samanage_001.png "Installation")

3. Klicka på fliken **Användare**
   
    ![Användare](./media/samanage-tutorial/tutorial_samanage_006.png "Användare")

4. Klicka på **Ny användare**.
   
    ![Ny användare](./media/samanage-tutorial/tutorial_samanage_007.png "Ny användare")

5. Skriv **Namn** och **E-postadress** för ett Azure Active Directory-konto som du vill etablera och klicka på **Skapa användare**.
   
    ![Skapa användare](./media/samanage-tutorial/tutorial_samanage_008.png "Skapa användare")
   
   >[!NOTE]
   >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt. Du kan använda andra verktyg eller API:er för Samanage-kontoskapande som tillhandahålls av Samanage för att etablera Azure Active Directory-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Samanage-panelen i åtkomstpanelen bör du automatiskt loggas in på Samanage som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

