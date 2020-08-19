---
title: 'Självstudie: Azure Active Directory integrering med Thoughtworks Mingle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Thoughtworks Mingle.
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
ms.openlocfilehash: caae4251c8a7c7194b2010f1cba750ef34579934
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546516"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Självstudie: Azure Active Directory integrering med Thoughtworks Mingle

I den här självstudien får du lära dig att integrera Thoughtworks-Mingle med Azure Active Directory (Azure AD).
Genom att integrera Thoughtworks-Mingle med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Thoughtworks-Mingle.
* Du kan göra det möjligt för användarna att logga in automatiskt till Thoughtworks Mingle (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Thoughtworks Mingle behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Thoughtworks Mingle, enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Thoughtworks Mingle stöder **SP** -INITIERAd SSO

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Lägga till Thoughtworks Mingle från galleriet

Om du vill konfigurera integreringen av Thoughtworks-Mingle i Azure AD måste du lägga till Thoughtworks Mingle från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Thoughtworks-Mingle från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Thoughtworks Mingle**, väljer **Thoughtworks Mingle** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Thoughtworks Mingle i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Thoughtworks Mingle baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Thoughtworks Mingle upprättas.

Om du vill konfigurera och testa enkel inloggning med Thoughtworks Mingle i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Thoughtworks Mingle Single Sign-on](#configure-thoughtworks-mingle-single-sign-on)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Thoughtworks Mingle test User](#create-thoughtworks-mingle-test-user)** – om du vill ha en motsvarighet till Britta Simon i Thoughtworks Mingle som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Thoughtworks-Mingle i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Thoughtworks Mingle** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Thoughtworks Mingle-domän och URL-adresser](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Thoughtworks Mingle client support team](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Thoughtworks Mingle** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Konfigurera enkel inloggning för Thoughtworks Mingle

1. Logga in på din **Thoughtworks Mingle** -företags webbplats som administratör.

2. Klicka på fliken **admin** och klicka sedan på **SSO config**.
   
    ![Fliken admin](./media/thoughtworks-mingle-tutorial/ic785157.png "SSO-konfiguration")

3. Utför följande steg i avsnittet **SSO config** :
   
    ![SSO-konfiguration](./media/thoughtworks-mingle-tutorial/ic785158.png "SSO-konfiguration")
    
    a. Ladda upp metadatafilen genom att klicka på **Välj fil**. 

    b. Klicka på **Spara ändringar**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Thoughtworks Mingle.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Thoughtworks Mingle**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Thoughtworks Mingle**.

    ![Thoughtworks Mingle-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-thoughtworks-mingle-test-user"></a>Skapa Thoughtworks Mingle-test användare

För att Azure AD-användare ska kunna logga in måste de vara etablerade i Thoughtworks Mingle-programmet med sina Azure Active Directory användar namn. När det gäller Thoughtworks-Mingle är etableringen en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din Thoughtworks Mingle-företags webbplats som administratör.

2. Klicka på **profil**.
   
    ![Ditt första projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Ditt första projekt")

3. Klicka på fliken **admin** och sedan på **användare**.
   
    ![Användare](./media/thoughtworks-mingle-tutorial/ic785161.png "Användare")

4. Klicka på **Ny användare**.
   
    ![Ny användare](./media/thoughtworks-mingle-tutorial/ic785162.png "Ny användare")

5. I dialogrutan **Ny användare** utför du följande steg:
   
    ![Dialog rutan ny användare](./media/thoughtworks-mingle-tutorial/ic785163.png "Ny användare")  
 
    a. Skriv **inloggnings namnet**och **visnings namnet**, **Välj lösen ord**, **Bekräfta lösen ordet** för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna. 

    b. Välj **fullständig användare**som **användar typ**.

    c. Klicka på **skapa den här profilen**.

>[!NOTE]
>Du kan använda andra Thoughtworks-Mingle för att skapa användar konton eller API: er från Thoughtworks Mingle för att etablera Azure AD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Thoughtworks Mingle på åtkomst panelen, bör du loggas in automatiskt på Thoughtworks-Mingle som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

