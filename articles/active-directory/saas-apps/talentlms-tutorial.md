---
title: 'Självstudie: Azure Active Directory integrering med TalentLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 57445d1010e9775e3a1735fd64722c44db8e0a4e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546611"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Självstudie: Azure Active Directory integrering med TalentLMS

I den här självstudien får du lära dig hur du integrerar TalentLMS med Azure Active Directory (Azure AD).
Genom att integrera TalentLMS med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TalentLMS.
* Du kan göra det möjligt för användarna att logga in automatiskt till TalentLMS (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TalentLMS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* TalentLMS-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TalentLMS stöder **SP** -INITIERAd SSO

## <a name="adding-talentlms-from-the-gallery"></a>Lägga till TalentLMS från galleriet

Om du vill konfigurera integreringen av TalentLMS i Azure AD måste du lägga till TalentLMS från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till TalentLMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **TalentLMS**, väljer **TalentLMS** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![TalentLMS i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TalentLMS baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i TalentLMS upprättas.

Om du vill konfigurera och testa enkel inloggning med TalentLMS i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TalentLMS-enkel inloggning](#configure-talentlms-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TalentLMS test User](#create-talentlms-test-user)** – om du vill ha en motsvarighet till Britta Simon i TalentLMS som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TalentLMS i Azure AD:

1. Välj **enkel inloggning**på sidan **TalentLMS** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TalentLMS-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.TalentLMSapp.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [TalentLMS client support team](https://www.talentlms.com/contact) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera TalentLMS** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-talentlms-single-sign-on"></a>Konfigurera TalentLMS enkel inloggning

1. Logga in på din TalentLMS-företags webbplats som administratör i ett annat webbläsarfönster.

1. I avsnittet **konto & inställningar** klickar du på fliken **användare** .

    ![Konto & inställningar](./media/talentlms-tutorial/IC777296.png "Konto & inställningar")

1. Klicka på **enkel inloggning (SSO)**,

1. Gör följande i avsnittet Enkel inloggning:

    ![Enkel inloggning](./media/talentlms-tutorial/IC777297.png "för Aha!")

    a. Välj **SAML 2,0**i listan **typ av SSO-integration** .

    b. I text rutan **identitets leverantör (IdP)** klistrar du in värdet för **Azure AD-identifieraren**, som du har kopierat från Azure Portal.

    c. Klistra in **tumavtryck** -värdet från Azure Portal i text rutan **finger avtryck för certifikat** .

    d.  I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    e. I text rutan **fjärrinloggnings-URL** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    f. Fyll i följande:

    * I text rutan **TargetedID** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * I text rutan **förnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * I text rutan **efter namn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * I text rutan **e-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klicka på **Spara**.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TalentLMS.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **TalentLMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TalentLMS**.

    ![TalentLMS-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-talentlms-test-user"></a>Skapa TalentLMS test användare

Om du vill att Azure AD-användare ska kunna logga in på TalentLMS måste de tillhandahållas i TalentLMS. När det gäller TalentLMS är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **TalentLMS** -klient.

1. Klicka på **användare**och sedan på **Lägg till användare**.

1. Utför följande steg på dialog sidan **Lägg till användare** :

    ![Lägg till användare](./media/talentlms-tutorial/IC777299.png "Lägg till användare")  

    a. I text rutan för det **första namnet** anger du det första namnet på användaren som **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.
 
    c. I textrutan **E-postadress** skriver du e-postadressen för användaren: `brittasimon\@contoso.com`.

    d. Klicka på **Lägg till användare**.

> [!NOTE]
> Du kan använda andra verktyg för TalentLMS av användar konton eller API: er som tillhandahålls av TalentLMS för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen TalentLMS på åtkomst panelen, bör du loggas in automatiskt på den TalentLMS som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

