---
title: 'Självstudie: Azure Active Directory integrering med Voyance | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Voyance.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/07/2019
ms.author: jeedes
ms.openlocfilehash: e51d275b32b634b7914b4e4f53959c1b89d96aed
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531731"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Självstudie: Azure Active Directory integrering med Voyance

I den här självstudien får du lära dig hur du integrerar Voyance med Azure Active Directory (Azure AD).
Genom att integrera Voyance med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Voyance.
* Du kan göra det möjligt för användarna att logga in automatiskt till Voyance (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Voyance behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Voyance-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Voyance stöder **SP** -och **IDP** -initierad SSO

* Voyance stöder **just-in-Time** User-etablering

## <a name="adding-voyance-from-the-gallery"></a>Lägga till Voyance från galleriet

Om du vill konfigurera integreringen av Voyance i Azure AD måste du lägga till Voyance från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Voyance från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **Voyance**, väljer **Voyance** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Voyance i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Voyance baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Voyance upprättas.

Om du vill konfigurera och testa enkel inloggning med Voyance i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Voyance-enkel inloggning](#configure-voyance-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Voyance test User](#create-voyance-test-user)** – om du vill ha en motsvarighet till Britta Simon i Voyance som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Voyance i Azure AD:

1. Välj **enkel inloggning**på sidan **Voyance** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Voyance-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<companyname>.nyansa.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<companyname>.nyansa.com/saml/create/`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Voyance-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyname>.nyansa.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Voyance client support team](mailto:support@nyansa.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Voyance** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-voyance-single-sign-on"></a>Konfigurera Voyance enkel inloggning

1. Logga in på din Voyance-klient som administratör i ett annat webbläsarfönster.

2. Gå till det övre högra hörnet i navigerings fältet och klicka på **profil**.
    
    ![Konfigurera enkel inloggning på App-sidan – University University](./media/voyance-tutorial/tutorial_voyance_001.png) 

3. Klicka på **Administratörs inställningar**.

    ![Konfigurera enkel inloggning på App-sidans administratörs inställningar](./media/voyance-tutorial/tutorial_voyance_002.png)

4. Klicka på fliken **användar åtkomst** .

    ![Konfigurera enkel inloggning på App-sidans användar åtkomst](./media/voyance-tutorial/tutorial_voyance_003.png)

5. Klicka på knappen **SSO är inaktive rad** för att konfigurera Azure AD som en IDP med SAML 2,0.

    ![Konfigurera enkel inloggning på App-sidan för enkel inloggning är inaktive rad](./media/voyance-tutorial/tutorial_voyance_004.png)

6. Gå till **SAML v2** -avsnittet och utför stegen nedan:

    ![Konfigurera enkel inloggning på App-sidan SAML v2](./media/voyance-tutorial/tutorial-voyance-005.png)
    
    a. Välj **Aktiverad**.
    
    b. Klistra in **inloggnings-URL**, som du har kopierat från Azure Portal till text rutan för **IDP-inloggnings-URL** .

    c. Öppna det hämtade base64-kodade certifikatet i anteckningar, kopiera innehållet i det till Urklipp och klistra in det i text rutan **IDP-cert** .
    
    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** brittasimon@yourcompanydomain.extension . Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Voyance.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Voyance**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Voyance**.

    ![Voyance-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-voyance-test-user"></a>Skapa Voyance test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Voyance. Voyance stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Voyance skapas en ny efter autentiseringen.

>[!NOTE]
>Om du behöver skapa en användare manuellt måste du kontakta [Voyance support team](maiLto:support@nyansa.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Voyance på åtkomst panelen, bör du loggas in automatiskt på den Voyance som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

