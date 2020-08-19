---
title: 'Självstudie: Azure Active Directory integrering med Mozy Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mozy Enterprise.
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
ms.openlocfilehash: c9c4cef07bf1de64bcb14bc4487e0f56d2a40dfc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518652"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Självstudie: Azure Active Directory integrering med Mozy Enterprise

I den här självstudien får du lära dig att integrera Mozy Enterprise med Azure Active Directory (Azure AD).
Genom att integrera Mozy Enterprise med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Mozy Enterprise.
* Du kan göra det möjligt för användarna att logga in automatiskt till Mozy Enterprise (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Mozy Enterprise behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise Single Sign-on-aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Mozy Enterprise stöder **SP** -INITIERAd SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Lägga till Mozy Enterprise från galleriet

Om du vill konfigurera integrationen av Mozy Enterprise i Azure AD måste du lägga till Mozy Enterprise från galleriet i listan över hanterade SaaS-appar.

**Gör så här för att lägga till Mozy Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Mozy Enterprise**, väljer **Mozy Enterprise** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Mozy Enterprise i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Mozy Enterprise baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Mozy Enterprise.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Mozy Enterprise måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Mozy Enterprise Single Sign-on](#configure-mozy-enterprise-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Mozy Enterprise test User](#create-mozy-enterprise-test-user)** – om du vill ha en motsvarighet till Britta Simon i Mozy Enterprise som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Mozy Enterprise:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Mozy Enterprise** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Mozy företags domän och URL: er](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Mozy Enterprise client support team](https://support.mozy.com/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Mozy Enterprise** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurera Mozy Enterprise Single Sign-on

1. Logga in på företagets företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **autentiseringsprincip**i avsnittet **konfiguration** .
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777314.png "Autentiseringsprincip")

3. Utför följande steg i avsnittet **autentiseringsprincip** :
   
    ![Autentiseringsprincip](./media/mozy-enterprise-tutorial/ic777315.png "Autentiseringsprincip")
   
    a. Välj **katalog tjänst** som **Provider**.
   
    b. Välj **Använd LDAP-push**.
   
    c. Klicka på fliken **SAML-autentisering**.
   
    d. Klistra in **inloggnings-URL**, som du har kopierat från Azure Portal till text rutan för **autentiserings-URL** .
   
    e. Klistra in **Azure AD-identifierare**, som du har kopierat från Azure Portal till text rutan **SAML-slutpunkt** .
   
    f. Öppna det hämtade Base-64-kodade certifikatet i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in hela certifikatet i text rutan för **SAML-certifikatet** .
   
    ex. Välj **Aktivera SSO för administratörer för att logga in med sina Nätverksautentiseringsuppgifter**.
   
    h. Klicka på **Spara ändringar**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Mozy Enterprise.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Mozy Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Mozy Enterprise**.

    ![Mozy Enterprise-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mozy-enterprise-test-user"></a>Skapa Mozy Enterprise test User

För att Azure AD-användarna ska kunna logga in på Mozy Enterprise måste de tillhandahållas i Mozy Enterprise. När det gäller Mozy Enterprise är etableringen en manuell uppgift.

>[!NOTE]
>Du kan använda andra Mozy Enterprise User Account-verktyg eller API: er från Mozy Enterprise för att etablera Azure AD-användarkonton.

**Utför följande steg för att tillhandahålla ett användarkonto:**

1. Logga in på din **Mozy Enterprise** -klient.

2. Klicka på **användare**och sedan på **Lägg till ny användare**.
   
    ![Användare](./media/mozy-enterprise-tutorial/ic777317.png "Användare")
   
    >[!NOTE]
    >Alternativet **Lägg till ny användare** visas bara om **Mozy** har valts som Provider under **autentiseringsprincip**. Om SAML-autentisering har kon figurer ATS läggs användarna automatiskt till första inloggningen via enkel inloggning.
    
3. Utför följande steg i dialog rutan ny användare:
   
    ![Lägg till användare](./media/mozy-enterprise-tutorial/ic777318.png "Lägg till användare")
   
    a. Välj en grupp i listan **Välj en grupp** .
   
    b. Välj en typ i listan **vilken typ av användare som** används.
   
    c. I text rutan **användar namn** anger du namnet på Azure AD-användaren.
   
    d. Skriv e-postadressen för Azure AD-användaren i text rutan för **e-post** .
   
    e. Välj **skicka e-post för användar instruktioner**.
   
    f. Klicka på **Lägg till användare**.

     >[!NOTE]
     > När användaren har skapats skickas ett e-postmeddelande till den Azure AD-användare som innehåller en länk för att bekräfta kontot innan det aktive ras.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Mozy Enterprise på åtkomst panelen, bör du loggas in automatiskt till det Mozy-företag som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

