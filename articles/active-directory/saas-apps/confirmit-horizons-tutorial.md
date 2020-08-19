---
title: 'Självstudie: Azure Active Directory integrering med Confirmit-horisonter | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Confirmit-horisonter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 0ff197a5603480e9143a14a4760f14dccf8b101b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544541"
---
# <a name="tutorial-azure-active-directory-integration-with-confirmit-horizons"></a>Självstudie: Azure Active Directory integrering med Confirmit-horisonter

I den här självstudien får du lära dig att integrera Confirmit-horisonter med Azure Active Directory (Azure AD).
Genom att integrera Confirmit-horisonter med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Confirmit-horisonter.
* Du kan låta dina användare bli automatiskt inloggade för att Confirmit horisonter (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Confirmit-horisonter behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Confirmit, enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Confirmit-horisonter stöder **SP-och IDP** -INITIERAd SSO

* Confirmit-horisonter har stöd **för just vid tidpunkten för** användar etablering

## <a name="adding-confirmit-horizons-from-the-gallery"></a>Lägga till Confirmit-horisonter från galleriet

Om du vill konfigurera integreringen av Confirmit-horisonter i Azure AD måste du lägga till Confirmit-horisonter från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Confirmit-horisonter från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Confirmit-horisonter**, väljer **Confirmit-horisonter** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Confirmit-horisonter i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Confirmit-horisonter baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Confirmit-horisonter upprättas.

Om du vill konfigurera och testa enkel inloggning med Confirmit-horisonter i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Confirmit-horisonter enkel inloggning](#configure-confirmit-horizons-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Confirmit-horisonter testa användare](#create-confirmit-horizons-test-user)** – om du vill ha en motsvarighet till Britta Simon i Confirmit-horisonter som är länkade till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Confirmit-horisonter i Azure AD:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Confirmit horisonter** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Confirmit-horisont, domän och URL-information för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.com.au/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.ca/identity/AuthServices/<UNIQUEID>/acs
    https://<SUBDOMAIN>.confirmit.hk/identity/AuthServices/<UNIQUEID>/acs
    https://sso.us.confirmit.com/<UNIQUEID>/saml/acs
    ```

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Confirmit-horisont, domän och URL-information för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    ```http
    https://<SUBDOMAIN>.confirmit.com/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.com.au/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.ca/identity/<UNIQUEID>
    https://<SUBDOMAIN>.confirmit.hk/identity/<UNIQUEID>
    https://sso.us.confirmit.com/<UNIQUEID>
    ```

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Confirmits horisonter support team](mailto:support@confirmit.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-confirmit-horizons-single-sign-on"></a>Konfigurera enkel inloggning för Confirmit-Horisont

Om du vill konfigurera enkel inloggning på **Confirmit-horisonter** måste du skicka **URL: en för appens Federation Metadata** till [support teamet för Confirmit-horisonter](mailto:support@confirmit.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Confirmit-horisonter.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Confirmit-horisonter**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Confirmit-horisonter**.

    ![Länken Confirmit-horisonter i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-confirmit-horizons-test-user"></a>Skapa Confirmit-horisonter test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Confirmit-horisonter. Confirmit-horisonter har stöd för just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Confirmit-horisonter skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Confirmit-horisonter på åtkomst panelen, bör du loggas in automatiskt på de Confirmit-horisonter som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

