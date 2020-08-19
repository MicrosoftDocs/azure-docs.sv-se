---
title: 'Självstudie: Azure Active Directory integrering med Picturepark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Picturepark.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: bb12b666315f6d30df64b33b6a87d6ce831fd225
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553867"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Självstudie: Azure Active Directory integrering med Picturepark

I den här självstudien får du lära dig hur du integrerar Picturepark med Azure Active Directory (Azure AD).
Genom att integrera Picturepark med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Picturepark.
* Du kan göra det möjligt för användarna att logga in automatiskt till Picturepark (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Picturepark behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Picturepark-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Picturepark stöder **SP** -INITIERAd SSO

## <a name="adding-picturepark-from-the-gallery"></a>Lägga till Picturepark från galleriet

Om du vill konfigurera integreringen av Picturepark i Azure AD måste du lägga till Picturepark från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Picturepark från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Picturepark**, väljer **Picturepark** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Picturepark i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Picturepark baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Picturepark upprättas.

Om du vill konfigurera och testa enkel inloggning med Picturepark i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Picturepark-enkel inloggning](#configure-picturepark-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Picturepark test User](#create-picturepark-test-user)** – om du vill ha en motsvarighet till Britta Simon i Picturepark som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Picturepark i Azure AD:

1. Välj **enkel inloggning**på sidan **Picturepark** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Picturepark-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.picturepark.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Picturepark client support team](https://picturepark.com/company/picturepark-customer-support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. I avsnittet **SAML-signerings certifikat** kopierar du **tumavtrycket** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera Picturepark** kopierar du lämpliga URL: er enligt ditt krav. För **inloggnings-URL**använder du värdet med följande mönster: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ är klient-ID: t för Azure AD-prenumerationen.

    ![Kopiera konfigurations-URL:er](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-identifierare

    b. Utloggnings-URL

### <a name="configure-picturepark-single-sign-on"></a>Konfigurera Picturepark enkel inloggning

1. Logga in på din Picturepark-företags webbplats som administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp klickar du på **administrations verktyg**och sedan på **hanterings konsol**.
   
    ![Hanterings konsol](./media/picturepark-tutorial/ic795062.png "Hanterings konsol")

3. Klicka på **autentisering**och sedan på **identitets leverantörer**.
   
    ![Autentisering](./media/picturepark-tutorial/ic795063.png "Autentisering")

4. I avsnittet **konfiguration av identitets leverantör** utför du följande steg:
   
    ![Konfiguration av identitetsprovider](./media/picturepark-tutorial/ic795064.png "Konfiguration av identitetsprovider")
   
    a. Klicka på **Lägg till**.
  
    b. Ange ett namn för din konfiguration.
   
    c. Välj **Ange som standard**.
   
    d. I text rutan **utfärdar-URI** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.
   
    e. Klistra **in värdet** för **tumavtryck** som du har kopierat från avsnittet **SAML-signeringscertifikat** 

5. Klicka på **JoinDefaultUsersGroup**.

6. Ange attributet **EmailAddress** i text rutan **anspråk** genom att skriva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` och klicka på **Spara**.

      ![Konfiguration](./media/picturepark-tutorial/ic795065.png "Konfiguration")

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Picturepark.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Picturepark**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Picturepark**.

    ![Picturepark-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-picturepark-test-user"></a>Skapa Picturepark test användare

För att Azure AD-användare ska kunna logga in på Picturepark måste de tillhandahållas i Picturepark. När det gäller Picturepark är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **Picturepark** -klient.

1. I verktygsfältet högst upp klickar du på **administrations verktyg**och sedan på **användare**.
   
    ![Användare](./media/picturepark-tutorial/ic795067.png "Användare")

1. Klicka på **nytt**på fliken **användare översikt** .
   
    ![Användarhantering](./media/picturepark-tutorial/ic795068.png "Användarhantering")

1. I dialog rutan **skapa användare** utför du följande steg för en giltig Azure Active Directory-användare som du vill etablera:
   
    ![Skapa användare](./media/picturepark-tutorial/ic795069.png "Skapa användare")
   
    a. I text rutan **e-postadress** anger du användarens **e-postadress** `BrittaSimon@contoso.com` .  
   
    b. I text rutorna **lösen** ord och **Bekräfta lösen ord** skriver du **lösen ordet** för BrittaSimon. 
   
    c. I text rutan **förnamn** skriver du det **första namnet** på användaren **Britta**. 
   
    d. I text rutan **efter namn** skriver du det **senaste namnet** på användaren **Simon**.
   
    e. I text rutan **företag** anger du användarens **företags namn** . 
   
    f. I text rutan **land** väljer du användarens **land/region** .
  
    ex. I **zip** -text rutan skriver du ortns **post nummer** .
   
    h. Skriv **Orts namnet** för användaren i text rutan **stad** .

    i. Välj ett **språk**.
   
    j. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda andra verktyg för Picturepark av användar konton eller API: er som tillhandahålls av Picturepark för att etablera Azure AD-användarkonton.
>

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Picturepark på åtkomst panelen, bör du loggas in automatiskt på den Picturepark som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

