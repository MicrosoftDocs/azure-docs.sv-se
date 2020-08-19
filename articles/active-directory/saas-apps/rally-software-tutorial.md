---
title: 'Självstudie: Azure Active Directory integration med rally-programvara | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Rally-programvara.
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
ms.openlocfilehash: 542d330138885a997a0e4a335272492116686a65
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548913"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Självstudie: Azure Active Directory integrering med rally-programvara

I den här självstudien får du lära dig att integrera Rally-program med Azure Active Directory (Azure AD).
Att integrera Rally-program med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till Rally-programvaran.
* Du kan göra det möjligt för användarna att logga in automatiskt till Rally program vara (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med rally-programvara behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Rally program vara med enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Rally-programvara stöder **SP** -INITIERAd SSO

## <a name="adding-rally-software-from-the-gallery"></a>Lägga till Rally-programvara från galleriet

Om du vill konfigurera integreringen av Rally-programvaran i Azure AD måste du lägga till Rally-program från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Rally-program från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Rally Software**, väljer **Rally program** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Rally program vara i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med rally-programvara baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Rally-programvaran upprättas.

Om du vill konfigurera och testa enkel inloggning med rally-programvaran i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Rally Software Single Sign-on](#configure-rally-software-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Rally Software test User](#create-rally-software-test-user)** – om du vill ha en motsvarighet till Britta Simon i Rally-programvara som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med rally-programvara i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **Rally Software** integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för rally program domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Rally Software client support team](https://help.rallydev.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Rally-programvara** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-rally-software-single-sign-on"></a>Konfigurera Rally program vara enkel inloggning

1. Logga in på din Rally-klient för **program vara** .

2. I verktygsfältet högst upp klickar du på **konfiguration**och väljer sedan **prenumeration**.
   
    ![Prenumeration](./media/rally-software-tutorial/ic769531.png "Prenumeration")

3. Klicka på knappen **åtgärd** . Välj **Redigera prenumeration** längst upp till höger i verktygsfältet.

4. På dialog sidan **prenumeration** utför du följande steg och klickar sedan på **Spara & Stäng**:
   
    ![Autentisering](./media/rally-software-tutorial/ic769542.png "Autentisering")
   
    a. Välj **rally eller SSO-autentisering** från listruta för autentisering.

    b. I text rutan **identitets leverantörens URL** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal. 

    c. I text rutan för **SSO-utloggning** klistrar du in värdet för **utloggnings-URL**, som du har kopierat från Azure Portal.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Rally-programvaran.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Rally-programvara**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Rally-programvara**.

    ![Rally program varu länk i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-rally-software-test-user"></a>Skapa Rally program vara test användare

För att Azure AD-användare ska kunna logga in måste de vara etablerade i Rally-programvaran med sina Azure Active Directory användar namn.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din Rally-klient för program vara.

2. Gå till **installations \> användare**och klicka sedan på **+ Lägg till ny**.
   
    ![Användare](./media/rally-software-tutorial/ic781039.png "Användare")

3. Skriv namnet i text rutan ny användare och klicka sedan på **Lägg till med information**.

4. I avsnittet **Skapa användare** utför du följande steg:
   
    ![Skapa användare](./media/rally-software-tutorial/ic781040.png "Skapa användare")

    a. I text rutan **användar namn** skriver du namnet på användaren som **Brittsimon**.
   
    b. I text rutan e-postadress anger du e- **postadressen** till användaren brittasimon@contoso.com .

    c. I textrutan **Förnamn** anger du förnamnet på användaren som **Britta**.

    d. I text rutan **efter namn** anger du det senaste namnet på användaren som **Simon**.

    e. Klicka på **Save & Close** (Spara och stäng).

   >[!NOTE]
   >Du kan använda andra Rally-verktyg för användar konton eller API: er som tillhandahålls av Rally-programvaran för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på program panelen rally på åtkomst panelen, bör du loggas in automatiskt till Rally-programvaran som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

