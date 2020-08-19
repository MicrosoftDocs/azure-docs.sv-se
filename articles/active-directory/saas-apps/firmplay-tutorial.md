---
title: 'Självstudie: Azure Active Directory integration med FirmPlay-anställda rådgivnings för rekrytering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FirmPlay-anställda rådgivnings för rekrytering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 522de4f3e9d82be91290fef7ea900998cb4d1d8f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555055"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Självstudie: Azure Active Directory integration med FirmPlay-anställda rådgivnings för rekrytering

I den här självstudien får du lära dig att integrera FirmPlay-anställdas rådgivnings för rekrytering med Azure Active Directory (Azure AD).
Att integrera FirmPlay – anställda rådgivnings för rekrytering med Azure AD ger följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till FirmPlay-anställda rådgivnings för rekrytering.
* Du kan göra det möjligt för användarna att logga in automatiskt till FirmPlay-anställda rådgivnings för rekrytering (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med FirmPlay-anställda rådgivnings för rekrytering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* FirmPlay – medarbetares rådgivnings för rekrytering av enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FirmPlay – anställnings rådgivnings för rekrytering stöder **SP** -INITIERAd SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Lägga till FirmPlay – anställda rådgivnings för rekrytering från galleriet

Om du vill konfigurera integrationen av FirmPlay-Employee rådgivnings för rekrytering till Azure AD måste du lägga till FirmPlay-anställda rådgivnings för rekrytering från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till FirmPlay-anställdas rådgivnings för rekrytering från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **FirmPlay-personal rådgivnings för rekrytering**, väljer **FirmPlay – anställda rådgivnings för rekrytering** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![FirmPlay – medarbetarnas rådgivnings för rekrytering i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med FirmPlay-anställda rådgivnings för rekrytering baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i FirmPlay-anställda rådgivnings för rekrytering upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med FirmPlay-anställda rådgivnings för rekrytering måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera FirmPlay – anställda rådgivnings för rekrytering av enkel inloggning](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** för att konfigurera inställningar för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa FirmPlay – anställda rådgivnings för rekrytering av test användare](#create-firmplay---employee-advocacy-for-recruiting-test-user)** – om du vill ha en motsvarighet till Britta Simon i FirmPlay-Employee rådgivnings för rekrytering som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med FirmPlay-anställda rådgivnings för rekrytering:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **FirmPlay-anställda rådgivnings för rekrytering av** program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![FirmPlay – anställda rådgivnings för rekrytering av domän och URL-information för enkel inloggning](common/sp-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [FirmPlay – anställda rådgivnings för rekrytering av kund support teamet](mailto:engineering@firmplay.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera FirmPlay – anställda rådgivnings for rekrytering** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurera FirmPlay – anställda rådgivnings för rekrytering av enkel inloggning

Om du vill konfigurera enkel inloggning på **FirmPlay-anställda rådgivnings för rekryterings** sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [FirmPlay-anställda rådgivnings för rekryterings support teamet](mailto:engineering@firmplay.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till FirmPlay personal rådgivnings för rekrytering.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **FirmPlay-anställda rådgivnings för rekrytering**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **FirmPlay – personal rådgivnings för rekrytering**.

    ![FirmPlay – personal rådgivnings för rekryterings länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Skapa FirmPlay – anställda rådgivnings för rekrytering av test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i FirmPlay-Employee rådgivnings för rekrytering. Arbeta med [FirmPlay-anställda rådgivnings för rekryterings support teamet](mailto:engineering@firmplay.com) för att lägga till användarna i FirmPlay-anställda rådgivnings för rekryterings plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen FirmPlay – personal rådgivnings för rekrytering på åtkomst panelen, bör du loggas in automatiskt till FirmPlay-anställda rådgivnings för rekrytering som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

