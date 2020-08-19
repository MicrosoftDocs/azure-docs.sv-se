---
title: 'Självstudie: Azure Active Directory integrering med snö flingor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 485e55d7a17027fa8b8ec1f313a326ddbc5f40eb
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545227"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Självstudie: Azure Active Directory integrering med snö flingor

I den här självstudien lär du dig att integrera Snowflake med Azure Active Directory (Azure AD).
Genom att integrera Snowflake med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Snowflake från Azure AD.
* Du kan låta dina användare automatiskt loggas in på Snowflake (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Snowflake behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Snowflake-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Snowflake har stöd för **SP- och IDP**-initierad enkel inloggning
* Snö har stöd för [Automatisk användar etablering och avetablering](snowflake-provisioning-tutorial.md) (rekommenderas)

## <a name="adding-snowflake-from-the-gallery"></a>Lägga till Snowflake från galleriet

För att konfigurera integrering av Snowflake i Azure AD behöver du lägga till Snowflake från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Snowflake från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Snowflake** i sökrutan, välj **Snowflake** i resultatpanelen och klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![Snowflake i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Snowflake baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Snowflake upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med hjälp av Snowflake, behöver du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Snowflake](#configure-snowflake-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Snowflake-testanvändare](#create-snowflake-test-user)** – för att ha en motsvarighet för Britta Simon i Snowflake som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med hjälp av Snowflake:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Snowflake** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg, om du vill konfigurera programmet i **IDP** initierat läge:

    ![Information om enkel inloggning med Snowflake-domäner och URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i SP-initierat läge:

    ![Information om enkel inloggning med Snowflake-domäner och URL:er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    I text rutan **utloggnings-URL** skriver du en URL med följande mönster:  `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Hämta värdena genom att kontakta [supportteamet för Snowflake-klienten](https://support.snowflake.net/s/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Snowflake** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-snowflake-single-sign-on"></a>Konfigurera enkel inloggning för Snowflake

1. Logga in på Snowflake som säkerhetsadministratör i ett annat webbläsarfönster.

1. **Växla roll** till **ACCOUNTADMIN** genom att klicka på **profil** högst upp till höger på sidan.

    > [!NOTE]
    > Det här är inte samma som den kontext du har valt i det övre högra hörnet under ditt användarnamn
    
    ![Snowflake-administratören](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Öppna det **nedladdade Base64-certifikatet** i anteckningar. Kopiera värdet mellan ”---BEGIN CERTIFICATE---” och ”---END CERTIFICATE---” och klistra in det i de dubbla citattecknen bredvid **certifikat** nedan. I **ssoUrl** klistrar du in värdet för **Inloggnings-URL** som du har kopierat från Azure-portalen. Välj **Alla frågor** och klicka på **Kör**.

   ![Snowflake-sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Snowflake.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Snowflake**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Snowflake**.

    ![Snowflake-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-snowflake-test-user"></a>Skapa Snowflake-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på Snowflake måste de etableras i Snowflake. I Snowflake är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Snowflake som en säkerhetsadministratör.

2. **Växla roll** till **ACCOUNTADMIN** genom att klicka på **profil** högst upp till höger på sidan.  

    ![Snowflake-administratören](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Skapa användaren genom att köra SQL-frågan nedan, och se till att Azure AD-användarnamnet anges som ”Inloggningsnamn” i kalkylbladet enligt nedan.

    ![Snowflake-adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Snowflake-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Snowflake som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

