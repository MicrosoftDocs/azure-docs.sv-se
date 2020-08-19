---
title: 'Självstudie: Azure Active Directory integration med informations hantering för informations hantering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och informations hantering i informations hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 8724bb332e48bb9e458550b312fb9c1d41da2477
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550579"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Självstudie: Azure Active Directory integration med informations hantering i detalj handeln – informations hantering

I den här självstudien får du lära dig att integrera informations hantering med information i detalj hantering med Azure Active Directory (Azure AD).
Att integrera informations hantering i informations hantering med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har till gång till information i detalj handeln – informations hantering.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade för informations hantering – informations hantering (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med informations hantering i informations hantering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Detalj handel – informations hantering enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Informations hantering – informations hantering stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Lägga till information i detalj handeln – informations hantering från galleriet

Om du vill konfigurera integrering av informations hantering i informations hantering i Azure AD måste du lägga till informations hantering i informations hantering från galleriet till din lista över hanterade SaaS-appar.

**Gör så här om du vill lägga till information i detalj handels hantering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **detalj handel – informations hantering**, Välj **informations hantering** från resultat panelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Detalj handel – informations hantering i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med informations åter försäljning – informations hantering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i informations-och detalj hanterings-och informations hantering.

Om du vill konfigurera och testa enkel inloggning med informations hantering i Azure AD och informations hantering måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera information för detalj handel – informations hantering – enkel inloggning](#configure-infor-retail--information-management-single-sign-on)** – så här konfigurerar du inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa informations hantering – test användare av informations hantering](#create-infor-retail--information-management-test-user)** – om du vill ha en motsvarighet till Britta Simon i informations åter försäljning – informations hantering som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med informations hantering i Azure AD med informations hantering:

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan **informations hantering – information** om program integration och väljer **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Informations hantering – information om domän och URL: er för information om enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    ```http
    https://<company name>.mingle.infor.com
    http://<company name>.mingledev.infor.com
    ```

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<company name>.mingle.infor.com/sp/ACS.saml2`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Informations hantering – information om domän och URL: er för information om enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<company name>.mingle.infor.com/<company code>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [informations hantering klient support teamet](mailto:innovate@infor.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera informations hantering – informations hantering** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-infor-retail--information-management-single-sign-on"></a>Konfigurera informations hantering – enkel inloggning för informations hantering

Om du vill konfigurera enkel inloggning på **informations hanterings sidan – informations hanterings** sidan måste du skicka den hämtade **metadata-XML** och lämpliga kopierade url: er från Azure Portal till [informations hanterings teamet för informations hantering](mailto:innovate@infor.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** `brittasimon@yourcompanydomain.extension` . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till informations hantering för informations hantering.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer **informations hantering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **detalj handel – informations hantering**.

    ![Länken informations hantering – informations hantering i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-infor-retail--information-management-test-user"></a>Skapa informations hantering – test användare för informations hantering

I det här avsnittet skapar du en användare med namnet Britta Simon i infor-detalj handel – informations hantering. Arbeta med [informations hanterings teamet](mailto:innovate@infor.com) för informations hantering om du vill lägga till användarna i informations hanterings plattformen för information i detalj handeln. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på informations hanterings panelen – informations hantering i åtkomst panelen, bör du loggas in automatiskt till detalj handeln – informations hantering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
