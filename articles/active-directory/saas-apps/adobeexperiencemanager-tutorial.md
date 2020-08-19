---
title: 'Självstudie: Azure Active Directory integration med Adobe Experience Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 1088827ead320007c758d8e843a15328f108c035
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543031"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Självstudie: Azure Active Directory integration med Adobe Experience Manager

I den här självstudien får du lära dig hur du integrerar Adobe Experience Manager med Azure Active Directory (AD Azure).
Integreringen av Adobe Experience Manager med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Adobe Experience Manager från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Adobe Experience Manager (enkel inloggning) med sina Azure Active Directory-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Adobe Experience Manager behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Adobe Experience Manager-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Adobe Experience Manager stöder **SP- och IDP**-initierad enkel inloggning

* Adobe Experience Manager stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Lägga till Adobe Experience Manager från galleriet

För att konfigurera integreringen av Adobe Experience Manager i Azure AD måste du lägga till Adobe Experience Manager från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Adobe Experience Manager från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Adobe Experience Manager**, väljer **Adobe Experience Manager** från resultatpanelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Adobe Experience Manager i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Adobe Experience Manager-testanvändare](#create-adobe-experience-manager-test-user)** – för att skapa en motsvarighet till Britta Simon i Adobe Experience Manager som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. Välj **Enkel inloggning** på sidan för integrering av **Adobe Experience Manager**-programmet på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med Adobe Experience Manager-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du ett unikt värde som du även definierar på AEM-servern.

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Uppdatera värdet för Svars-URL med den faktiska svars-URL:en. Kontakta [Adobe Experience Manager-kundsupporten](https://helpx.adobe.com/support/experience-manager.html) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Adobe Experience Manager-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du URLe:n för Adobe Experience Manager-servern.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Adobe Experience Manager** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurera enkel inloggning för Adobe Experience Manager

1. Öppna ett nytt webbläsarfönster och öppna **Adobe Experience Manager**-administrationsportalen.

2. Välj **Inställningar**  >  **säkerhets**  >  **användare**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Välj **Administratör** eller en annan relevant användare.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Välj **konto inställningar**  >  **Hantera TrustStore**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Klicka på **Välj certifikatsfil** under **Lägg till certifikat från CER-fil**. Bläddra till och välj certifikatfilen, som du redan har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Certifikatet läggs till i TrustStore. Observera certifikatets alias.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Välj **authentication-service** på sidan **Användare**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Välj **konto inställningar**  >  **skapa/hantera nyckel lagring**. Skapa KeyStore genom att ange ett lösenord.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Gå tillbaka till administrationsskärmen. Välj sedan **Inställningar**  >  **Åtgärds**  >  **webb konsol**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Konfigurationssidan öppnas.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Leta upp **Adobe Granite SAML 2.0 Authentication Handler**. Välj sedan ikonen **Lägg till**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Utför följande åtgärder på den här sidan.

    ![Konfigurera enkel inloggning – knappen Spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Ange **/** i rutan **Sökväg**.

    b. I rutan **IDP URL** (IDP-URL) anger du värdet för **Inloggnings-URL** som du kopierade från Azure-portalen.

    c. I rutan **Certifikatalias för IDP**  anger du värdet för **Certifikatalias** som du lade till i TrustStore.

    d. I rutan **Säkerhetstilldelat entitets-ID** anger du det unika värdet för **Azure Ad-identifieraren** som du konfigurerade på Azure-portalen.

    e. I rutan **URL för konsumenttjänst för försäkran** anger du värdet för **svars-URL:en** som du konfigurerade på Azure-portalen.

    f. I rutan för **lösenord för nyckelarkiv** anger du **lösenordet** som du angav i KeyStore.

    ex. I rutan **ID för användarattribut** anger du **namn-ID:t** eller ett annat användar-ID som är relevant i ditt fall.

    h. Välj **Skapa CRX-användare automatiskt**.

    i. I rutan **Utloggnings-URL** anger du det unika värdet för **utloggnings-URL:en** som du hämtade från Azure-portalen.

    j. Välj **Spara**.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till Adobe Experience Manager.

1. På Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Adobe Experience Manager**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Adobe Experience Manager**.

    ![Adobe Experience Manager-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-adobe-experience-manager-test-user"></a>Skapa Adobe Experience Manager-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Adobe Experience Manager. Om du valde alternativet **Skapa CRX-användare automatiskt** skapas användare automatiskt efter en lyckad autentisering.

Om du vill skapa användare manuellt arbetar du med [support teamet för Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html)   för att lägga till användarna i Adobe Experience Manager-plattformen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Adobe Experience Manager på åtkomstpanelen bör du loggas in automatiskt i Adobe Experience Manager-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
