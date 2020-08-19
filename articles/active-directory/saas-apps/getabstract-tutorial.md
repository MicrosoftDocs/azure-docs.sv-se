---
title: 'Självstudie: Azure Active Directory integrering med Getabstract | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Getabstract.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: e95dadd81cd021d0d54d77e70251495b1bd5b6a5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550869"
---
# <a name="tutorial-azure-active-directory-integration-with-getabstract"></a>Självstudie: Azure Active Directory integrering med Getabstract

I den här självstudien lär du dig att integrera Getabstract med Azure Active Directory (AD Azure).
Integreringen av Getabstract med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Getabstract.
* Du kan göra så att dina användare automatiskt loggas in på Getabstract (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Getabstract behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Getabstract-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Getabstract har stöd för **SP**- och **IDP**-initierad enkel inloggning

* Getabstract har stöd för **just-in-time**-användaretablering


## <a name="adding-getabstract-from-the-gallery"></a>Lägga till Getabstract från galleriet

För att konfigurera integreringen av Getabstract i Azure AD behöver du lägga till Getabstract från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Getabstract från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Getabstract**, väljer **Getabstract** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Getabstract i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Getabstract baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Getabstract upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Getabstract slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Getabstract](#configure-getabstract-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Getabstract-testanvändare](#create-getabstract-test-user)** – för att ha en motsvarighet för Britta Simon i Getabstract som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Getabstract:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Getabstract** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med Getabstract-domän och URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL: 

    För Stage/pre_production (mellanlagring/förproduktion): `https://int.getabstract.com`

    För produktion: `https://www.getabstract.com`

    b. I textrutan **Svars-URL** anger du en URL:
    
    För Stage/pre_production (mellanlagring/förproduktion): `https://int.getabstract.com/ACS.do`
    
    För produktion: `https://www.getabstract.com/ACS.do`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Getabstract-domän och URL:er](common/metadata-upload-additional-signon.png)
    
    I textrutan **Inloggnings-URL** anger du en URL med följande mönster:

    För Stage/pre_production (mellanlagring/förproduktion): `https://int.getabstract.com/portal/<org_username>`
    
    För produktion: `https://www.getabstract.com/portal/<org_username>`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för Getabstract](https://www.getabstract.com/en/contact) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Ladda ned** för att ladda ned **XML-federationsmetadata** från de angivna alternativen enligt dina behov och sparar dem på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Getabstract** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-getabstract-single-sign-on"></a>Konfigurera enkel inloggning för Getabstract

För att konfigurera enkel inloggning på **Getabstract**-sidan behöver du skicka den nedladdade **XML:en med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Getabstract](https://www.getabstract.com/en/contact). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Getabstract.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Getabstract**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Getabstract**.

    ![Länken för Getabstract i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-getabstract-test-user"></a>Skapa Getabstract-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Getabstract. Getabstract har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Getabstract skapas en ny efter autentisering.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [supportteamet för Getabstract](https://www.getabstract.com/en/contact)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Getabstract-panelen i åtkomstpanelen bör du automatiskt loggas in på Getabstract som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

