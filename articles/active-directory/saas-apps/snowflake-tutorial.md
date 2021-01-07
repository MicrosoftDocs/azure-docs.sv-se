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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f516f51c2e059526b4e678f2779ef0ad059c74e7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968559"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Självstudie: Azure Active Directory integrering med snö flingor

I den här självstudien får du lära dig hur du integrerar snö flingor med Azure Active Directory (Azure AD). När du integrerar snö flingor med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till snö flingor.
* Gör det möjligt för användarna att logga in automatiskt till snö flingor med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Snowflake behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Snowflake-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kommer du att konfigurera och testa enkel inloggning i Azure AD i en test miljö.

- Snowflake har stöd för **SP- och IDP**-initierad enkel inloggning
- Snö har stöd för [Automatisk användar etablering och avetablering](snowflake-provisioning-tutorial.md) (rekommenderas)

## <a name="adding-snowflake-from-the-gallery"></a>Lägga till Snowflake från galleriet

För att konfigurera integrering av Snowflake i Azure AD behöver du lägga till Snowflake från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv **snö** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **snö** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Konfigurera och testa Azure AD SSO för snö flingor

Konfigurera och testa Azure AD SSO med snö flingor med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i snö.

För att konfigurera och testa Azure AD SSO med snö flingor slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera snö enkel](#configure-snowflake-sso)** inloggning – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa snö-test-användare](#create-snowflake-test-user)** – för att få en motsvarighet till B. Simon i snö som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **snö** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg, om du vill konfigurera programmet i **IDP** initierat läge:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i SP-initierat läge:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. I text rutan **utloggnings-URL** skriver du en URL med följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Hämta värdena genom att kontakta [supportteamet för Snowflake-klienten](https://support.snowflake.net/s/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Snowflake** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till snö flingor.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **snö flingor**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-snowflake-sso"></a>Konfigurera snö enkel inloggning

1. Logga in på Snowflake som säkerhetsadministratör i ett annat webbläsarfönster.

1. **Växla roll** till **ACCOUNTADMIN** genom att klicka på **profil** högst upp till höger på sidan.

    > [!NOTE]
    > Detta skiljer sig från den kontext som du har valt i det övre högra hörnet under ditt användar namn.
    
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

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till en snö-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till snö inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den snö snö som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen snö flingor i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på den snö som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat snö kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)