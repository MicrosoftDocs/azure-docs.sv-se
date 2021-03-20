---
title: 'Självstudie: Azure Active Directory integrering med Cloud Service-PICCO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Service PICCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.openlocfilehash: 1df8c6fa0877c20518be072674447d28d7f1ca0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97670180"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-service-picco"></a>Självstudie: Azure Active Directory integrering med Cloud Service PICCO

I den här självstudien lär du dig hur du integrerar Cloud Service PICCO med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Cloud Service PICCO med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Cloud Service PICCO.
* Du kan göra så att dina användare automatiskt loggas in på Cloud Service PICCO (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna konfigurera Azure AD-integrering med Cloud Service PICCO behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cloud Service PICCO-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cloud Service PICCO har stöd för **SP**-initierad enkel inloggning
* Cloud Service PICCO har stöd för **just-in-time**-användaretablering

## <a name="adding-cloud-service-picco-from-the-gallery"></a>Lägga till Cloud Service PICCO från galleriet

För att kunna konfigurera integreringen av Cloud Service PICCO i Azure AD måste du lägga till Cloud Service PICCO från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Cloud Service PICCO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Cloud Service PICCO** i sökrutan, välj **Cloud Service PICCO** på resultatpanelen och klicka på knappen **Lägg till** för att lägga till programmet.

    ![Cloud Service PICCO i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Cloud Service PICCO baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cloud Service PICCO upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Cloud Service PICCO måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cloud Service PICCO](#configure-cloud-service-picco-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa Cloud Service PICCO-testanvändare](#create-cloud-service-picco-test-user)** – så att det finns en motsvarighet till Britta Simon i Cloud Service PICCO som är länkad till Azure AD-representationen av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Cloud Service PICCO:

1. Välj **Enkel inloggning** på sidan för **Cloud Service PICCO**-programintegrering i [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Cloud Service PICCO – enkel inloggning-information för domän och -URL:er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<SUB DOMAIN>.cloudservicepicco.com/app`

    b. I rutan **identifierare** anger du en URL med följande mönster: `<SUB DOMAIN>.cloudservicepicco.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUB DOMAIN>.cloudservicepicco.com/app`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Cloud Service PICCO-kundsupporten](mailto:picco.support@est.fujitsu.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-cloud-service-picco-single-sign-on"></a>Konfigurera enkel inloggning i Cloud Service PICCO

Om du vill konfigurera enkel inloggning på **Cloud Service PICCO**-sidan måste du skicka **URL för appfederationsmetadata** till [Cloud Service PICCO-supporten](mailto:picco.support@est.fujitsu.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till Cloud Service PICCO.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cloud Service PICCO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cloud Service PICCO** i programlistan.

    ![Cloud Service PICCO-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cloud-service-picco-test-user"></a>Skapa Cloud Service PICCO-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i Cloud Service PICCO. Cloud Service PICCO stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Cloud Service PICCO skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cloud Service PICCO-panelen i åtkomstpanelen bör du automatiskt loggas in i Cloud Service PICCO som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)