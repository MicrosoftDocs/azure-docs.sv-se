---
title: 'Självstudie: Azure Active Directory integrering med Tutorialspan eTMF | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory ochPan eTMF.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: f0744b98b0264a79072e60ddb7627a4b527bdf1e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600649"
---
# <a name="tutorial-azure-active-directory-integration-with-wingspan-etmf"></a>Självstudie: Azure Active Directory integrering med Tutorialspan eTMF

I den här självstudien lär du dig att integrera Tutorialpan eTMF med Azure Active Directory (Azure AD).
Integreringen avPan eTMF med Azure AD ger dig följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst tillPan eTMF.
* Du kan göra så att dina användare automatiskt loggas in påPan eTMF (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Japan eTMF behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du skaffa ett [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Prenumeration med Enkel inloggning aktiverat förPan eTMF

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Pan eTMF har stöd för **SP-initierad** enkel inloggning

## <a name="adding-wingspan-etmf-from-the-gallery"></a>Lägga till Ellerpan eTMF från galleriet

För att konfigurera integreringen av Ettpan eTMF i Azure AD behöver du lägga till Ettpan eTMF från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga tillPan eTMF från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Alternativetpan eTMF,** väljer **Alternativetpan eTMF** i resultatpanelen och klickar på knappen Lägg till för att lägga till programmet. 

     ![Pan eTMF i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD medAvsnittpan eTMF baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren iPan eTMF upprättas.

För att konfigurera och testa enkel inloggning i Azure AD medPan eTMF behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning förPanpan eTMF](#configure-wingspan-etmf-single-sign-on)** – för att konfigurera Sign-On på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Enplypan eTMF-testanvändare](#create-wingspan-etmf-test-user)** – för att ha en motsvarighet för Britta Simon i Ettpan eTMF som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD medPan eTMF:

1. På sidan [Azure Portal](https://portal.azure.com/)väljer du Enkel inloggning på sidan för Programintegrering med **eTMF** för Alternativet **Förser dig med enkel inloggning.**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning medPan eTMF-domän och URL:er](common/sp-identifier-reply.png)

    a. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://<customer name>.<instance name>.mywingspan.com/saml`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `http://saml.<instance name>.wingspan.com/shibboleth`

    c. I **textrutan Svars-URL** skriver du en URL med följande mönster: `https://<customer name>.<instance name>.mywingspan.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta supportteamet för Grupppan eTMF-klienten och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I **avsnittet KonfigureraPan eTMF** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-wingspan-etmf-single-sign-on"></a>KonfigureraPan eTMF – enskild Sign-On

För att konfigurera enkel inloggning på **Sidan Medlpan eTMF** behöver du skicka den nedladdade XML:en med federationsmetadata och lämpliga kopierade **URL:er** från Azure Portal till Supportteamet för ETMF för Sms. De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny** användare överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** skriver du brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst tillAvsnittpan eTMF.

1. I Azure Portal väljer du **Företagsprogram,** **Alla program** och sedan Alternativet **eTMF.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Alternativetpan eTMF**.

    ![Länken förPan eTMF i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i  SAML-försäkran väljer du i dialogrutan Välj roll  lämplig roll för användaren i listan och klickar sedan på knappen Välj längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wingspan-etmf-test-user"></a>SkapaPan eTMF-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon iPan eTMF. Ta hjälp avsupportteamet förPan eTMF för att lägga till användarna på eTMF-plattformen iPan. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Panelen Försett med eTMF i Åtkomstpanelen bör du automatiskt loggas in på Det 15 eTMF som du har ställt in enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)
