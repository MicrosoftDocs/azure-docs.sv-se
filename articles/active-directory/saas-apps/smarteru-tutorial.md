---
title: 'Självstudie: Azure Active Directory integrering med SmarterU | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmarterU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 6b4004697828e08215cc912dcd21491b43fc9d64
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527782"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Självstudie: Azure Active Directory integrering med SmarterU

> [!NOTE]
> Processen för att integrera SmarterU med Azure Active Directory dokumenteras och underhålls också i [SmarterU-hjälpsystemet](https://help.smarteru.com/ID2053086).

I den här självstudien får du lära dig hur du integrerar SmarterU med Azure Active Directory (Azure AD).
Genom att integrera SmarterU med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SmarterU.
* Du kan göra det möjligt för användarna att logga in automatiskt till SmarterU (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SmarterU behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SmarterU-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SmarterU stöder **IDP** INITIERAd SSO

## <a name="adding-smarteru-from-the-gallery"></a>Lägga till SmarterU från galleriet

Om du vill konfigurera integreringen av SmarterU i Azure AD måste du lägga till SmarterU från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SmarterU från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **nytt program** överst i dialog rutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SmarterU**, väljer **SmarterU** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![SmarterU i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SmarterU baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SmarterU upprättas.

Om du vill konfigurera och testa enkel inloggning med SmarterU i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SmarterU-enkel inloggning](#configure-smarteru-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SmarterU test User](#create-smarteru-test-user)** – om du vill ha en motsvarighet till Britta Simon i SmarterU som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SmarterU i Azure AD:

1. Välj **enkel inloggning**på sidan **SmarterU** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för SmarterU-domän och URL: er](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du URL:en: `https://www.smarteru.com/`

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera SmarterU** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-smarteru-single-sign-on"></a>Konfigurera SmarterU enkel inloggning

1. Logga in på din SmarterU-företags webbplats som administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp klickar du på **konto inställningar**.

    ![Konto inställningar](./media/smarteru-tutorial/accountsettings.png)

1. På sidan för kontokonfiguration utför du följande steg:

    ![Extern auktorisering](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Välj **Aktivera extern auktorisering**.
  
    b. I avsnittet **huvud inloggnings kontroll** väljer du fliken **SmarterU** .
  
    c. I avsnittet **användarens standard inloggning** väljer du fliken **SmarterU** .
  
    d. Välj **Aktivera SAML**.
  
    e. Kopiera innehållet i den hämtade metadatafilen och klistra sedan in den i text rutan **IDP metadata** .

    f. Välj ett **Identifier-attribut/-anspråk**.
  
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
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SmarterU.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SmarterU**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SmarterU**.

    ![SmarterU-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-smarteru-test-user"></a>Skapa SmarterU test användare

Om du vill att Azure AD-användare ska kunna logga in på SmarterU måste de tillhandahållas i SmarterU. När det gäller SmarterU är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **SmarterU** -klient.

1. Gå till **användare**.

1. Utför följande steg i avsnittet användare:

    ![Ny användare](./media/smarteru-tutorial/adduser.png)  

    a. Klicka på **+ användare**.

    b. Ange de relaterade attributvärdena för Azure AD-användarkontot i följande text rutor: **primär e-post**, **anställnings-ID**, **lösen ord**, **Verifiera lösen ord**, **förnamn**, efter namn **.**

    c. Klicka på **aktiv**.

    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för SmarterU av användar konton eller API: er som tillhandahålls av SmarterU för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SmarterU på åtkomst panelen, bör du loggas in automatiskt på den SmarterU som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
