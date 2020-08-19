---
title: 'Självstudie: Azure Active Directory integrering med Questetra BPM Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Questetra BPM Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 11a6df348ae623ccf75e61bc1abbb5e28c832237
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548981"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Självstudie: Azure Active Directory integrering med Questetra BPM Suite

I den här självstudien får du lära dig att integrera Questetra BPM Suite med Azure Active Directory (Azure AD).
Genom att integrera Questetra BPM Suite med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Questetra BPM Suite.
* Du kan göra det möjligt för användarna att logga in automatiskt till Questetra BPM Suite (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Questetra BPM Suite behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Questetra BPM Suite enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Questetra BPM Suite stöder **SP** -INITIERAd SSO

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Lägga till Questetra BPM Suite från galleriet

Om du vill konfigurera integreringen av Questetra BPM Suite i Azure AD måste du lägga till Questetra BPM Suite från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Questetra BPM Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **QUESTETRA BPM Suite**, väljer **Questetra BPM Suite** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Questetra BPM Suite i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med Questetra BPM Suite baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Questetra BPM Suite upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Questetra BPM Suite måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera QUESTETRA BPM Suite Single Sign-on](#configure-questetra-bpm-suite-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa QUESTETRA BPM Suite-test användare](#create-questetra-bpm-suite-test-user)** – om du vill ha en motsvarighet till Britta Simon i Questetra BPM Suite som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Questetra BPM Suite:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Questetra BPM Suite** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Questetra BPM Suite-domän och enkel inloggnings information för URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Du kan hämta dessa värden från avsnittet **SP information** på din företags webbplats för **Questetra BPM Suite** , som beskrivs senare i självstudien eller kontakta [support teamet för BPM Suite-klienten](https://www.questetra.com/contact/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera QUESTETRA BPM Suite** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurera Questetra BPM Suite enkel inloggning

1. Logga in på företags platsen för **QUESTETRA BPM Suite** som administratör i ett annat webbläsarfönster.

2. Klicka på **Systeminställningar**på menyn högst upp. 
   
    ![Enkel inloggning för Azure AD][10]

3. Öppna sidan **SingleSignOnSAML** genom att klicka på **SSO (SAML)**. 
   
    ![Enkel inloggning för Azure AD][11]

4. På din företags webbplats för **QUESTETRA BPM Suite** går du till avsnittet **SP information** och utför följande steg:

    a. Kopiera **ACS-URL**och klistra in den i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.
    
    b. Kopiera **entitets-ID: t**och klistra in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

5. Utför följande steg på din företags webbplats för **QUESTETRA BPM Suite** : 
   
    ![Konfigurera enkel inloggning][15]
   
    a. Välj **aktivera enkel inloggning**.
   
    b. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.
    
    c. I text rutan **URL för inloggnings sida** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.
    
    d. I text rutan URL för utloggnings **sida** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure Portal.
    
    e. I text rutan **NameID format** skriver du `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    f. Öppna ditt **bas-64-** kodade certifikat i anteckningar som hämtats från Azure Portal, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **verifierings certifikat** . 

    ex. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Questetra BPM Suite.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Questetra BPM Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **QUESTETRA BPM Suite**.

    ![Questetra BPM Suite-länk i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-questetra-bpm-suite-test-user"></a>Skapa Questetra BPM Suite-test användare

Syftet med det här avsnittet är att skapa en användare med namnet Britta Simon i Questetra BPM Suite.

**Utför följande steg för att skapa en användare med namnet Britta Simon i Questetra BPM Suite:**

1. Logga in på företags platsen för Questetra BPM Suite som administratör.

2. Gå till **Systeminställningar > användar lista > ny användare**.
 
3. Utför följande steg i dialog rutan ny användare: 
   
    ![Skapa test användare][300] 
   
    a. Skriv **namnet** på användaren i text rutan **namn** britta.simon@contoso.com .
   
    b. Skriv **e-post** för användaren i text rutan **e-post** britta.simon@contoso.com .
   
    c. Skriv ett **lösen ord** för användaren i text rutan **lösen ord** .
    
    d. Klicka på **Lägg till ny användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Questetra BPM Suite på åtkomst panelen, bör du loggas in automatiskt till Questetra BPM Suite som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png