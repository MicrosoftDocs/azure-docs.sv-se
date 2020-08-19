---
title: 'Självstudie: Azure Active Directory integration med Splunk Enterprise och Splunk Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splunk Enterprise och Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 22406fc3ed17e486859fc7e2e2f026619c7c6b55
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527620"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Självstudie: Azure Active Directory integrering med Splunk Enterprise och Splunk Cloud

I den här självstudien lär du dig hur du integrerar Splunk Enterprise och Splunk Cloud med Azure Active Directory (Azure AD).
Genom att integrera Splunk Enterprise och Splunk Cloud med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Splunk Enterprise och Splunk Cloud.
* Du kan göra så att dina användare automatiskt loggas in på Splunk Enterprise och Splunk Cloud (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Splunk Enterprise och Splunk Cloud behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel inloggning aktiverat för Splunk Enterprise- och Splunk Cloud-prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Splunk Enterprise och Splunk Cloud stöder **SP**-initierad SSO

## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Lägga till Splunk Enterprise och Splunk Cloud från galleriet

Om du vill konfigurera integreringen av Splunk Enterprise och Splunk Cloud till Azure AD behöver du lägga till Splunk Enterprise och Splunk Cloud från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Splunk Enterprise och Splunk Cloud från galleriet utför du följande steg:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Splunk Enterprise och Splunk Cloud**, väljer **Splunk Enterprise och Splunk Cloud** i resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Splunk Enterprise och Splunk Cloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Splunk Enterprise och Splunk Cloud baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Splunk Enterprise och Splunk Cloud upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Splunk Enterprise och Splunk Cloud:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Splunk Enterprise och Splunk Cloud](#configure-splunk-enterprise-and-splunk-cloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Splunk Enterprise- och Splunk Cloud-testanvändare](#create-splunk-enterprise-and-splunk-cloud-test-user)** – för att ha en motsvarighet till Britta Simon i Splunk Enterprise och Splunk Cloud som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Splunk Enterprise och Splunk Cloud utför du följande steg:

1. I [Azure-portalen](https://portal.azure.com/)på programintegreringssidan för **Splunk Enterprise och Splunk Cloud** markerar du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för URL:er för Splunk Enterprise och Splunk Cloud](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<splunkserverUrl>/en-US/app/launcher/home`

    b. I rutan **identifierare** anger du en URL med följande mönster: `<splunkserverUrl>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [supportteamet för Splunk Enterprise och Splunk Cloud ](https://www.splunk.com/en_us/about-splunk/contact-us.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-splunk-enterprise-and-splunk-cloud-single-sign-on"></a>Konfigurera enkel inloggning för Splunk Enterprise och Splunk Cloud

Om du vill konfigurera enkel inloggning på **Splunk Enterprise och Splunk Cloud**-sidan måste du skicka den hämtade **Federation Metadata-XML:en** och lämpliga kopierade URL:er från Azure-portalen till [Splunk Enterprise- och Splunk Cloud-supportteamet](https://www.splunk.com/en_us/about-splunk/contact-us.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Splunk Enterprise och Splunk Cloud.

1. I Azure-portalen väljer du **Företagsprogram** följt av **Alla program** och sedan **Splunk Enterprise och Splunk Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program skriver du och väljer **Splunk Enterprise och Splunk Cloud**.

    ![Länken Splunk Enterprise och Splunk Cloud i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Skapa testanvändare för Splunk Enterprise och Splunk Cloud

I det här avsnittet skapar du en användare som heter Britta Simon i Splunk Enterprise och Splunk Cloud. Arbeta med [Splunk Enterprise- och Splunk Cloud-supportteamet](https://www.splunk.com/en_us/about-splunk/contact-us.html) för att lägga till användare i Splunk Enterprise och Splunk Cloud-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Splunk Enterprise och Splunk Cloud i åtkomstpanelen bör du loggas in automatiskt till Splunk Enterprise och Splunk Cloud som du ställer in enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

