---
title: 'Självstudie: Azure Active Directory integrering med sprinkler | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och sprinkler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 02b9adcb72b4d26a19b6514d19ce94a70a2415ed
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534225"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Självstudie: Azure Active Directory integrering med sprinkler

I den här självstudien får du lära dig hur du integrerar sprinkler med Azure Active Directory (Azure AD).
Att integrera sprinkler med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till sprinkler.
* Du kan göra det möjligt för användarna att logga in automatiskt till sprinkler (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med sprinkler behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Aktive rad prenumeration för enkel inloggning med sprinkler

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Sprinkler stöder **SP** -INITIERAd SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Lägga till sprinkler från galleriet

Om du vill konfigurera integrering av sprinkler i Azure AD måste du lägga till en sprinkler från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till en sprinkler från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **sprinkler**, väljer **sprinkler** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Sprinkler i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med sprinkler baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i sprinklern upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med sprinkler måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med sprinkler](#configure-sprinklr-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa användare av sprinkler-test](#create-sprinklr-test-user)** – för att få en motsvarighet till Britta Simon i sprinkler som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med sprinkler:

1. På sidan [Azure Portal](https://portal.azure.com/)på sidan för program integrering i **sprinkler** väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för sprinkler-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [sprinklers klient support grupp](https://www.sprinklr.com/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera sprinkler** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurera enkel inloggning för sprinkler

1. Logga in på din plats för sprinkler företaget som administratör i ett annat webbläsarfönster.

1. Gå till **administrations \> Inställningar**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **hantera \> enkel inloggning för partner** i det vänstra fönstret.

    ![Hantera partner](./media/sprinklr-tutorial/ic782908.png "Hantera partner")

1. Klicka på **+ Lägg till enkla inloggningar**.

    ![Enkel inloggning](./media/sprinklr-tutorial/ic782909.png "Enkel inloggning")

1. Utför följande steg på sidan **enkel inloggning** :

    ![Enkel inloggning](./media/sprinklr-tutorial/ic782910.png "Enkel inloggning")

    a. I text rutan **namn** anger du ett namn för din konfiguration (till exempel: *WAADSSOTest*).

    b. Välj **Aktiverad**.

    c. Välj **Använd nytt SSO-certifikat**.

    d. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan för **identitets leverantörs certifikat** .

    e. Klistra in värdet för **Azure AD-identifieraren** som du har kopierat från Azure Portal till text rutan **entitets-ID** .

    f. Klistra in **URL** -värdet för inloggning som du har kopierat från Azure Portal till text rutan för **inloggnings-URL för identitets leverantör** .

    ex. Klistra in **URL** -värdet för utloggning som du har kopierat från Azure Portal till text rutan för **inloggade URL för identitets leverantör** .

    h. Som **SAML-användarens ID-typ**väljer du **Assertion innehåller användarens Sprinklr.com användar namn**.

    i. Som **SAML-användar-ID-plats**väljer du **användar-ID finns i elementets namn identifierare i ämnes instruktionen**.

    j. Klicka på **Spara**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till sprinkler.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **sprinkler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **sprinkler**.

    ![Sprinkler-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sprinklr-test-user"></a>Skapa användare av sprinkler-test

1. Logga in på din plats för sprinkler företaget som administratör.

1. Gå till **administrations \> Inställningar**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Gå till **Hantera klient \> användare** i det vänstra fönstret.

    ![Inställningar](./media/sprinklr-tutorial/ic782914.png "Inställningar")

1. Klicka på **Lägg till användare**.

    ![Inställningar](./media/sprinklr-tutorial/ic782915.png "Inställningar")

1. I dialog rutan **Redigera användare** utför du följande steg:

    ![Redigera användare](./media/sprinklr-tutorial/ic782916.png "Redigera användare")

    a. I text rutorna **e-post**, **förnamn** och **efter namn** anger du informationen för ett Azure AD-användarkonto som du vill etablera.

    b. Välj **lösen ord inaktiverat**.

    c. Välj **språk**.

    d. Välj **användar typ**.

    e. Klicka på **Uppdatera**.

    > [!IMPORTANT]
    > Det **inaktiverade lösen ordet** måste väljas för att en användare ska kunna logga in via en identitets leverantör. 

1. Gå till **roll**och utför sedan följande steg:

    ![Partner roller](./media/sprinklr-tutorial/ic782917.png "Partner roller")

    a. Välj **ALL_Permissions**i listan **Global** .  

    b. Klicka på **Uppdatera**.

> [!NOTE]
> Du kan använda andra verktyg eller API: er för skapande av sprinkler-användare som tillhandahålls av sprinkler för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen sprinkler på åtkomst panelen, bör du loggas in automatiskt på den sprinkler som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
