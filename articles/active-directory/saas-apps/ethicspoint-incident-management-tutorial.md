---
title: 'Självstudie: Azure Active Directory integration med EthicsPoint incident hantering (EPIM) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EthicsPoint Incident Management (EPIM).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 8ea85db0749b2dc95c073072e52a49c795585b55
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555442"
---
# <a name="tutorial-azure-active-directory-integration-with-ethicspoint-incident-management-epim"></a>Självstudie: Azure Active Directory integration med EthicsPoint incident hantering (EPIM)

I den här självstudien lär du dig att integrera EthicsPoint Incident Management (EPIM) med Azure Active Directory (AD Azure).
Integreringen av EthicsPoint Incident Management (EPIM) med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till EthicsPoint Incident Management (EPIM).
* Du kan göra så att dina användare automatiskt loggas in på EthicsPoint Incident Management (EPIM) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att konfigurera Azure AD-integrering med EthicsPoint Incident Management (EPIM):

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* EthicsPoint Incident Management (EPIM)-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* EthicsPoint Incident Management (EPIM) har stöd för **SP**-initierad enkel inloggning

## <a name="adding-ethicspoint-incident-management-epim-from-the-gallery"></a>Lägga till EthicsPoint Incident Management (EPIM) från galleriet

För att konfigurera integrering av EthicsPoint Incident Management (EPIM) i Azure AD behöver du lägga till EthicsPoint Incident Management (EPIM) från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till EthicsPoint Incident Management (EPIM) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **EthicsPoint Incident Management (EPIM)**, väljer **EthicsPoint Incident Management (EPIM)** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![EthicsPoint Incident Management (EPIM) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med EthicsPoint Incident Management (EPIM) baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i EthicsPoint Incident Management (EPIM) upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med EthicsPoint Incident Management (EPIM) behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för EthicsPoint Incident Management (EPIM)](#configure-ethicspoint-incident-management-epim-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa EthicsPoint Incident Management (EPIM)-testanvändare](#create-ethicspoint-incident-management-epim-test-user)** – för att ha en motsvarighet till Britta Simon i EthicsPoint Incident Management (EPIM) som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med EthicsPoint Incident Management (EPIM):

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **EthicsPoint Incident Management (EPIM)** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med EthicsPoint Incident Management (EPIM)-domän och -URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    ```http
    https://<companyname>.navexglobal.com
    https://<companyname>.ethicspointvp.com
    ```

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<companyname>.navexglobal.com/adfs/services/trust`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<servername>.navexglobal.com/adfs/ls/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [kundsupporten för EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera EthicsPoint Incident Management (EPIM)** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ethicspoint-incident-management-epim-single-sign-on"></a>Konfigurera enkel inloggning för EthicsPoint Incident Management (EPIM)

För att konfigurera enkel inloggning på **EthicsPoint Incident Management (EPIM)**-sidan behöver du skicka nedladdad **federationsmetadata-XML** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till EthicsPoint Incident Management (EPIM).

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **EthicsPoint Incident Management (EPIM)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **EthicsPoint Incident Management (EPIM)**.

    ![Länken för EthicsPoint Incident Management (EPIM) i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ethicspoint-incident-management-epim-test-user"></a>Skapa testanvändare för EthicsPoint Incident Management (EPIM)

I det här avsnittet skapar du en användare med namnet Britta Simon i EthicsPoint Incident Management (EPIM). Kontakta  [supportteamet för EthicsPoint Incident Management (EPIM)](https://www.navexglobal.com/company/contact-us) och lägg till användarna på EthicsPoint Incident Management (EPIM)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på EthicsPoint Incident Management (EPIM)-panelen på åtkomstpanelen bör du automatiskt loggas in i EthicsPoint Incident Management (EPIM) som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

