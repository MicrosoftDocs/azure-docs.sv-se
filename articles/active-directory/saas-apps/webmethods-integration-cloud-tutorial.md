---
title: 'Självstudie: Azure Active Directory integration med webMethods integration Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och webMethods integration Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 1a7931f524d7f6cb1f98a582bed65c4dd06f9c58
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523907"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Självstudie: Azure Active Directory integration med webMethods integration Suite

I den här självstudien får du lära dig hur du integrerar webMethods integration Suite med Azure Active Directory (Azure AD).
Integrering av webMethods integration Suite med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har till gång till integrerings paket för webMethods.
* Du kan göra det möjligt för användarna att vara automatiskt inloggade till webMethods integration Suite (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med webMethods integration Suite behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* webMethods aktive rad prenumeration för enkel inloggning i integrerings paket

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* webMethods integration Suite stöder **SP** -och **IDP** -initierad SSO

* webMethods integration Suite stöder **just-in-Time-** etablering av användare

## <a name="adding-webmethods-integration-suite-from-the-gallery"></a>Lägga till webMethods integration Suite från galleriet

Om du vill konfigurera integreringen av webMethods integration Suite i Azure AD måste du lägga till webMethods integration Suite från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till webMethods integration Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **webMethods integration Suite**, väljer **webMethods integration Suite** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![webMethods integration Suite i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med webMethods integration Suite baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i webMethods integration Suite.

Om du vill konfigurera och testa enkel inloggning i Azure AD med webMethods integration Suite måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera webMethods enkel inloggning för integrerings paket](#configure-webmethods-integration-suite-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa webb metoden integration Suite-test användare](#create-webmethods-integration-suite-test-user)** – om du vill ha en motsvarighet till Britta Simon i webMethods integration Suite som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med webMethods integration Suite:

1. I [Azure Portal](https://portal.azure.com/)väljer du **enkel inloggning**på sidan **WebMethod integration Suite** -program integration.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera **integrerings molnet för webbmetoder**går du till avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![webbmetoder integration Suite-domän och enkel inloggnings information för URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    - `<SUBDOMAIN>.webmethodscloud.com`
    - `<SUBDOMAIN>.webmethodscloud.eu`
    - `<SUBDOMAIN>.webmethodscloud.de`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:


    - `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`
    - `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![webbmetoder integration Suite-domän och enkel inloggnings information för URL: er](common/metadata-upload-additional-signon.png)

    d. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    - `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`
    - `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [webbmetoder integration Suite-klientens support team](https://empower.softwareag.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Gör så här om du vill konfigurera **webbmetods-API-molnet**i avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge:

    ![webbmetoder integration Suite-domän och enkel inloggnings information för URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    - `<SUBDOMAIN>.webmethodscloud.com`
    - `<SUBDOMAIN>.webmethodscloud.eu`
    - `<SUBDOMAIN>.webmethodscloud.de`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`
    - `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`

    c. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![webbmetoder integration Suite-domän och enkel inloggnings information för URL: er](common/metadata-upload-additional-signon.png)

    d. I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    - `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`
    - `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`
    - `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [webbmetoder integration Suite-klientens support team](https://empower.softwareag.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera webMethods integration Suite** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-webmethods-integration-suite-single-sign-on"></a>Konfigurera enkel inloggning för webMethods integration Suite

Om du vill konfigurera enkel inloggning på **webMethods** -sidan för integration Suite måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [webMethods support team för integration Suite](https://empower.softwareag.com/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till webMethods integration Suite.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **webMethods integration Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **webMethods integration Suite**.

    ![Länken webMethods integration Suite i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-webmethods-integration-suite-test-user"></a>Skapa webb metods test användare för integration Suite

I det här avsnittet skapas en användare som kallas Britta Simon i webMethods integration Suite. webMethods integration Suite stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i webMethods integration Suite skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen webMethods integration Suite på åtkomst panelen, bör du loggas in automatiskt på den webMethods integration Suite som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

