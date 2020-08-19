---
title: 'Självstudie: Azure Active Directory integration med kopplings funktion | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: a116dff0eba7e5469ab0c352c30bca506ee18238
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549270"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudie: Azure Active Directory integration med koppling

I den här självstudien lär du dig att integrera Coupa med Azure Active Directory (AD Azure).
Integreringen av Coupa med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Coupa.
* Du kan göra så att dina användare automatiskt loggas in på Coupa (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Coupa behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Coupa-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Coupa har stöd för **SP**-initierad enkel inloggning

## <a name="adding-coupa-from-the-gallery"></a>Lägga till Coupa från galleriet

För att konfigurera integreringen av Coupa i Azure AD måste du lägga till Coupa från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Coupa från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Coupa**, väljer **Coupa** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Coupa i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Coupa baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Coupa upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Coupa behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Coupa](#configure-coupa-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Coupa-testanvändare](#create-coupa-test-user)** – för att skapa en motsvarighet till Britta Simon i Coupa som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Coupa:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **Coupa**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Coupa-domän och -URL:er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Coupa](https://success.coupa.com/Support/Contact_Us?) och be om det här värdet.

    b. I textrutan **Identifierare** skriver du in en URL: 

    | Miljö  | URL |
    |:-------------|----|
    | Begränsat läge | `sso-stg1.coupahost.com`|
    | Produktion | `sso-prd1.coupahost.com`|
    | | |

    c. Skriv en URL i textrutan **Svars-URL**: 

    | Miljö | URL |
    |------------- |----|
    | Begränsat läge | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produktion | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Coupa** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-coupa-single-sign-on"></a>Konfigurera enkel inloggning för Coupa

1. Logga in på din Coupa-företagsplats som administratör.

2. Gå till **Setup \> Security Control** (Konfiguration > Säkerhetskontroll).

    ![Säkerhets kontroller](./media/coupa-tutorial/ic791900.png "Säkerhetskontroller")

3. I avsnittet **Log in using Coupa credentials** (Logga in med autentiseringsuppgifter för Coupa) utför du följande steg:

    ![Kopplings-SP-metadata](./media/coupa-tutorial/ic791901.png "Kopplings-SP-metadata")

    a. Välj **Log in using SAML** (Logga in med hjälp av SAML).

    b. Klicka på **Bläddra** för att ladda upp de metadata som du laddade ned från Azure-portalen.

    c. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Coupa.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Coupa**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Coupa**.

    ![Länken för Coupa i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-coupa-test-user"></a>Skapa Coupa-testanvändare

För att Azure AD-användare ska kunna logga in i Coupa måste de etableras till Coupa.  

* När det gäller Coupa är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Coupa**-företagsplats som administratör.

2. På menyn längst upp klickar du på **Setup** (Konfiguration) och sedan på **Användare**.

    ![Användare](./media/coupa-tutorial/ic791908.png "Användare")

3. Klicka på **Skapa**.

    ![Skapa användare](./media/coupa-tutorial/ic791909.png "Skapa användare")

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Användar information](./media/coupa-tutorial/ic791910.png "Användar information")

    a. Skriv attributen **Inloggning**, **Förnamn**, **Efternamn**, **ID för enkel inloggning** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Skapa**.

    >[!NOTE]
    >Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.
    >

>[!NOTE]
>Du kan använda andra verktyg för skapande av användar konton eller API: er som tillhandahålls av kopplings verktyg för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Coupa-panelen i åtkomstpanelen bör du automatiskt loggas in på Coupa som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

