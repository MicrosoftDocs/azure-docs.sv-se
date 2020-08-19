---
title: 'Självstudie: Azure Active Directory integration med SAML-anslutning för virtuell ON24-miljö | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ON24 virtuell miljö SAML-anslutning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 3977aee84b1281e7918987c63185069892b5909e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554287"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Självstudie: Azure Active Directory integration med SAML-anslutning för virtuell ON24-miljö

I den här självstudien får du lära dig hur du integrerar ON24 virtuella miljöer med SAML-anslutning med Azure Active Directory (Azure AD).
Genom att integrera ON24 virtuell miljö SAML-anslutning med Azure AD får du följande fördelar:

* Du kan kontrol lera i Azure AD vem som har åtkomst till ON24 virtuella miljö SAML-anslutning.
* Du kan göra det möjligt för användarna att logga in automatiskt till ON24 för virtuell miljö (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ON24 virtuell miljö SAML-anslutning behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ON24 virtuell miljö SAML-anslutning enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ON24 virtuell miljö med SAML-anslutning stöder **SP** -och **IDP** -initierad SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Lägger till en ON24 virtuell miljö SAML-anslutning från galleriet

Om du vill konfigurera en integrering av ON24 virtuella miljö SAML-anslutning till Azure AD måste du lägga till ON24 Virtual Environment SAML-anslutning från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till en ON24 virtuell miljö SAML-anslutning från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **ON24 virtuell miljö SAML-anslutning**, väljer **ON24 virtuell miljö SAML-anslutning** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![ON24 virtuell miljö, SAML-anslutning i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med ON24-SAML-anslutning för virtuell miljö baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i ON24 virtuell miljö SAML-anslutning upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD med ON24 virtuell miljö SAML-anslutning måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ON24 virtuell miljö SAML-anslutning enkel inloggning](#configure-on24-virtual-environment-saml-connection-single-sign-on)** – konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa test användare av SAML-ON24 för virtuell miljö](#create-on24-virtual-environment-saml-connection-test-user)** för att få en motsvarighet till Britta Simon i ON24 virtuell MILJÖs SAML-anslutning som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med ON24 virtuell miljö SAML-anslutning:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **ON24 virtuell miljö SAML-anslutning** program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![ON24 virtuell miljö, SAML-anslutnings domän och URL-information för enkel inloggning](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL: 

     **URL för produktions miljö**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL till kvalitets miljö**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Skriv en URL i textrutan **Svars-URL**: 

     **URL för produktions miljö**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL till kvalitets miljö**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klicka på **Ange ytterligare URL:er**. 

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:

    ![ON24 virtuell miljö, SAML-anslutnings domän och URL-information för enkel inloggning](common/both-signonurl.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med det faktiska relä läget och inloggnings-URL: en. Kontakta ON24 för att få de här värdena i den [virtuella miljön SAML-anslutning](https://www.on24.com/contact-us/) . Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera ON24 virtuell miljö SAML-anslutning** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Konfigurera ON24 virtuell miljö SAML-anslutning enkel inloggning

Om du vill konfigurera enkel inloggning på **ON24 virtuell miljö SAML-nätverksanslutning** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [ON24 Virtual Environment SAML Connection support team](https://www.on24.com/about-us/support/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ON24 virtuella miljö SAML-anslutning.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **ON24 virtuell miljö SAML-anslutning**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **ON24 virtuell miljö SAML-anslutning**.

    ![Den virtuella ON24-miljön för SAML-anslutningar i listan program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Skapa test användare för SAML-anslutning för virtuell ON24-miljö

I det här avsnittet skapar du en användare med namnet Britta Simon i ON24 Virtual Environment SAML-anslutning. Arbeta med [ON24 Virtual Environment SAML Connection support team](https://www.on24.com/about-us/support/) för att lägga till användarna i den virtuella ON24-plattformen för SAML-anslutningar. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML-anslutningen ON24 virtuell miljö på åtkomst panelen, bör du loggas in automatiskt på den ON24 virtuella miljön SAML-anslutning för vilken du konfigurerar SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

