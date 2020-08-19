---
title: 'Självstudie: Azure Active Directory integration med moln Hanteringsportal för Microsoft Azure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Management Portal for Microsoft Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: d45a3ddda44922c70080a09447866a9a4c6997a7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554461"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Självstudie: Azure Active Directory integrering med moln Hanteringsportal för Microsoft Azure

I den här självstudien lär du dig att integrera Cloud Management Portal for Microsoft Azure med Azure Active Directory (AD Azure).
Integreringen av Cloud Management Portal for Microsoft Azure med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Cloud Management Portal for Microsoft Azure.
* Du kan göra så att dina användare automatiskt loggas in på Cloud Management Portal for Microsoft Azure (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Cloud Management Portal for Microsoft Azure behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cloud Management Portal for Microsoft Azure-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cloud Management Portal for Microsoft Azure har stöd för **SP**-initierad enkel inloggning

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Lägga till Cloud Management Portal for Microsoft Azure från galleriet

För att konfigurera integreringen av Cloud Management Portal for Microsoft Azure i Azure AD måste du lägga till Cloud Management Portal for Microsoft Azure från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cloud Management Portal for Microsoft Azure från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cloud Management Portal for Microsoft Azure**, väljer **Cloud Management Portal for Microsoft Azure** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Cloud Management Portal for Microsoft Azure i programlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Cloud Management Portal for Microsoft Azure baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cloud Management Portal for Microsoft Azure upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Cloud Management Portal for Microsoft Azure behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cloud Management Portal for Microsoft Azure](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Cloud Management Portal for Microsoft Azure-testanvändare](#create-cloud-management-portal-for-microsoft-azure-test-user)** – för att skapa en motsvarighet till Britta Simon i Cloud Management Portal for Microsoft Azure som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Cloud Management Portal for Microsoft Azure:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för **Cloud Management Portal for Microsoft Azure**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Cloud Management Portal for Microsoft Azure-domän och -URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    ```http
    https://portal.newsignature.com/<instancename>
    https://portal.igcm.com/<instancename>
    ```

    b. I rutan **Identifierare** skriver du en URL med följande mönster:

    ```http
    https://<subdomain>.igcm.com
    https://<subdomain>.newsignature.com
    ```

    c. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ```http
    https://<subdomain>.igcm.com/<instancename>
    https://<subdomain>.newsignature.com
    https://<subdomain>.newsignature.com/<instancename>
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [kundsupporten för Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) to och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Cloud Management Portal for Microsoft Azure** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Konfigurera enkel inloggning för Cloud Management Portal for Microsoft Azure

För att konfigurera enkel inloggning på **Cloud Management Portal for Microsoft Azure**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Cloud Management Portal for Microsoft Azure.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cloud Management Portal for Microsoft Azure**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Cloud Management Portal for Microsoft Azure**.

    ![Länken för Cloud Management Portal for Microsoft Azure i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Skapa testanvändare för Cloud Management Portal for Microsoft Azure

I det här avsnittet skapar du en användare med namnet Britta Simon i Cloud Management Portal for Microsoft Azure. Kontakta [supportteamet för Cloud Management Portal for Microsoft Azure](mailto:jczernuszka@newsignature.com) och lägg till användarna i Cloud Management Portal for Microsoft Azure-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cloud Management Portal for Microsoft Azure-panelen i åtkomstpanelen bör du automatiskt loggas in på Cloud Management Portal for Microsoft Azure som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

