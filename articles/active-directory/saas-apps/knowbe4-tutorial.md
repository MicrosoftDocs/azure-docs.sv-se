---
title: 'Självstudie: Azure Active Directory integrering med KnowBe4 Security medvetenhet Training | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowBe4 Security Awareness Training.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: c6e70268dcc252964a0b046488c399f474660ca5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540120"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Självstudie: Azure Active Directory integrering med KnowBe4 Security medvetenhet Training

I den här självstudien lär du dig att integrera KnowBe4 Security Awareness Training med Azure Active Directory (Azure AD).
Integreringen av KnowBe4 Security Awareness Training med Azure AD medför följande fördelar:

* Du kan styra vilka som har tillgång till KnowBe4 Security Awareness Training från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på KnowBe4 Security Awareness Training (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med KnowBe4 Security Awareness Training behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En KnowBe4 Security Awareness Training-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* KnowBe4 Security Awareness Training har stöd för **SP**-initierad enkel inloggning

* KnowBe4 Security Awareness Training har stöd för **Just-in-time**-användaretablering

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Lägga till KnowBe4 Security Awareness Training från galleriet

För att kunna konfigurera integrering av KnowBe4 Security Awareness Training i Azure AD behöver du lägga till KnowBe4 Security Awareness Training från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till KnowBe4 Security Awareness Training från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **KnowBe4 Security Awareness Training** i sökrutan, välj **KnowBe4 Security Awareness Training** i resultatpanelen och klicka sedan på knappen **Lägg till** för att lägga till programmet.

     ![KnowBe4 Security Awareness Training i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med KnowBe4 Security Awareness Training baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i KnowBe4 Security Awareness Training upprättas.

För att kunna konfigurera och testa enkel inloggning med Azure AD med hjälp av KnowBe4 Security Awareness Training behöver du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för KnowBe4 Security Awareness Training](#configure-knowbe4-security-awareness-training-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa KnowBe4 Security Awareness Training-testanvändare](#create-knowbe4-security-awareness-training-test-user)** – för att få en motsvarighet till Britta Simon i KnowBe4 Security Awareness Training som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med hjälp av KnowBe4 Security Awareness Training:

1. Välj **Enkel inloggning** på sidan för programintegrering med **KnowBe4 Security Awareness Training** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med KnowBe4 Security Awareness Training-domäner och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Värdet för inloggnings-URL:en är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta värdet genom att kontakta [supportteamet för KnowBe4 Security Awareness Training-klienten](mailto:support@KnowBe4.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. I textrutan **Identifierare (entitets-ID)** anger du strängvärdet: `KnowBe4`

    > [!NOTE]
    > Värdet är skiftlägeskänsligt.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera KnowBe4 Security Awareness Training** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Konfigurera KnowBe4 Security Awareness Training för enkel inloggning

För att konfigurera enkel inloggning på **KnowBe4 Security Awareness Training**-sidan behöver du skicka det nedladdade **certifikatet (RAW)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till KnowBe4 Security Awareness Training.

1. På Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och väljer sedan **KnowBe4 Security Awareness Training**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **KnowBe4 Security Awareness Training** i listan över program.

    ![KnowBe4 Security Awareness Training-länken i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Skapa KnowBe4 Security Awareness Training-testanvändare

Syftet med det här avsnittet är att skapa en användare kallad Britta Simon i KnowBe4 Security Awareness Training. KnowBe4 Security Awareness Training stöder just-in-time-etablering, som är aktiverat med standardinställningen.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare skapas under ett försök att komma åt KnowBe4 Security Awareness Training om det inte finns ännu.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du behöva kontakta supportteamet för [KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för KnowBe4 Security Awareness Training i åtkomstpanelen bör du loggas in automatiskt på den KnowBe4 Security Awareness Training som du har ställt in enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
