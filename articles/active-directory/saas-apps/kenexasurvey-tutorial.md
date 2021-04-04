---
title: 'Självstudie: Azure Active Directory integrering med IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IBM Kenexa Survey Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: faea5fdc7fdab11c630f24dbb8527e5bf198765c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459160"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Självstudie: Azure Active Directory integrering med IBM Kenexa Survey Enterprise

I den här självstudien får du lära dig att integrera IBM Kenexa Survey Enterprise med Azure Active Directory (Azure AD).
Att integrera IBM Kenexa Survey Enterprise med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till IBM Kenexa Survey Enterprise.
* Du kan göra det möjligt för användarna att logga in automatiskt till IBM Kenexa Survey Enterprise (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med IBM Kenexa Survey Enterprise behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IBM Kenexa Survey Enterprise enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* IBM Kenexa Survey Enterprise stöder **IDP** INITIERAd SSO

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Lägga till IBM Kenexa Survey Enterprise från galleriet

Om du vill konfigurera integreringen av IBM Kenexa Survey Enterprise i Azure AD måste du lägga till IBM Kenexa Survey Enterprise från galleriet till listan över hanterade SaaS-appar.

**Gör så här för att lägga till IBM Kenexa Survey Enterprise från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **IBM Kenexa Survey Enterprise**, väljer **IBM Kenexa Survey Enterprise** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![IBM Kenexa Survey Enterprise i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med IBM Kenexa Survey Enterprise baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i IBM Kenexa Survey Enterprise.

Om du vill konfigurera och testa enkel inloggning med Azure AD med IBM Kenexa Survey Enterprise måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IBM Kenexa Survey Enterprise enkel inloggning](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** – så här konfigurerar du de enskilda Sign-On inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa IBM Kenexa Survey Enterprise test User](#create-ibm-kenexa-survey-enterprise-test-user)** – om du vill ha en motsvarighet till Britta Simon i IBM Kenexa Survey Enterprise som är länkat till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med IBM Kenexa Survey Enterprise:

1. I [Azure Portal](https://portal.azure.com/)på sidan **IBM Kenexa Survey Enterprise** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![IBM Kenexa Survey Enterprise-domän och URL: er enkel inloggnings information](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://surveys.kenexa.com/<companycode>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [IBM Kenexa Survey Enterprise client support team](https://www.ibm.com/support/home/?lnk=fcw) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Den IBM Kenexa Survey Enterprise-appen förväntar sig att ta emot SAML-intyg (Security Assertion Markup Language) i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen av dina SAML-token-attribut. Värdet för användar-ID-anspråk i svaret måste matcha det SSO-ID som har kon figurer ATS i Kenexa-systemet. Om du vill mappa rätt användar identifierare i din organisation som SSO IDP (Internet Datagram Protocol) arbetar du med [IBM Kenexa Survey Enterprise Support Team](https://www.ibm.com/support/home/?lnk=fcw).

    Som standard anger Azure AD användar-ID: t som User Principal Name-värde (UPN). Du kan ändra det här värdet på fliken **användarattribut** , som du ser i följande skärm bild. Integrationen fungerar bara när du har slutfört mappningen på rätt sätt.

    ![image](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera IBM Kenexa Survey Enterprise** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurera IBM Kenexa Survey Enterprise Single Sign-On

Om du vill konfigurera enkel inloggning på **IBM Kenexa Survey Enterprise** sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [IBM Kenexa Survey Enterprise Support Team](https://www.ibm.com/support/home/?lnk=fcw). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till IBM Kenexa Survey Enterprise.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **IBM Kenexa Survey Enterprise**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **IBM Kenexa Survey Enterprise**.

    ![IBM Kenexa Survey Enterprise-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Skapa IBM Kenexa Survey Enterprise test User

I det här avsnittet skapar du en användare som heter Britta Simon i IBM Kenexa Survey Enterprise.

Om du vill skapa användare i IBM Kenexa Survey Enterprise system och mappa SSO-ID: t för dem kan du arbeta med [IBM Kenexa Survey Enterprise Support Team](https://www.ibm.com/support/home/?lnk=fcw). Detta SSO-ID-värde ska också mappas till värdet för användar-ID från Azure AD. Du kan ändra den här standardinställningen på fliken **attribut** .

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen IBM Kenexa Survey Enterprise på åtkomst panelen, bör du loggas in automatiskt till IBM Kenexa Survey Enterprise som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)