---
title: 'Självstudie: Azure Active Directory integrering med ProCore SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ProCore SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 13f8f1067ce7c9fe55160400d20ec0b20788c17b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515295"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudie: Azure Active Directory integrering med ProCore SSO

I den här självstudien får du lära dig hur du integrerar Core SSO med Azure Active Directory (Azure AD).
Genom att integrera ProCore SSO med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ProCore SSO.
* Du kan göra det möjligt för användarna att logga in automatiskt till ProCore SSO (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ProCore SSO behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* ProCore SSO-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Core SSO stöder **IDP** -INITIERAd SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Lägga till ProCore SSO från galleriet

Om du vill konfigurera integreringen av ProCore SSO i Azure AD måste du lägga till ProCore SSO från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till ProCore SSO från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **ProCore SSO**, väljer **ProCore SSO** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![ProCore SSO i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med ProCore SSO baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Core SSO upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med ProCore SSO måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning](#configure-procore-sso-single-sign-on)** med enkel inloggning för enkel inloggning för att konfigurera enskilda Sign-On inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ProCore SSO test User](#create-procore-sso-test-user)** – om du vill ha en motsvarighet till Britta Simon i ProCore SSO som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med ProCore SSO för Azure AD:

1. På sidan [Azure Portal](https://portal.azure.com/)på sidan för integration av **SSO** -program väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Information om enkel inloggning med ProCore SSO-domän och URL: er](common/preintegrated.png)

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera ProCore SSO** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurera ProCore SSO Single Sign-On

1. Om du vill konfigurera enkel inloggning på **ProCore SSO** -sidan loggar du in på din ProCore-företags plats som administratör.

2. Klicka på **administratör** i list rutan för verktygs lådan för att öppna sidan SSO-inställningar.

    ![Skärm bild som visar ProCore-företags platsen med vald katalog.](./media/procoresso-tutorial/procore_tool_admin.png)

3. Klistra in värdena i rutorna enligt beskrivningen nedan –

    ![Skärm bild som visar dialog rutan Lägg till en person.](./media/procoresso-tutorial/procore_setting_admin.png) 

    a. I text rutan **URL för enkel inloggnings utfärdare** klistrar du in värdet för **Azure AD-identifieraren** som du kopierade från Azure Portal.

    b. I rutan **SAML-inloggningens mål-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    c. Öppna nu **XML-metadata för federationsmetadata** som hämtades ovan från Azure Portal och kopiera certifikatet i taggen med namnet **X509Certificate**. Klistra in det kopierade värdet i rutan **enkel inloggning på x509-certifikat** .

4. Klicka på **Spara ändringar**.

5. Efter de här inställningarna måste du skicka det **domän namn** (t. ex. **contoso.com**) som du loggar in i ProCore till [support teamet](https://support.procore.com/) och de aktiverar federerade SSO för domänen.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ProCore SSO.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **ProCore SSO**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ProCore SSO**.

    ![Hyperlänken för ProCore-SSO i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-procore-sso-test-user"></a>Skapa ProCore SSO-test användare

Följ stegen nedan om du vill skapa en ProCore test-användare på en central enkel inloggnings sida.

1. Logga in på din ProCore-företags plats som administratör.    

2. Öppna sidan företags katalog genom att klicka på **katalog** i list rutan för verktygs lådan.

    ![Skärm bild som visar ProCore-företags platsen med katalogen som valts från verktygs lådan.](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klicka på alternativet **Lägg till en person** för att öppna formuläret och ange följande alternativ –

    ![Skärm bild som visar Boylan-konstruktion där du kan ange användar information.](./media/procoresso-tutorial/Procore_user_add.png)

    a. I text rutan **förnamn** skriver du användarens förnamn som **Britta**.

    b. I text rutan **efter namn** skriver du användarens efter namn som **Simon**.

    c. I text rutan **e-postadress** skriver du användarens e-postadress som BrittaSimon@contoso.com .

    d. Välj **behörighets mal len** som mall för behörighet att **använda senare**.

    e. Klicka på **Skapa**.

4. Kontrol lera och uppdatera informationen för den nyligen tillagda kontakten.

    ![Skärm bild som visar en redigerings sida där du kan verifiera användar inställningarna.](./media/procoresso-tutorial/Procore_user_check.png)

5. Klicka på **Spara och skicka inbjudan** (om en inbjudan via e-post krävs) eller **Spara** (Spara direkt) för att slutföra användar registreringen.
    
    ![Skärm bild som visar de aktuella projekt inställningarna där du kan spara och skicka inbjudan.](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för grundläggande SSO i åtkomst panelen, bör du loggas in automatiskt på den grundläggande SSO-filen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)